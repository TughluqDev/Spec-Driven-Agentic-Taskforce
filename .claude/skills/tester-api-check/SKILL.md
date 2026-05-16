---
name: tester-api-check
description: >
  Teaches the Tester how to test backend APIs using curl, PowerShell
  Invoke-WebRequest, npm scripts, or project-specific API clients. Covers
  happy paths, validation errors, auth behavior, and edge cases.
---

# Skill: tester-api-check

## Purpose

Verify that API endpoints behave correctly by sending real HTTP requests and
inspecting real responses. This is the only reliable way to verify backend behavior.
Static analysis and unit tests cannot replace it.

## When to Use

When the test contract includes API check scenarios.
When the feature adds or changes any API endpoint.

---

## Step 1 — Start and Verify the Server

**Check if server is running (Windows PowerShell):**
```powershell
netstat -an | findstr :3000
# If you see LISTENING, the port is in use. Confirm it's your server.
```

**Check if server is running (Linux / Mac):**
```bash
lsof -i :3000
```

**Start the server if needed:**
```bash
# Try in order based on project type:
npm run dev
npm start
node server.js
python main.py
go run .
cargo run
```

Wait for the "ready" or "listening" message before sending any requests.
If the server fails to start, record the error and mark API checks as FAIL — do not fabricate responses.

---

## Step 2 — Run Each Scenario

### Windows — PowerShell templates

**GET request:**
```powershell
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/health" -Method GET -UseBasicParsing
"Status: $($resp.StatusCode)"
$resp.Content | ConvertFrom-Json | ConvertTo-Json -Depth 10
```

**POST with JSON:**
```powershell
$body = '{"title": "Buy groceries"}'
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/tasks" `
  -Method POST `
  -ContentType "application/json" `
  -Body $body `
  -UseBasicParsing
"Status: $($resp.StatusCode)"
$resp.Content
```

**PATCH / PUT:**
```powershell
$body = '{"completed": true}'
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/tasks/1" `
  -Method PATCH `
  -ContentType "application/json" `
  -Body $body `
  -UseBasicParsing
"Status: $($resp.StatusCode)"
$resp.Content
```

**DELETE:**
```powershell
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/tasks/1" `
  -Method DELETE `
  -UseBasicParsing
"Status: $($resp.StatusCode)"
$resp.Content
```

**With Auth header:**
```powershell
$headers = @{ Authorization = "Bearer eyJ...TOKEN" }
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/protected" `
  -Method GET `
  -Headers $headers `
  -UseBasicParsing
"Status: $($resp.StatusCode)"
$resp.Content
```

**Handling 4xx errors (PowerShell throws on non-2xx by default):**
```powershell
try {
  $resp = Invoke-WebRequest -Uri "http://localhost:3000/api/tasks" `
    -Method POST `
    -ContentType "application/json" `
    -Body '{}' `
    -UseBasicParsing
  "Status: $($resp.StatusCode)"
  $resp.Content
} catch {
  $statusCode = $_.Exception.Response.StatusCode.value__
  $body = $_.ErrorDetails.Message
  "Status: $statusCode"
  "Body: $body"
}
```

### Linux / Mac — curl templates

**GET:**
```bash
curl -s -w "\n[HTTP %{http_code}]" http://localhost:3000/api/health
```

**POST with JSON:**
```bash
curl -s -w "\n[HTTP %{http_code}]" \
  -X POST http://localhost:3000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Buy groceries"}'
```

**PATCH:**
```bash
curl -s -w "\n[HTTP %{http_code}]" \
  -X PATCH http://localhost:3000/api/tasks/1 \
  -H "Content-Type: application/json" \
  -d '{"completed": true}'
```

**DELETE:**
```bash
curl -s -w "\n[HTTP %{http_code}]" \
  -X DELETE http://localhost:3000/api/tasks/1
```

**With auth:**
```bash
curl -s -w "\n[HTTP %{http_code}]" \
  -H "Authorization: Bearer TOKEN" \
  http://localhost:3000/api/protected
```

---

## Step 3 — Required Scenarios Per Endpoint

For every endpoint in the test contract, run ALL of these:

### Scenario A — Happy path
- Valid request with all required fields, valid auth if needed
- Expected: 2xx status + correct response body with all required fields present

### Scenario B — Missing required field
- Omit one required field from the request body
- Expected: 400 or 422 + error message mentioning the missing field

### Scenario C — Empty/blank required field
- Send an empty string `""` for a required string field
- Expected: 400 or 422 + error message

### Scenario D — Auth checks (for protected endpoints only)
- No token → 401
- Invalid/expired token → 401 (or 403)
- Valid token → 2xx

### Scenario E — Not found (for endpoints taking a resource ID)
- Request with a non-existent ID (e.g., `id=99999`)
- Expected: 404 + error message

### Scenario F — Edge cases from the test contract
Test each edge case listed in TEST_CONTRACT.md

---

## Step 4 — Record Results

For each scenario in TEST_REPORT.md:

```markdown
### API: POST /api/tasks — happy path
**Request:**
```
POST http://localhost:3000/api/tasks
Content-Type: application/json
Body: {"title": "Buy groceries"}
```
**Expected status:** 201
**Actual status:** 201
**Response body:**
```json
{"id": "abc123", "title": "Buy groceries", "completed": false, "createdAt": "2026-05-16T..."}
```
**Result:** PASS

---

### API: POST /api/tasks — empty title
**Request:**
```
POST http://localhost:3000/api/tasks
Content-Type: application/json
Body: {"title": ""}
```
**Expected status:** 400
**Actual status:** 400
**Response body:**
```json
{"error": "Title is required"}
```
**Result:** PASS
```

---

## Quality Checklist

- [ ] Server was confirmed running before any test
- [ ] Happy path tested for every endpoint
- [ ] Validation error tested for every endpoint
- [ ] Auth behavior tested for every protected endpoint
- [ ] Not-found case tested for every ID-based endpoint
- [ ] Actual status codes recorded (not assumed)
- [ ] Actual response bodies recorded (not expected bodies)
- [ ] 4xx error handling used correctly (try/catch in PowerShell)

## Safety Rules

- Never send requests to production URLs. Use localhost or a dedicated test environment only.
- Never use real user credentials or real API keys in test requests.
- Never run tests that irreversibly modify or delete real data.
- If the server is not running, record FAIL — do not fabricate responses.
- Record the actual response, not what you expect. The actual is the evidence.
