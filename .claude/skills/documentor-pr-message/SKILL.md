---
name: documentor-pr-message
description: >
  Teaches the Documentor how to write a PR title and body ready to paste into
  GitHub or Azure DevOps. Covers the required sections, how to represent test
  and review verdicts honestly, and how to write a useful reviewer checklist.
---

# Skill: documentor-pr-message

## Purpose

Write a PR message that gives reviewers everything they need to understand,
verify, and merge (or reject) this change — without needing to ask questions.

## When to Use

After `FEATURE_DOC.md` is written.
This is the final output of the Documentor agent.

## Input

- `.claude/work/FEATURE_DOC.md`
- `.claude/work/TEST_REPORT.md`
- `.claude/work/REVIEW_REPORT.md`
- `.claude/work/REVIEW_REPORT.md` (risk section)

## Output

`.claude/work/PR_MESSAGE.md`

---

## PR Title Guidelines

- Maximum 72 characters
- Starts with a verb: "Add", "Fix", "Update", "Remove", "Refactor"
- Describes the change, not the process
- Do not include ticket/issue numbers in the title (put them in the body)

Good titles:
- `Add health check endpoint to API`
- `Fix authentication bypass on /api/admin routes`
- `Update user profile to support avatar uploads`

Bad titles:
- `WIP: some auth stuff`
- `Fixes`
- `As per requirements, implement the feature requested in meeting on 5/15`

---

## PR Body Structure

### Summary

2–3 sentences. What does this PR do and why?
Write from the reviewer's perspective — what do they need to know immediately?

### What changed

Bullet list of changed areas. Group by area (API, UI, database, config).
One line per change.

### Why this changed

The motivation: user story, bug description, requirement, technical debt.
Link to issue/ticket if available.

### Testing

Table or bullet list of all checks with actual verdicts from `TEST_REPORT.md`.
Do not fabricate results.

### Risk level

LOW / MEDIUM / HIGH with a brief reason.
Base this on the risk analysis from `REVIEW_REPORT.md`.

### Deployment notes

List any steps required to deploy this change:
- Database migrations
- New environment variables
- Service restarts
- Feature flag changes

If nothing is required, write "None."

### Breaking changes

List any changes that break existing behavior:
- Changed API contracts (field names, types, removed fields)
- Removed endpoints
- Changed authentication requirements

If none, write "None."

### Reviewer checklist

A checklist for the human reviewer to complete:
- [ ] Code matches described behavior
- [ ] Tests cover happy path and at least one error case
- [ ] No hardcoded secrets or credentials
- [ ] Deployment steps are clear and complete
- [ ] Breaking changes are documented
- (Add scenario-specific items based on what was built)

---

## Output Format: PR_MESSAGE.md

```markdown
# PR: <title under 72 chars>

## Summary
<2-3 sentences>

## What changed
- **API:** Added `GET /api/health` returning `{"status": "ok"}`
- **Config:** No changes
- **Database:** No changes
- **UI:** No changes

## Why this changed
<motivation — user story, requirement, bug>

## Testing
| Check | Result |
|-------|--------|
| Build | PASS |
| Lint | PASS |
| Typecheck | PASS |
| Unit tests | PASS |
| API checks | PASS |
| Browser checks | PASS / MANUAL-REQUIRED / N/A |

## Risk level
LOW — <reason>

## Deployment notes
None.

## Breaking changes
None.

## Reviewer checklist
- [ ] Code matches described behavior
- [ ] Tests cover happy path and at least one error case
- [ ] No hardcoded secrets or credentials
- [ ] Deployment steps are clear
```

---

## Quality Checklist

- [ ] Title is under 72 characters and starts with a verb
- [ ] Summary is 2–3 sentences max
- [ ] Test verdicts match TEST_REPORT.md exactly (no fabrication)
- [ ] Risk level is from the review report
- [ ] Deployment notes are specific (or explicitly "None")
- [ ] Reviewer checklist has at least 4 items
- [ ] No secrets or credentials anywhere in the document

## Safety Rules

- Never use "PASS" for a check that was not run or did not pass.
- Never include secrets, tokens, or connection strings.
- Keep the PR message under 1000 words — reviewers stop reading long PRs.