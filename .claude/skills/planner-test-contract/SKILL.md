---
name: planner-test-contract
description: >
  Teaches the Planner how to write a complete TEST_CONTRACT.md for the Tester.
  Covers frontend, backend, API, build, lint, typecheck, unit, integration, and
  regression checks with exact commands, expected results, and pass/fail criteria.
---

# Skill: planner-test-contract

## Purpose

Write testing instructions so precise that the Tester knows exactly what to run,
what to check, and what evidence constitutes PASS vs FAIL.

## When to Use

After the implementation contract is written.
Before delegating to the Tester.

## Input

- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- `.claude/work/MASTER_PLAN.md` (acceptance criteria)
- `.claude/work/PROJECT_BRIEF.md` (key commands, test tools)

## Output

`.claude/work/TEST_CONTRACT.md`

---

## Process

### Step 1 — Derive expected behavior from acceptance criteria

Take every "Given..., when..., then..." from the master plan.
Turn each into a concrete test scenario.

### Step 2 — Write the command-level test plan

For each test category, write the exact command to run.
Do not write "run the tests" — write `npx vitest run --reporter=verbose`.

### Step 3 — Write API check scenarios

For every new or changed endpoint:
- Method and URL
- Request headers (Content-Type, Authorization if required)
- Request body (for POST/PUT/PATCH)
- Expected HTTP status code
- Expected response body (exact fields or schema)

Write at least:
- One happy path scenario
- One validation error scenario
- One auth scenario (if auth is involved)

### Step 4 — Write frontend check scenarios (if UI is involved)

For each affected page or component:
- URL to navigate to
- Actions to take (click, type, submit)
- Expected UI state (text present, element visible, error shown, etc.)

### Step 5 — Write regression checks

List tests or behaviors that must still work after this change.
These are things that could break if the implementation had a side effect.

### Step 6 — Write pass/fail criteria

Be explicit. State exactly what constitutes PASS and what constitutes FAIL.

---

## Output Format: TEST_CONTRACT.md

```markdown
# Test Contract

## Feature under test
<short name of feature>

## Expected behavior
- <behavior 1>
- <behavior 2>

## Commands to run

### Build check
```
<exact command>
```
Expected: exits with code 0

### Lint
```
<exact command>
```
Expected: no errors

### Typecheck
```
<exact command>
```
Expected: no errors

### Unit tests
```
<exact command>
```
Expected: all tests pass

### Integration tests
```
<exact command>
```
Expected: all tests pass

## API checks

### Scenario: <name>
- Method: <GET/POST/PUT/DELETE>
- URL: `<url>`
- Headers: `Content-Type: application/json`, `Authorization: Bearer <token>`
- Body: `{"key": "value"}`
- Expected status: <200/201/400/401/etc>
- Expected response: `{"field": "value"}`

### Scenario: <name>
...

## Frontend checks (if applicable)

### Scenario: <name>
1. Navigate to `<url>`
2. <action>
3. Expected: <what should be visible/present>

## Regression checks
- <test or behavior that must still work>

## Edge cases to verify
- <input or state> → <expected behavior>

## Pass criteria
- All commands exit with code 0
- All API checks return the expected status and response
- All frontend checks show expected UI states
- No previously-passing tests are now failing

## Fail criteria
- Any command exits with a non-zero code
- Any API check returns an unexpected status
- Any regression test fails
```

---

## Quality Checklist

- [ ] Every command is exact (copy-pasteable)
- [ ] Every API scenario has method, URL, expected status, and expected response
- [ ] At least one error/validation scenario is included
- [ ] Regression checks are listed
- [ ] Edge cases match those in the implementation contract
- [ ] Pass and fail criteria are explicit

## Safety Rules

- Do not write tests that require destructive operations (dropping tables, deleting prod data).
- Do not write API checks against production URLs — use localhost or test environment.
- Do not require browser checks if no UI was changed.