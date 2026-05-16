---
name: programmer-debugging
description: >
  Teaches the Programmer how to diagnose failing commands, error messages,
  broken tests, and runtime issues without guessing. Covers systematic diagnosis
  before attempting fixes.
---

# Skill: programmer-debugging

## Purpose

Fix the root cause. Not the symptom.
Every wasted guess introduces a new risk. Always diagnose before fixing.

## When to Use

When a command fails, a test fails, a type error appears, or behavior is wrong.
Use this before attempting any fix.

---

## Diagnosis Protocol

### Step 1 — Read the full error, word for word

Do not skim. Error messages contain:
- The exact file and line number
- The exact condition that failed
- Expected vs actual values
- Stack trace showing the execution path

Read from the **bottom of the stack trace** upward — the actual cause is usually at the bottom.

### Step 2 — Go to the exact location

Open the file. Go to the exact line. Read 15–20 lines before and after.
Understand the context.

### Step 3 — Form one hypothesis

Based on the error and the code, form **one specific hypothesis**.

Write it out: "I think the error is caused by X because Y."

If you cannot form a specific hypothesis, re-read the error. The error message tells you.

### Step 4 — Verify before fixing

Ask:
- Can you reproduce the error consistently?
- Does the observed behavior match your hypothesis?
- Is your fix implied directly by the error, or are you inferring?

If you are inferring (guessing), re-read the error. Dig deeper.

### Step 5 — One targeted fix

Change the minimum code necessary to address the root cause.
One change. Not two.

### Step 6 — Verify the fix

Re-run the failing command. Confirm the error is gone.
Then run a broader check to confirm no new failures:

```bash
# JavaScript / TypeScript
npm run lint && npx tsc --noEmit && npm test

# Python
python -m flake8 . && python -m mypy . && python -m pytest

# Go
go vet ./... && go test ./...

# Rust
cargo clippy && cargo test
```

If a new failure appears, stop. Do not fix the new failure without diagnosing it separately.

---

## Language-Specific Failure Patterns

### TypeScript type errors

```
Type 'X' is not assignable to type 'Y'
```

- The error tells you exactly which types are incompatible
- Read the function signature and the type of the value being passed
- Check if the function was recently changed (git diff)
- **Never use `as any` to suppress — fix the underlying type mismatch**
- Check if a type needs to be widened, narrowed, or if an interface needs updating

```
Object is possibly 'undefined'
```
- A value might be undefined before use
- Add a null check: `if (!value) return ...`
- Or use optional chaining: `value?.property`
- Do not use `!` (non-null assertion) unless you are certain — it suppresses the compiler

### Module not found

```
Cannot find module './utils' or its corresponding type declarations
```

- Check the exact path (case-sensitive on Linux/Mac, not Windows)
- Verify the file exists at that path: `ls src/utils*`
- Check if it was supposed to be created in a previous task
- Check if the extension matters: `.ts` vs `.js` vs no extension

### Test failures

```
Expected: "foo"
Received: "bar"
```

- Read which assertion failed and what the expected vs actual values are
- Check if the test is testing the right thing (the test might be wrong)
- Check if a previous task this test depends on was completed
- Run just this one test to isolate: `npx vitest run --reporter=verbose src/foo.test.ts`

### Build failures — missing peer dependency

```
Cannot find module 'react' from 'node_modules/...'
```

- Install the missing dependency: `npm install react`
- Or check if the version mismatch is causing the issue

### Port already in use

```
Error: listen EADDRINUSE: address already in use :::3000
```

**Windows:**
```powershell
netstat -ano | findstr :3000
# Get the PID from the last column
taskkill /PID <pid> /F
```

**Linux / Mac:**
```bash
lsof -ti :3000 | xargs kill -9
# or use a different port
```

### Python ImportError

```
ModuleNotFoundError: No module named 'X'
```

- Verify the module is installed: `pip show X`
- Check virtual environment is activated
- Install if missing: `pip install X`

### Go build errors

```
undefined: SomeFunction
```

- Check the function exists in the package: `grep -rn "func SomeFunction" .`
- Check the import path is correct
- Run `go mod tidy` to sync dependencies

---

## When to Stop and Report

Stop debugging and escalate to the Planner if:

1. The fix requires modifying a file not on the allowed list
2. The fix requires changing the contract requirements
3. You have tried **one targeted fix** and it did not work
4. The error reveals a design problem that requires re-planning
5. The error is in a dependency outside your control

**Report:**
- Exact failing command and its full output
- File and line where the error occurs
- Your hypothesis (what you think is causing it)
- What you tried (your one fix and its result)
- Why you are stopping (which condition above applies)

---

## Anti-patterns (never do these)

- `// @ts-ignore` or `// @ts-nocheck` — suppresses type safety
- Catching and discarding errors: `try { ... } catch (e) {}` 
- `|| true` to make a command appear to exit 0
- Commenting out failing tests to make the suite "pass"
- `any` in TypeScript to bypass type checking
- `2>/dev/null` to hide error output
- `--force` flags without understanding why the check is failing

---

## Safety Rules

- One diagnosis → one hypothesis → one fix → verify.
- Never suppress the error without understanding it.
- Never retry the same fix. If it didn't work, your hypothesis is wrong.
- If you have fixed the same type of error twice in the same session, step back and look for the common root cause.
