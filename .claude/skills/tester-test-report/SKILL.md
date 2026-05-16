---
name: tester-test-report
description: >
  Teaches the Tester how to write a complete, honest TEST_REPORT.md with a
  PASS or FAIL verdict backed by real command output, API response data, and
  browser check observations. No invented results.
---

# Skill: tester-test-report

## Purpose

Write a test report that gives the Reviewer and Documentor complete, accurate
evidence of what was tested and what was found. A good test report makes the
review faster and the documentation trustworthy.

## When to Use

After all test checks have been run.
Before reporting back to the Planner.

## Input

- `.claude/work/TEST_CONTRACT.md`
- All command outputs (captured during testing)
- All API request/response data (captured during testing)
- All browser check observations

## Output

`.claude/work/TEST_REPORT.md`

---

## Report Structure

### 1. Verdict

The first line must be the verdict:

```
## Verdict: PASS
```
or
```
## Verdict: FAIL
```

Rules:
- PASS: every required check passed and evidence is present
- FAIL: any required check failed, OR a required check could not be run

### 2. Summary

2–3 sentences. What was tested? What was the overall result?

### 3. Commands run

For each command, record:
- The exact command
- The full output (or a relevant excerpt for long outputs)
- The result: PASS / FAIL

```markdown
### Build check
**Command:** `npm run build`
**Result:** PASS
**Output:**
> Build succeeded. 0 errors, 0 warnings.
```

### 4. API check results

For each API scenario from the test contract:
- The request (method, URL, headers, body)
- The actual response status
- The actual response body (relevant excerpt)
- The verdict: PASS / FAIL

```markdown
### API: POST /api/health (happy path)
**Request:** POST http://localhost:3000/api/health
**Body:** (none)
**Expected status:** 200
**Actual status:** 200
**Response body:** `{"status": "ok", "timestamp": "2026-05-15T..."}`
**Result:** PASS
```

### 5. Frontend / browser check results

For each browser scenario:
- The URL navigated to
- The actions taken
- The expected state
- The actual observed state
- The verdict: PASS / FAIL / MANUAL-REQUIRED

### 6. Edge cases checked

List each edge case from the test contract and whether it was verified:
```
- Empty body → 400 Bad Request: PASS
- Missing auth token → 401 Unauthorized: PASS
- Invalid JSON → 400 Bad Request: PASS
```

### 7. Failures

For every failure, provide:
- The exact check that failed
- The exact error message or unexpected response
- Whether it was a test failure or an implementation issue

### 8. Untested areas

Honestly list any checks that were not run and why:
- "Integration tests not run — no test database configured"
- "Browser checks not run — Playwright MCP not available"

### 9. Final notes

Any observations relevant to the Reviewer or Planner.

---

## Output Format: TEST_REPORT.md

```markdown
# Test Report

## Verdict: PASS / FAIL

## Summary
<2-3 sentences>

## Commands run

### <check name>
**Command:** `<exact command>`
**Result:** PASS / FAIL
**Output:**
```
<output>
```

## API check results

### <scenario name>
**Request:** <method> <url>
**Body:** <body or none>
**Expected status:** <status>
**Actual status:** <status>
**Response:** <body excerpt>
**Result:** PASS / FAIL

## Frontend check results

### <scenario name>
**URL:** <url>
**Actions:** <what was done>
**Expected:** <what should appear>
**Actual:** <what was observed>
**Result:** PASS / FAIL / MANUAL-REQUIRED

## Edge cases checked
- <case> → <result>: PASS / FAIL

## Failures
<none / list>

## Untested areas
<none / list with reasons>

## Final notes
<none / observations>
```

---

## Quality Checklist

- [ ] Verdict is on the first line (PASS or FAIL)
- [ ] Every required check from the test contract has an entry
- [ ] API checks include actual status codes and response bodies
- [ ] Failures have exact error messages
- [ ] Untested areas are listed with reasons
- [ ] No results are fabricated

## Safety Rules

- Never write PASS for a check you did not run.
- Never truncate failure output — include the full error.
- Never omit a failing check — report it prominently.