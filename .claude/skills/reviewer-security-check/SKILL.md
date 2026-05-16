---
name: reviewer-security-check
description: >
  Teaches the Reviewer how to check for secrets, auth issues, injection
  vulnerabilities, unsafe file operations, insecure API behavior, and permission
  problems. Any finding is automatically REQUIRED severity.
---

# Skill: reviewer-security-check

## Purpose

Find security vulnerabilities before they reach production.
Any security finding is automatically a REQUIRED fix — no exceptions.

## When to Use

As part of the review process. Always run this check, even for small changes.

## Input

- Changed files (full content)
- Git diff
- `.claude/work/IMPLEMENTATION_CONTRACT.md`

## Output

Security findings in `.claude/work/REVIEW_REPORT.md`

---

## Security Checks

### 1. Hardcoded Secrets

Look for:
- API keys, tokens, passwords in source code
- Base64-encoded strings that might encode credentials
- Connection strings with embedded credentials
- Private keys or certificates

```bash
grep -rn "password\s*=\s*['\"]" src/
grep -rn "secret\s*=\s*['\"]" src/
grep -rn "api_key\s*=\s*['\"]" src/
grep -rn "Bearer " src/
```

Any hardcoded secret is an automatic FAIL.

### 2. SQL Injection

Look for raw string concatenation in SQL queries:

```python
# Dangerous
query = "SELECT * FROM users WHERE id = " + user_id

# Safe
query = "SELECT * FROM users WHERE id = $1", [user_id]
```

```javascript
// Dangerous
db.query(`SELECT * FROM users WHERE id = ${req.params.id}`)

// Safe
db.query('SELECT * FROM users WHERE id = $1', [req.params.id])
```

Any unparameterized query using user input is an automatic FAIL.

### 3. Command Injection

Look for shell execution with user-controlled input:

```python
# Dangerous
subprocess.run(f"git clone {user_url}", shell=True)
os.system(f"convert {filename}")

# Safe
subprocess.run(["git", "clone", user_url])
```

Any `shell=True` or equivalent with user input is an automatic FAIL.

### 4. Cross-Site Scripting (XSS)

Look for user input rendered directly in HTML without escaping:

```javascript
// Dangerous
element.innerHTML = userInput
document.write(userInput)

// Safe
element.textContent = userInput
```

Any unescaped user input in HTML context is an automatic FAIL.

### 5. Path Traversal

Look for user-controlled file paths used in file operations:

```python
# Dangerous
open(f"/uploads/{user_filename}")

# Safe — validate and sanitize filename first
safe_name = os.path.basename(user_filename)
if '..' in safe_name or safe_name.startswith('/'):
    raise ValueError("Invalid filename")
open(f"/uploads/{safe_name}")
```

### 6. Authentication and Authorization

For every new or changed endpoint:
- Is authentication required? Is it enforced?
- Is authorization checked (not just authentication)?
- Can a user access another user's data by guessing an ID?
- Are admin-only routes protected from regular users?

### 7. Sensitive Data in Logs

Look for passwords, tokens, or PII being logged:

```javascript
// Dangerous
console.log('User logged in:', { email, password })

// Safe
console.log('User logged in:', { email })
```

### 8. Insecure Direct Object References (IDOR)

Look for endpoints that accept a resource ID from the request without verifying
the requester owns or has permission to access that resource:

```javascript
// Dangerous — user can change :id to access any user's data
app.get('/api/users/:id/orders', async (req, res) => {
  const orders = await db.getOrders(req.params.id) // no ownership check
})

// Safe
app.get('/api/users/:id/orders', async (req, res) => {
  if (req.user.id !== req.params.id && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' })
  }
  ...
})
```

---

## Output Format in REVIEW_REPORT.md

```markdown
## Security Review

### Hardcoded secrets: NONE FOUND / FOUND
<detail if found — file, line, type of secret>

### SQL injection: NONE FOUND / FOUND
<detail if found>

### Command injection: NONE FOUND / FOUND
<detail if found>

### XSS: NONE FOUND / FOUND
<detail if found>

### Path traversal: NONE FOUND / FOUND
<detail if found>

### Auth/authorization: ADEQUATE / ISSUES FOUND
<detail if issues found>

### Sensitive data in logs: NONE FOUND / FOUND
<detail if found>

### IDOR: NONE FOUND / FOUND
<detail if found>

### Security verdict: PASS / FAIL
```

---

## Quality Checklist

- [ ] Every check was run, not just the ones that seemed likely
- [ ] Grep searches were used to find patterns beyond the diff
- [ ] Each finding includes exact file path and line number
- [ ] Any finding is classified as REQUIRED (security findings are never RECOMMENDED)

## Safety Rules

- Never downgrade a security finding to RECOMMENDED.
- Never approve a PR with a known security vulnerability.
- If unsure whether something is a vulnerability, classify it as REQUIRED and explain the concern.