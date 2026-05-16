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
correctly without asking any questions. Ambiguity here directly causes bugs.

## When to Use

After the master plan is approved and clarifying questions are resolved.
Before delegating to the Programmer.

## Input

- `.claude/work/MASTER_PLAN.md`
- `.claude/work/PROJECT_BRIEF.md`
- `.claude/work/DECISIONS.md`
- Actual source files you will reference in the contract (read them first)

---

## Process

### Step 1 — Read the actual source files

Before writing a single instruction, read every file the Programmer will touch.
Do not write a contract about code you have not read.

If the contract says "add a field to the User model", you must have read the User model file.
If the contract says "add middleware to the router", you must have read the router file.

### Step 2 — Write the objective

One sentence. Concrete and precise.

**Weak:** "Add auth to the app."  
**Strong:** "Add a Bearer token middleware to all `/api/*` routes that returns `{"error": "Unauthorized"}` with HTTP 401 when the token is missing or fails verification."

### Step 3 — List files to read first

The Programmer's reading list. Include every file that provides context for the tasks.
For each file, say **why** they should read it (what pattern or context they'll find there).

### Step 4 — List files allowed to modify

Explicit. Exhaustive. Every file the Programmer may touch.
If it is not on this list, the Programmer must stop and ask before modifying it.

### Step 5 — List files NOT allowed to modify

Files that might look related but must not be touched.
This prevents accidental breakage of unrelated systems.

### Step 6 — Write step-by-step tasks

Number every task. Each task must be:

- **Atomic** — one operation (add a function, change a field, add a route)
- **File-specific** — name the exact file path
- **Function-specific** — name the exact function or line area
- **Verifiable** — you can check if it was done by reading the file

**Weak:** "Add error handling."  
**Strong:** "In `src/middleware/auth.ts`, wrap the `jwt.verify()` call in a try/catch. In the catch block, call `next(new AppError(401, 'Invalid token'))` — use the existing `AppError` class from `src/utils/errors.ts`."

**Weak:** "Create a health endpoint."  
**Strong:** "In `src/routes/health.ts` (create this file), add `GET /api/health` that returns HTTP 200 with body `{"status": "ok", "timestamp": new Date().toISOString()}`. Register this route in `src/app.ts` before the auth middleware."

### Step 7 — Specify architecture rules

What patterns must the Programmer follow?

Examples (adapt to the actual project):
- "Follow the existing middleware pattern in `src/middleware/logger.ts`"
- "Use the existing `db.query()` helper in `src/db/client.ts`, not raw SQL"
- "Return errors using `AppError` from `src/utils/errors.ts`, not plain objects"
- "Register routes in `src/routes/index.ts` following the existing pattern"
- "Use the existing `validate()` helper from `src/utils/validation.ts` for input validation"

### Step 8 — List edge cases to handle

For each edge case, specify the input AND the expected behavior:

- Empty string title → return 400 `{"error": "Title is required"}`
- Title longer than 255 characters → return 400 `{"error": "Title too long"}`
- Task ID that does not exist → return 404 `{"error": "Task not found"}`
- Malformed JSON body → return 400 `{"error": "Invalid JSON"}`
- Missing required field → return 400 with the specific field name in error message

### Step 9 — Specify error handling

For each distinct error condition:
- Condition (what triggers it)
- HTTP status to return
- Response body (exact JSON structure)
- What to log (and log level)

### Step 10 — Write definition of done

Checkable conditions. Not "the feature works" — specific conditions:

- [ ] `GET /api/health` returns 200 with `{"status": "ok", "timestamp": "..."}`
- [ ] `POST /api/tasks` with empty title returns 400 with error message
- [ ] All routes in `src/routes/tasks.ts` are covered
- [ ] No TypeScript type errors (`npx tsc --noEmit` exits 0)
- [ ] No lint errors (`npm run lint` exits 0)
- [ ] TASKS.md is updated with all task statuses

---

## Output Format: IMPLEMENTATION_CONTRACT.md

```markdown
# Implementation Contract

## Objective
<one precise sentence>

## Files to read first
- `<exact path>` — <why: what pattern or context to find here>
- `<exact path>` — <why>

## Files allowed to modify
- `<exact path>` — <what change is expected>
- `<exact path>` — <what change is expected>

## Files NOT allowed to modify
- `<exact path>` — <why it must not be touched>

## Implementation tasks

### Task 1: <short name>
**File:** `<exact path>`
**Action:** <exact description — what to add/change/remove>
**Details:**
- <detail 1>
- <detail 2>
**Pattern to follow:** `<path to example file or function>`

### Task 2: <short name>
...

## Architecture rules
- <rule 1 — with file reference>
- <rule 2>

## Edge cases to handle
- <input or state> → <exact expected behavior and response>
- <input or state> → <exact expected behavior and response>

## Error handling
| Condition | HTTP Status | Response Body | Log Level |
|-----------|------------|---------------|-----------|
| <condition> | 400 | `{"error": "..."}` | warn |
| <condition> | 401 | `{"error": "Unauthorized"}` | info |
| <condition> | 404 | `{"error": "Not found"}` | info |
| <condition> | 500 | `{"error": "Internal error"}` | error |

## Definition of done
- [ ] <specific, checkable condition>
- [ ] <specific, checkable condition>
- [ ] `npm run lint` exits 0
- [ ] `npm run typecheck` exits 0 (or language equivalent)
- [ ] TASKS.md updated with completed task statuses
```

---

## Quality Checklist

- [ ] Objective is one precise sentence with exact endpoint names, field names, or behavior
- [ ] Every task references an exact file path and function/location
- [ ] Files allowed to modify is a complete, explicit list (not "relevant files")
- [ ] At least 3 edge cases are specified with input → response format
- [ ] Error handling table has specific status codes and exact response body formats
- [ ] Architecture rules reference actual project files, not generic patterns
- [ ] Definition of done uses checkable conditions, not vague "it works"

## Safety Rules

- Never write a task about a file you have not read.
- Never allow modification of files without specifying the exact change.
- Never write security-sensitive instructions without verifying the auth model first.
- Never use vague verbs: "handle", "manage", "deal with" → replace with exact actions.
