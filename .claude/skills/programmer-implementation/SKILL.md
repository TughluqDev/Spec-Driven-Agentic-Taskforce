---
name: programmer-implementation
description: >
  Teaches the Programmer how to safely implement code changes from a contract.
  Covers reading context, making minimal changes, following conventions, and
  reporting results accurately without claiming success prematurely.
---

# Skill: programmer-implementation

## Purpose

Implement exactly what the contract says. No more, no less.
Make it work. Make it follow conventions. Report what happened honestly.

## When to Use

When delegated implementation tasks by the Planner.
This is the primary skill for the Programmer agent.

## Input

- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- `.claude/work/MASTER_PLAN.md`
- `.claude/work/TASKS.md`
- Source files listed in contract

## Output

- Modified/created source files
- Updated `.claude/work/TASKS.md`
- Implementation report to Planner

---

## Process

### Step 1 — Read before writing

Read every file in "Files to read first" from the contract.
Read 2–3 nearby existing files to understand conventions:
- Naming patterns (camelCase vs snake_case, etc.)
- Import organization
- Error handling style
- File structure

Do not skip this step. Conventions discovered here prevent rework.

### Step 2 — Map the tasks

Read `TASKS.md`. Identify which tasks are yours. Start with Task 1.
Work in task order unless the contract specifies dependencies.

### Step 3 — Implement task by task

For each task:
1. Re-read the task description
2. Open the target file
3. Find the exact location to modify
4. Make the change
5. Verify the change is correct before moving to the next task

One task at a time. Do not make multiple changes in one edit.

### Step 4 — Stay in scope

Before every edit, ask:
- Is this file in "Files allowed to modify"?
- Is this change required by the contract?

If the answer to either is no, stop. Report to the Planner.

### Step 5 — Run quick checks

After all tasks are complete:

```bash
# Use whichever applies to this project
npm run lint
npm run typecheck
npx tsc --noEmit
python -m mypy .
go vet ./...
```

If a check fails, diagnose and fix before reporting.
If you cannot fix it, report the exact error to the Planner.

### Step 6 — Update TASKS.md

Mark each completed task:
```
- [x] TASK-001: <description>
```

### Step 7 — Write the implementation report

Return to the Planner with:
- Tasks completed
- Files modified (with one-line description of each change)
- Files created
- Quick check results
- Issues encountered
- Assumptions made

---

## Output Format: Implementation Report

```
## Implementation Report

### Tasks completed
- TASK-001: <what was done>
- TASK-002: <what was done>

### Files modified
- `src/middleware/auth.ts` — added token validation middleware

### Files created
- `src/middleware/auth.ts` — new file

### Quick checks
- lint: PASS
- typecheck: PASS

### Issues encountered
- <none / description>

### Assumptions made
- <none / description>
```

---

## Quality Checklist

- [ ] Read all required files before writing code
- [ ] Followed existing naming and style conventions
- [ ] Only modified files in the allowed list
- [ ] Every task in the contract was completed
- [ ] Quick checks were run and results are reported
- [ ] TASKS.md is updated

## Safety Rules

- Never modify files not on the allowed list.
- Never add features beyond the contract scope.
- Never claim tests pass — that is the Tester's job.
- Never hardcode secrets, tokens, or credentials.
- Stop and report if you find a security issue.