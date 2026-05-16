# Review Report

<!-- Written by: Reviewer Agent -->
<!-- Read by: Documentor Agent, Planner Agent -->
<!-- IMPORTANT: Mark FAIL for any security issue, correctness issue, or failed test evidence. -->

---

## Verdict: _PASS / FAIL_

<!-- PASS = all dimensions satisfactory and test evidence adequate. -->
<!-- FAIL = any required fix, security issue, or test failure. -->

---

## Summary
<!-- 2-3 sentences: what was reviewed and what the overall finding is. -->
_To be filled by Reviewer_

---

## Contract alignment

<!-- Did the implementation match the contract? Were any tasks missed? Were out-of-scope files modified? -->

- _[REQUIRED / RECOMMENDED / NOTE] finding_
- _[NOTE] All other tasks match the contract_

---

## Code quality

<!-- Correctness, maintainability, naming, error handling, dead code, debug output. -->
<!-- Include file path and line number for every finding. -->

- _[REQUIRED] `src/file.ts:45` — description_
- _[RECOMMENDED] `src/file.ts:67` — description_

---

## Architecture alignment

<!-- Does the change follow existing patterns? Does it break interfaces or contracts? -->

- _[NOTE] Follows existing middleware pattern_

---

## Security review

### Hardcoded secrets: NONE FOUND / FOUND
_detail if found_

### SQL injection: NONE FOUND / FOUND
_detail if found_

### Command injection: NONE FOUND / FOUND
_detail if found_

### XSS: NONE FOUND / FOUND
_detail if found_

### Auth / authorization: ADEQUATE / ISSUES FOUND
_detail if issues found_

### Sensitive data in logs: NONE FOUND / FOUND
_detail if found_

### IDOR: NONE FOUND / FOUND
_detail if found_

**Security verdict:** PASS / FAIL

---

## Risk review

### Regression risk: LOW / MEDIUM / HIGH
_description_

### Data risk: LOW / MEDIUM / HIGH
_description_

### Performance risk: LOW / MEDIUM / HIGH
_description_

### Deployment risk: LOW / MEDIUM / HIGH
_required steps, or "None"_

### Rollback risk: LOW / MEDIUM / HIGH
_description, or "Safe to revert"_

### User impact risk: LOW / MEDIUM / HIGH
_description, or "No user-facing changes"_

---

## Test evidence review

<!-- Was the test report adequate? Were the right things tested? -->

- Test report verdict: PASS / FAIL
- API checks: present with real status codes / missing
- Edge cases: tested / not tested
- Regression checks: run / not run
- Assessment: ADEQUATE / INADEQUATE

---

## Required fixes
<!-- Must be resolved before this can be approved. -->
- _None / list_

---

## Recommended improvements
<!-- Non-blocking. Should be done but does not block this PR. -->
- _None / list_

---

## Final decision

**PASS** — ready for documentation and PR.
/ 
**FAIL** — required fixes must be addressed before proceeding.