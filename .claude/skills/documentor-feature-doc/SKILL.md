---
name: documentor-feature-doc
description: >
  Teaches the Documentor how to write a complete FEATURE_DOC.md that explains
  what changed, why, how it was implemented, what was tested, and how to deploy
  and roll back. Written for a human reviewer who knows nothing about this session.
---

# Skill: documentor-feature-doc

## Purpose

Produce documentation that lets any engineer on the team understand this feature
without reading the conversation or the agent session. This document outlives the session.

## When to Use

After the Reviewer returns a verdict (PASS or FAIL — document either way).
Before writing the PR message.

## Input

- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- `.claude/work/MASTER_PLAN.md`
- `.claude/work/TEST_REPORT.md`
- `.claude/work/REVIEW_REPORT.md`
- `.claude/work/DECISIONS.md`
- Changed source files (read them)
- Git diff

## Output

`.claude/work/FEATURE_DOC.md`

---

## Process

### Step 1 — Read all inputs

Do not write from memory. Read the actual files and actual diff.
Your document must reflect what was built, not what was planned.

### Step 2 — Identify the user problem

What problem does this feature solve?
Write one clear sentence from the user's perspective.

### Step 3 — Describe the behavior

What does the user/caller experience?
Be concrete. "The endpoint returns a 200 with `{"status": "ok"}`" is better than "it works."

### Step 4 — Describe the implementation

How was it built? Keep this brief and accurate.
Reference file names and key functions, not pseudocode.

### Step 5 — List all changed files

Read the git diff. List every file with a one-line description of what changed.

### Step 6 — Document API changes

For every new or changed endpoint:
- Method and URL
- Request format (headers, body fields)
- Response format (status codes, body)
- Auth requirements

### Step 7 — Document UI changes

For every new or changed screen/component:
- What is visible?
- What interactions are available?
- What states exist (loading, empty, error, success)?

### Step 8 — Document database changes

For every schema change:
- Table and column names
- Data types
- Migration name and direction (up/down)
- Whether the migration is reversible

### Step 9 — Summarize testing

State which checks passed and which failed.
Do not claim PASS unless `TEST_REPORT.md` verdict is PASS.

### Step 10 — Write rollback and deployment notes

How to deploy: any migrations, config changes, or restarts required.
How to rollback: is it safe to revert the code? What happens to existing data?

---

## Output Format: FEATURE_DOC.md

```markdown
# Feature: <name>

## Purpose
<one sentence: what problem does this solve?>

## User-facing behavior
<what the user or API caller experiences>

## Technical design
<how it was implemented — files, patterns, key decisions>

## Files changed

| File | Change |
|------|--------|
| `src/routes/health.ts` | Added GET /health endpoint |

## API changes

### GET /api/health
- **Auth required:** No
- **Request:** No body
- **Response 200:**
  ```json
  {"status": "ok", "timestamp": "..."}
  ```

## UI changes
<none / description>

## Database changes
<none / migration name and description>

## Config / environment changes
<none / new env vars and their purpose>

## Testing completed
- Build: PASS / FAIL
- Lint: PASS / FAIL
- Unit tests: PASS / FAIL
- API checks: PASS / FAIL
- Browser checks: PASS / FAIL / MANUAL-REQUIRED
- Overall: PASS / FAIL

## Review result
PASS / FAIL — <brief summary of findings>

## Known limitations
<none / list>

## Deployment notes
<none / steps required>

## Rollback notes
<safe to revert / steps if migration must be undone>
```

---

## Quality Checklist

- [ ] Written for an engineer who was not in this session
- [ ] All changed files are listed with accurate descriptions
- [ ] API documentation matches actual implementation (read the code)
- [ ] Test verdicts match TEST_REPORT.md exactly
- [ ] Review verdict matches REVIEW_REPORT.md exactly
- [ ] No secrets or credentials included
- [ ] Deployment and rollback notes are specific

## Safety Rules

- Never claim PASS if the actual report says FAIL.
- Never include secrets, tokens, or connection strings.
- Never describe planned behavior — describe what was actually built.