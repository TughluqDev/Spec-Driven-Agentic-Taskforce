---
name: reviewer-code-review
description: >
  Teaches the Reviewer how to conduct a strict, thorough code review against the
  implementation contract. Covers correctness, maintainability, conventions,
  and contract alignment. Returns specific findings, not vague impressions.
---

# Skill: reviewer-code-review

## Purpose

Verify that the implementation is correct, maintainable, and matches the contract.
Find real problems before they reach production.

## When to Use

When delegated review by the Planner.
This is the primary skill for the Reviewer agent.

## Input

- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- `.claude/work/MASTER_PLAN.md`
- `.claude/work/TEST_REPORT.md`
- Git diff
- Full content of changed files

## Output

Findings recorded in `.claude/work/REVIEW_REPORT.md`

---

## Process

### Step 1 — Get the diff

```bash
git diff HEAD~1
git diff --name-only HEAD~1
```

List all changed files. Read each one in full — do not rely on the diff alone.

### Step 2 — Check contract alignment

Compare each changed file against `IMPLEMENTATION_CONTRACT.md`:

- Is every task from the contract implemented?
- Were any out-of-scope files modified?
- Were any out-of-scope changes made to in-scope files?
- Does the implementation match the exact instructions (endpoint names, field names, response format, etc.)?

### Step 3 — Check correctness

For every new function or changed logic:

- Is the logic correct for all input cases?
- Are all required edge cases handled?
- Is the error handling complete?
- Could this produce unexpected output for any valid input?

Look for:
- Off-by-one errors
- Null/undefined dereferences
- Missing null checks
- Incorrect boolean logic
- Wrong status codes returned

### Step 4 — Check maintainability

- Are variable and function names clear and consistent with the rest of the codebase?
- Is the code understandable without reading external context?
- Is there dead code or leftover debug output (`console.log`, `print`, `fmt.Println`)?
- Are there magic numbers or strings that should be named constants?
- Is complexity appropriate (no deep nesting, no 200-line functions unless unavoidable)?

### Step 5 — Check test evidence

Review `TEST_REPORT.md`:
- Did the Tester actually test what this change does?
- Are API checks present with real status codes and response bodies?
- Were edge cases from the contract tested?
- If the report says PASS, is the evidence credible?
- If the report says FAIL, the review verdict must be FAIL.

---

## Severity Classification

For each finding, classify:

- **REQUIRED** — must be fixed before PASS (correctness issue, security issue, contract mismatch)
- **RECOMMENDED** — should be addressed but does not block (minor maintainability, style)
- **NOTE** — informational (pattern worth noting, no action needed)

---

## Output Format in REVIEW_REPORT.md

```markdown
## Code Review Findings

### Contract alignment
- [REQUIRED] Task 2 was not implemented: <missing function>
- [NOTE] All other tasks match the contract

### Correctness
- [REQUIRED] `src/auth.ts:45` — missing null check on `req.headers.authorization`
- [RECOMMENDED] `src/auth.ts:67` — catch block swallows the error without logging

### Maintainability
- [NOTE] `src/auth.ts:23` — variable name `x` should be `tokenPayload`
```

---

## Quality Checklist

- [ ] Diff was read in full, not just skimmed
- [ ] Every changed file was read in full
- [ ] Contract alignment was checked task by task
- [ ] Test evidence was reviewed, not assumed adequate
- [ ] Findings include file paths and line numbers
- [ ] Each finding is classified (REQUIRED / RECOMMENDED / NOTE)

## Safety Rules

- Be specific. "This looks fine" is not a review.
- Never approve work when the test report shows FAIL.
- Never skip the test evidence review.
- Do not implement fixes — report them.