---
name: planner-implementation-contract
description: >
  Teaches the Planner how to write an exact, unambiguous IMPLEMENTATION_CONTRACT.md
  for the Programmer. Covers files to read, files allowed to modify, step-by-step
  tasks, constraints, edge cases, and definition of done.
---

# Skill: planner-implementation-contract

## Purpose

Write instructions so precise that the Programmer can implement the feature
correctly without asking any questions. Ambiguity here causes bugs.

## When to Use

After the master plan is written and approved.
Before delegating to the Programmer.

## Input

- `.claude/work/MASTER_PLAN.md`
- `.claude/work/PROJECT_BRIEF.md`
- `.claude/work/DECISIONS.md`
- Relevant source files (read them before writing the contract)

## Output

`.claude/work/IMPLEMENTATION_CONTRACT.md`

---

## Process

### Step 1 — Read the relevant source files

Before writing any instruction, read the files the Programmer will touch.
Understand the current structure. Do not write instructions based on assumptions
about what the code looks like — read it.

### Step 2 — Write the objective

One sentence. What does this contract produce?

Bad: "Add auth to the app."
Good: "Add a Bearer token middleware to all `/api/*` routes that returns 401 when the token is missing or invalid."

### Step 3 — List files to read first

Give the Programmer a reading list so they understand context before writing.

### Step 4 — List files allowed to modify

Be explicit. Only files on this list may be modified.
If the Programmer discovers they need to modify another file, they must stop and report.

### Step 5 — List files not allowed to modify

List files that might seem related but must not be touched.
This prevents accidental breakage of unrelated systems.

### Step 6 — Write step-by-step tasks

Number every task. Each task should be:
- Atomic (one thing at a time)
- Specific (exact function name, file path, data structure)
- Verifiable (you can check if it was done)

Bad: "Add error handling."
Good: "In `src/middleware/auth.ts`, add a try/catch around the `jwt.verify()` call. On failure, call `res.status(401).json({ error: 'Invalid token' })`."

### Step 7 — Specify architecture rules

What patterns must be followed?
- "Follow the existing middleware pattern in `src/middleware/logger.ts`"
- "Use the existing `db.query()` helper, not raw SQL"
- "Return errors using the existing `AppError` class, not plain objects"

### Step 8 — List edge cases to handle

What inputs or states require special handling?
- Empty string inputs
- Null values
- Missing optional fields
- Concurrent requests
- Very large inputs

### Step 9 — Specify error handling

For each error condition, specify:
- What error to detect
- What to return/throw
- What to log

### Step 10 — Write definition of done

How does the Programmer know when the task is complete?
List the exact conditions.

---

## Output Format: IMPLEMENTATION_CONTRACT.md

```markdown
# Implementation Contract

## Objective
<one precise sentence>

## Files to read first
- `<path>` — <why to read it>

## Files allowed to modify
- `<path>` — <what change is expected>

## Files NOT allowed to modify
- `<path>` — <why it must not be touched>

## Implementation tasks

### Task 1: <short name>
**File:** `<path>`
**Action:** <exact description>
**Details:**
- <detail>
- <detail>

### Task 2: <short name>
...

## Architecture rules
- <rule>

## Edge cases to handle
- <case> → <expected behavior>

## Error handling
- <error condition> → <response/exception/log>

## Definition of done
- [ ] <condition>
- [ ] <condition>
```

---

## Quality Checklist

- [ ] Objective is one precise sentence
- [ ] Every task references an exact file and function
- [ ] Files allowed to modify is an explicit list (not "relevant files")
- [ ] At least 2 edge cases are specified
- [ ] Error handling specifies exact responses, not just "handle errors"
- [ ] Definition of done is checkable without running tests

## Safety Rules

- Do not write vague tasks. If you cannot be specific, re-read the code first.
- Do not allow modification of files you have not inspected.
- Do not write security-sensitive instructions without verifying the auth model first.
