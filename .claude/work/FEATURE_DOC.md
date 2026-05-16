# Feature Documentation

<!-- Written by: Documentor Agent (documentor-feature-doc skill) -->
<!-- Read by: Human reviewers, team members, future maintainers -->
<!-- Written for someone who was NOT in this session. Be complete and accurate. -->

---

## Feature: _Name_

---

## Purpose
<!-- What problem does this solve? One sentence from the user's perspective. -->
_To be filled by Documentor_

---

## User-facing behavior
<!-- What does the user or API caller experience? Be concrete. -->
_To be filled by Documentor_

---

## Technical design
<!-- How was it implemented? Brief and accurate. Reference file names. -->
_To be filled by Documentor_

---

## Files changed

| File | Change |
|------|--------|
| _path_ | _one-line description_ |

---

## API changes

<!-- Document every new or changed endpoint. Remove if no API changed. -->

### _METHOD_ _/path_

- **Auth required:** Yes / No
- **Request headers:** `Content-Type: application/json`
- **Request body:**
  ```json
  {"field": "type"}
  ```
- **Response 200:**
  ```json
  {"field": "value"}
  ```
- **Response 400:** `{"error": "description"}`
- **Response 401:** `{"error": "Unauthorized"}`

---

## UI changes
<!-- Describe new/changed screens and interactions. Remove if no UI changed. -->
_None_ / _description_

---

## Database changes
<!-- Migrations, new tables, new columns, index changes. Remove if none. -->
_None_ / _migration name and description_

---

## Config / environment changes
<!-- New env vars, feature flags, deployment config. Remove if none. -->
_None_ / _list of env vars with purpose_

---

## Testing completed

| Check | Result |
|-------|--------|
| Build | PASS / FAIL |
| Lint | PASS / FAIL |
| Typecheck | PASS / FAIL |
| Unit tests | PASS / FAIL |
| Integration tests | PASS / FAIL / N/A |
| API checks | PASS / FAIL |
| Browser checks | PASS / FAIL / MANUAL-REQUIRED / N/A |
| Regression | PASS / FAIL |

**Overall test verdict:** PASS / FAIL

---

## Review result
<!-- From REVIEW_REPORT.md -->
PASS / FAIL — _brief summary of findings_

---

## Known limitations
<!-- Anything not implemented, deferred, or incomplete. -->
_None_ / _list_

---

## Deployment notes
<!-- Steps required to deploy. Be specific. -->
_None required_ / _list steps_

---

## Rollback notes
<!-- How to undo this if it causes issues in production. -->
_Safe to revert the code change_ / _describe rollback steps_