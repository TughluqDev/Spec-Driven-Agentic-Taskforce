---
name: tester-regression-check
description: >
  Teaches the Tester how to check for regressions after a code change. Covers
  running the full test suite, identifying at-risk areas, and avoiding the trap
  of only testing the happy path of the new feature.
---

# Skill: tester-regression-check

## Purpose

Verify that the new implementation has not broken existing behavior.
New features often have side effects. This skill finds them before review.

## When to Use

After running feature-specific checks.
Before writing the final TEST_REPORT.md.

## Input

- `.claude/work/IMPLEMENTATION_CONTRACT.md` (which files were changed)
- The project's full test suite
- Knowledge of which areas of the codebase were modified

## Output

- Regression check results recorded in `TEST_REPORT.md`

---

## Process

### Step 1 — Identify at-risk areas

Read `IMPLEMENTATION_CONTRACT.md → Files allowed to modify`.

For each modified file, think:
- What existing features use this file?
- What other files import from this file?
- What test files cover this area?

```bash
# Find files that import from a modified file
grep -r "from './auth'" src/
grep -r "import.*auth" src/
```

### Step 2 — Run the full test suite

Run every test, not just tests for the new feature:

```bash
npm test
npx vitest run
python -m pytest
go test ./...
cargo test
```

Check the total pass/fail count. Note any newly-failing tests.

### Step 3 — Check related endpoints

If any API routes were added or modified, test that nearby routes still work:

```bash
# Example: if you modified the /api/users POST handler, also test GET /api/users
curl -s http://localhost:3000/api/users
```

### Step 4 — Check shared utilities

If you modified any shared utility, helper, or middleware:
- Identify every caller of that utility
- Manually verify or run tests for a representative set of callers

### Step 5 — Check integration points

Did the change touch:
- Authentication? → Test that existing auth flows still work
- Database queries? → Test that existing data retrieval still works
- Configuration? → Test that the app starts cleanly with existing config
- Public API contracts? → Test that existing clients would still work

---

## Reporting Regressions

For any regression found:

```markdown
### Regression: <test name or behavior>
**Previously:** <what worked before>
**Now:** <what is broken>
**Cause:** <what change caused it>
**Severity:** HIGH / MEDIUM / LOW
```

Report every regression to the Planner immediately. Do not attempt to fix
regressions without Planner instruction — fixing may be out of scope.

---

## Regression vs New Failure

Distinguish between:
- **Regression** — a previously-passing test that now fails
- **Pre-existing failure** — a test that was already failing before this change

To confirm a failure is pre-existing:
```bash
git stash
npm test
git stash pop
```

If the test fails on the original code too, it is pre-existing. Note it but do not count it as a regression.

---

## Quality Checklist

- [ ] Full test suite was run (not just feature tests)
- [ ] At-risk areas were identified from changed files
- [ ] All newly-failing tests are documented with their error messages
- [ ] Pre-existing failures were distinguished from regressions
- [ ] Regression report is included in TEST_REPORT.md

## Safety Rules

- Never skip the full suite "to save time" — partial suites miss regressions.
- Never mark regression checks as PASS if the full suite was not run.
- Never fix a regression without reporting it to the Planner first.