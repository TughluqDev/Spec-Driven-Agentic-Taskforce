# Test Report

<!-- Written by: Tester Agent (tester-test-report skill) -->
<!-- Read by: Reviewer Agent, Documentor Agent, Planner Agent -->
<!-- IMPORTANT: Only mark PASS when real evidence supports it. -->

---

## Verdict: _PASS / FAIL_

<!-- This must be the first thing in the file. -->
<!-- PASS = all required checks passed with evidence. -->
<!-- FAIL = any required check failed, or a required check could not be run. -->

---

## Summary
<!-- 2-3 sentences: what was tested and what the overall result was. -->
_To be filled by Tester_

---

## Commands run

### Build check
**Command:** `_exact command_`
**Result:** PASS / FAIL
**Output:**
```
_output_
```

### Lint
**Command:** `_exact command_`
**Result:** PASS / FAIL
**Output:**
```
_output_
```

### Typecheck
**Command:** `_exact command_`
**Result:** PASS / FAIL
**Output:**
```
_output_
```

### Unit tests
**Command:** `_exact command_`
**Result:** PASS / FAIL
**Output:**
```
_output — include pass/fail counts_
```

### Integration tests
**Command:** `_exact command_`
**Result:** PASS / FAIL / SKIPPED
**Reason skipped:** _if applicable_

---

## API check results

### Scenario: _Name_
**Request:** `METHOD http://localhost:PORT/endpoint`
**Body:** `_body or none_`
**Expected status:** _status_
**Actual status:** _status_
**Response body:**
```json
_body_
```
**Result:** PASS / FAIL

---

## Frontend / browser check results

### Scenario: _Name_
**URL:** `http://localhost:PORT/page`
**Actions:** _what was done_
**Expected:** _expected state_
**Actual:** _observed state_
**Result:** PASS / FAIL / MANUAL-REQUIRED

---

## Edge cases checked

| Edge case | Expected | Actual | Result |
|-----------|----------|--------|--------|
| _Case_ | _Expected_ | _Actual_ | PASS/FAIL |

---

## Regression check results
**Full test suite run:** YES / NO
**Previously-failing tests now passing:** _count / none_
**Previously-passing tests now failing:** _count / none_
**Newly failing tests:** _list or none_

---

## Failures
<!-- List every failure with exact error messages. -->
_None / list_

---

## Untested areas
<!-- Honest list of what was NOT tested and why. -->
_None / list with reasons_

---

## Final notes
_None / any observations for the Reviewer or Planner_