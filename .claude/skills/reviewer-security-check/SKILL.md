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
Any security finding is a REQUIRED fix. No exceptions. No RECOMMENDED severity for security issues.

## When to Use

On every review. Always. Even for "small" changes. Security issues hide in small changes.

---

## Check 1 — Hardcoded Secrets

Look for API keys, tokens, passwords, private keys in source code.

```bash
# Passwords and secrets
grep -rn --include="*.ts" --include="*.js" --include="*.py" --include="*.go" \
  -E "(password|secret|passwd|pwd)\s*[=:]\s*['\"][^'\"]" src/

# API keys and tokens  
grep -rn --include="*.ts" --include="*.js" --include="*.py" \
  -E "(api[_-]?key|apikey|access[_-]?token|auth[_-]?token)\s*[=:]\s*['\"]" src/

# Bearer tokens hardcoded
grep -rn --include="*.ts" --include="*.js" -E "Bearer [A-Za-z0-9+/]{20,}" src/

# Private keys
grep -rn "BEGIN (RSA |EC |OPENSSH )?PRIVATE KEY" src/

# Base64 that might be credentials (long base64 strings assigned to variables)
grep -rn -E "['\"][A-Za-z0-9+/]{40,}={0,2}['\"]" src/

# Connection strings with credentials
grep -rn -E "(mongodb|postgres|mysql|redis)://[^:]+:[^@]+@" src/
grep -rn -E "Server=.*;Password=" src/
```

**Any hardcoded secret → automatic FAIL.**

### Patterns that look innocent but are not:
- Config files with real credentials checked in
- Test fixtures with real-looking API keys
- "Example" config files with actual tokens

---

## Check 2 — SQL Injection

Look for raw string concatenation or interpolation in database queries.

```bash
# Template literal SQL (JavaScript/TypeScript)
grep -rn --include="*.ts" --include="*.js" \
  -E "query\s*\(\s*\`[^']*\$\{" src/

# String concatenation in SQL (JavaScript/TypeScript)
grep -rn --include="*.ts" --include="*.js" \
  -E "(SELECT|INSERT|UPDATE|DELETE|WHERE).*\+.*req\." src/

# f-string SQL (Python)
grep -rn --include="*.py" \
  -E "execute\s*\(\s*f['\"]" src/

# Python % formatting in SQL
grep -rn --include="*.py" \
  -E "execute\s*\(\s*['\"].*%\s*(" src/

# Go Sprintf SQL
grep -rn --include="*.go" \
  -E "Sprintf.*SELECT|Sprintf.*INSERT|Sprintf.*WHERE" .
```

**Safe patterns to confirm:**
- JS/TS: `db.query('SELECT * FROM t WHERE id = $1', [id])`
- Python: `cursor.execute('SELECT * FROM t WHERE id = %s', (id,))`
- Go: `db.Query("SELECT * FROM t WHERE id = $1", id)`
- ORM: Prisma, SQLAlchemy, GORM all parameterize automatically

---

## Check 3 — Command Injection

Look for shell execution with user-controlled input.

```bash
# Python shell=True
grep -rn --include="*.py" "shell=True" .

# Python os.system with variables
grep -rn --include="*.py" -E "os\.system\s*\(.*(\+|f['\"]|format)" .

# Python subprocess with user input in string
grep -rn --include="*.py" -E "subprocess\.(run|call|Popen)\s*\(\s*f['\"]" .

# Node child_process exec with user input
grep -rn --include="*.ts" --include="*.js" \
  -E "exec\s*\(\s*['\`][^']*\$\{" .

# Node child_process exec with concatenation
grep -rn --include="*.ts" --include="*.js" \
  -E "child_process.*exec\s*\(.*\+" .
```

**Safe patterns:**
- Always use argument arrays: `subprocess.run(["git", "clone", user_url])`
- In Node.js: `execFile()` or `spawn()` with array args, never `exec()` with string interpolation

---

## Check 4 — Cross-Site Scripting (XSS)

Look for user input rendered in HTML without escaping.

```bash
# innerHTML assignment
grep -rn --include="*.ts" --include="*.js" --include="*.tsx" --include="*.jsx" \
  "innerHTML\s*=" src/

# document.write
grep -rn --include="*.ts" --include="*.js" --include="*.tsx" --include="*.jsx" \
  "document\.write(" src/

# React dangerouslySetInnerHTML
grep -rn --include="*.tsx" --include="*.jsx" --include="*.ts" --include="*.js" \
  "dangerouslySetInnerHTML" src/

# Vue v-html directive
grep -rn --include="*.vue" "v-html" src/

# Template engines without escaping
grep -rn --include="*.ejs" --include="*.hbs" --include="*.pug" \
  -E "<%=|{{{|\!\{" .
```

**XSS in server-rendered HTML — Python:**
```bash
grep -rn --include="*.py" -E "Markup\(|mark_safe\(|escape=False" .
```

