---
name: programmer-debugging
description: >
  Teaches the Programmer how to diagnose failing commands, error messages,
  broken tests, and runtime issues without guessing. Covers systematic diagnosis
  before attempting fixes.
---

# Skill: programmer-debugging

## Purpose

Fix the right thing. Guessing at fixes wastes time and introduces new bugs.
Always diagnose before attempting a fix.

## When to Use

When a command fails, a test fails, or the implementation does not behave as expected.

## Input

- The failing command and its full output
- The file(s) involved
- The contract requirement that is failing

## Output

A targeted fix that addresses the root cause, or a clear report to the Planner
if the fix is outside scope.

---

## Diagnosis Process

### Step 1 — Read the full error message

Read every line. Do not skim. Error messages often contain:
- The exact file and line number
- The exact condition that failed
- The expected vs actual value

### Step 2 — Locate the failing code

Go to the exact file and line the error mentions.
Read the surrounding 20 lines for context.

### Step 3 — Form a hypothesis

Based on the error and the code, form one specific hypothesis about the cause.
Do not form multiple hypotheses. Pick the most likely one.

### Step 4 — Verify the hypothesis

Before fixing:
- Can you reproduce the error consistently?
- Does the error match your hypothesis?
- Is the fix implied directly by the error, or are you guessing?

If you are guessing, re-read the error. Your hypothesis may be wrong.

### Step 5 — Apply one targeted fix

Change the minimum amount of code to fix the root cause.
Do not "fix" multiple things at once.

### Step 6 — Verify the fix

Re-run the failing command. Confirm the error is gone.
Check that you have not introduced a new failure:
```bash
npm run lint && npm run typecheck && npm test
```

---

## Common Failure Patterns

### TypeScript type error
- Read the type error exactly — it tells you what types are incompatible
- Check function signatures and the types of values being passed
- Do not use `any` to suppress errors — fix the underlying type mismatch

### Module not found
- Check the import path (case-sensitive on Linux)
- Check the file actually exists at that path
- Check if it was supposed to be created in a previous task

### Test failure
- Read the exact assertion failure: expected vs received
- Check if the test is correct or if the implementation is wrong
- Check if a previous task that this test depends on was completed

### Command not found
- The project may not have this command
- Check `package.json` scripts or Makefile
- Use the correct command for this project

### Port already in use
- Another process is using the port
- Use a different port for testing or kill the other process

---

## When to Stop and Report

Stop debugging and report to the Planner if:
- The fix requires modifying a file not on the allowed list
- The fix requires changing the contract requirements
- You have tried one targeted fix and it did not work
- The error reveals a design problem that requires re-planning

Report the exact error, your hypothesis, and why you are stopping.

---

## Safety Rules

- Never suppress errors with try/catch, `|| true`, or `2>/dev/null` to make a command appear to pass.
- Never use `any` in TypeScript to bypass type errors.
- Never comment out failing tests.
- One fix at a time. Verify before moving on.