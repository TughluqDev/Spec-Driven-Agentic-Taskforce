# Test Contract

<!-- Written by: Planner Agent (planner-test-contract skill) -->
<!-- Read by: Tester Agent -->
<!-- This is the Tester's instruction set. Run every check listed here. -->

---

## Feature under test
<!-- Short name of the feature being tested -->
_To be filled by Planner_

---

## Expected behavior
<!-- Precise description of what the feature should do -->
- _Behavior_

---

## Commands to run

### Build check
```
_exact command_
```
Expected: exits with code 0

### Lint
```
_exact command_
```
Expected: no errors

### Typecheck
```
_exact command_
```
Expected: no errors

### Unit tests
```
_exact command_
```
Expected: all pass

### Integration tests
```
_exact command — or "N/A"_
```

---

## API checks

### Scenario: _Name_ (happy path)
- **Method:** GET / POST / PUT / DELETE
- **URL:** `http://localhost:PORT/api/endpoint`
- **Headers:** `Content-Type: application/json`
- **Body:** `{"key": "value"}` / _none_
- **Expected status:** 200
- **Expected response:** `{"field": "value"}`

### Scenario: _Name_ (validation error)
- **Method:** POST
- **URL:** `http://localhost:PORT/api/endpoint`
- **Body:** `{}` (missing required field)
- **Expected status:** 400
- **Expected response:** contains an error message

### Scenario: _Name_ (auth check)
- **Method:** GET
- **URL:** `http://localhost:PORT/api/protected`
- **Headers:** _none_
- **Expected status:** 401

---

## Frontend / browser checks
<!-- Remove this section if no UI was changed -->

### Scenario: _Name_
1. Navigate to `http://localhost:PORT/page`
2. _Action_
3. Expected: _visible state_

---

## Regression checks
<!-- Behaviors that must still work after this change -->
- _Existing behavior that must not break_

---

## Edge cases to verify

| Input / State | Expected behavior |
|--------------|-------------------|
| _Edge case_ | _Behavior_ |

---

## Pass criteria
- All commands exit with code 0
- All API checks return the expected status and response
- All frontend checks show expected UI states
- No previously-passing tests are now failing

## Fail criteria
- Any command exits with a non-zero code
- Any API check returns an unexpected status or body
- Any regression test fails
- A required check could not be run