**Safe patterns:**
- React: Use JSX expressions `{userInput}` (auto-escaped), not `dangerouslySetInnerHTML`
- Node/Express: Use template engines with auto-escaping; never mark content as safe
- Always use `element.textContent` not `element.innerHTML`

---

## Check 5 — Path Traversal

Look for user-controlled values used in file system operations.

```bash
# Node.js file operations with user input
grep -rn --include="*.ts" --include="*.js" \
  -E "(readFile|writeFile|createReadStream|existsSync)\s*\(.*req\." src/

# Python file operations
grep -rn --include="*.py" \
  -E "open\s*\(.*request\." src/

# Path.join with user input (still unsafe without validation)
grep -rn --include="*.ts" --include="*.js" \
  -E "path\.join\s*\(.*req\." src/

# Directory traversal characters in path handling
grep -rn --include="*.ts" --include="*.js" --include="*.py" \
  -E "\.\.\/" src/
```

**Safe pattern:** Always use `path.basename()` + whitelist validation, then resolve against a known safe directory.

---

## Check 6 — Authentication and Authorization

For every new or modified endpoint, verify:

1. **Authentication** — is the route protected if it should be?
   ```bash
   grep -rn "router\.(get|post|put|patch|delete)" src/routes/
   # Check each unprotected route — is that intentional?
   ```

2. **Authorization** — after verifying who the user is, does code verify what they can do?
   ```bash
   # Look for resource access without ownership check
   grep -rn --include="*.ts" --include="*.js" \
     -E "params\.id|params\[:id\]" src/routes/
   # Each hit: does the handler verify req.user.id === resource.userId?
   ```

3. **IDOR (Insecure Direct Object Reference)** — can a user access another user's resources?
   - Any endpoint that takes a resource ID from the URL/body and returns that resource
   - Verify: the handler checks `resource.userId === req.user.id` (or equivalent ownership/permission check)

4. **Admin routes** — are admin-only endpoints protected from regular users?
   ```bash
   grep -rn "admin\|isAdmin\|role.*admin" src/routes/
   ```

---

## Check 7 — Sensitive Data Exposure

Look for passwords, tokens, or PII in logs or responses.

```bash
# Password in logs
grep -rn --include="*.ts" --include="*.js" --include="*.py" \
  -E "(console\.log|print|logger)\s*\(.*password" src/

# Token in logs
grep -rn --include="*.ts" --include="*.js" \
  -E "(console\.log|logger\.(info|debug|warn))\s*\(.*token" src/

# Full user object logged (might include password hash)
grep -rn --include="*.ts" --include="*.js" \
  -E "console\.log\s*\(\s*(user|req\.user)" src/

# Password field returned in API response
grep -rn --include="*.ts" --include="*.js" \
  -E "res\.json\s*\(\s*user" src/
# Check: does the user object include password field?
```

---

## Check 8 — Dependency Security (if applicable)

```bash
# Node.js — check for known vulnerabilities
npm audit --audit-level=high

# Python
pip-audit  # if installed

# Check for packages with known issues
grep -E "(node-serialize|serialize-javascript|log4j)" package.json
```

Note any HIGH or CRITICAL findings.

---

## Output Format in REVIEW_REPORT.md

```markdown
## Security Review

### Hardcoded secrets
NONE FOUND
— or —
FOUND: `src/config/db.ts:14` — hardcoded database password in connection string

### SQL injection
NONE FOUND
— or —
FOUND: `src/routes/users.ts:42` — raw string interpolation in SELECT query

### Command injection: NONE FOUND / FOUND

### XSS: NONE FOUND / FOUND

### Path traversal: NONE FOUND / FOUND

### Authentication / authorization
ADEQUATE — all new routes protected by existing auth middleware
— or —
ISSUES FOUND: `src/routes/admin.ts:18` — DELETE /api/admin/users has no role check

### Sensitive data in logs: NONE FOUND / FOUND

### IDOR: NONE FOUND / FOUND

### Dependency vulnerabilities
NOT CHECKED / NONE FOUND / FOUND (<severity> in <package>)

### Security verdict: PASS / FAIL
```

---

## Quality Checklist

- [ ] Every grep search was run, not just the likely ones
- [ ] Every new endpoint was checked for auth and authorization
- [ ] Each finding includes exact file path and line number
- [ ] All findings are classified REQUIRED (never RECOMMENDED for security)
- [ ] Verdict is FAIL if any finding exists

## Safety Rules

- Never downgrade a security finding to RECOMMENDED or NOTE.
- Never approve a review with a known security vulnerability.
- If you are unsure whether something is exploitable, classify it REQUIRED.
  The developer can argue against it — but it must be acknowledged.
- Run every grep check even when "nothing changed in auth code" — injection can appear anywhere.
