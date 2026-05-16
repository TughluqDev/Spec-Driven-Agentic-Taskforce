---
name: tester-api-check
description: >
  Teaches the Tester how to test backend APIs using curl, PowerShell
  Invoke-RestMethod, npm scripts, or project-specific API clients. Covers
  happy paths, validation errors, auth behavior, and edge cases.
---

# Skill: tester-api-check

## Purpose

Verify that API endpoints behave correctly by sending real HTTP requests and
inspecting real responses. This is the only reliable way to test backend behavior.

## When to Use

When the test contract includes API check scenarios.
When the feature adds or changes any API endpoint.

## Input

- `.claude/work/TEST_CONTRACT.md` (API check scenarios)
- The running server URL and port

## Output

- API check results recorded in `TEST_REPORT.md`

---

## Ensuring the Server Is Running

Before running API checks, verify the server is running:

```bash
# Check if a port is in use (Linux/Mac)
lsof -i :3000

# Check if a port is in use (Windows PowerShell)
netstat -an | findstr :3000
```

If the server is not running, start it:
```bash
npm run dev
# or
npm start
# or
python main.py
# or
go run .
```

Wait for the ready message before sending requests.

---

## Making Requests

### Linux / Mac — curl

Basic GET:
```bash
curl -s -w "\n[HTTP %{http_code}]" http://localhost:3000/api/health
```

POST with JSON body:
```bash
curl -s -w "\n[HTTP %{http_code}]" \
  -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name": "Alice", "email": "alice@example.com"}'
```

With auth header:
```bash
curl -s -w "\n[HTTP %{http_code}]" \
  -H "Authorization: Bearer <token>" \
  http://localhost:3000/api/protected
```

### Windows — PowerShell Invoke-WebRequest

```powershell
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/health" -Method GET
Write-Output "Status: $($resp.StatusCode)"
Write-Output "Body: $($resp.Content)"
```

POST with JSON:
```powershell
$body = '{"name": "Alice", "email": "alice@example.com"}'
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/users" `
  -Method POST `
  -ContentType "application/json" `
  -Body $body
Write-Output "Status: $($resp.StatusCode)"
Write-Output "Body: $($resp.Content)"
```

With auth:
```powershell
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/protected" `
  -Headers @{ Authorization = "Bearer <token>" }
```

---

## Required Test Scenarios

For every endpoint in the test contract, run:

### 1. Happy path
- Send a valid request with all required fields
- Expect: 2xx status + correct response body

### 2. Validation error
- Send a request with missing or invalid fields
- Expect: 400 or 422 with an error message

### 3. Auth check (if endpoint requires auth)
- Send request with no token → expect 401
- Send request with invalid token → expect 401 or 403
- Send request with valid token → expect 2xx

### 4. Edge cases from test contract
- Empty string values
- Missing optional fields
- Extra unexpected fields
- Very large inputs (if applicable)

---

## Recording Results

Capture and record for each scenario:

```markdown
### API: POST /api/users (happy path)
**Request:**
```
POST http://localhost:3000/api/users
Content-Type: application/json
Body: {"name": "Alice", "email": "alice@example.com"}
```
**Expected status:** 201
**Actual status:** 201
**Response body:**
```json
{"id": "123", "name": "Alice", "email": "alice@example.com"}
```
**Result:** PASS
```

---

## Quality Checklist

- [ ] Server was confirmed running before tests started
- [ ] Happy path tested for every endpoint
- [ ] Validation error tested for every endpoint
- [ ] Auth behavior tested for every protected endpoint
- [ ] Actual status codes are recorded (not assumed)
- [ ] Actual response bodies are recorded

## Safety Rules

- Never send requests to production URLs. Use localhost or a test environment.
- Never use real user credentials in test requests.
- Never run tests that delete or modify real data.
- If the server is not running, note this in the report — do not fabricate responses.