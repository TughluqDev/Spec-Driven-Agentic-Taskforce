---
name: programmer-refactor-safety
description: >
  Teaches the Programmer how to avoid unnecessary refactors, preserve existing
  architecture, and keep changes minimal. Covers the specific risks of over-engineering
  and how to recognize when a refactor is out of scope.
---

# Skill: programmer-refactor-safety

## Purpose

Keep changes small and targeted. Prevent the Programmer from "improving"
code that is not in scope, which creates review burden, regression risk,
and scope creep.

## When to Use

Before starting any implementation. Especially when you notice code nearby
that looks like it could use improvement.

## Input

- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- Source files you are about to modify

## Output

A narrowly-scoped implementation that changes only what the contract requires.

---

## The Core Rule

**If the contract does not say to change it, do not change it.**

This applies to:
- Variable names
- Function signatures (beyond what is required)
- Import organization
- Comment style
- File structure
- Error handling patterns (use existing ones)
- Abstractions (use existing ones)

---

## How to Recognize Out-of-Scope Refactors

You are about to do an out-of-scope refactor if you think:
- "This function is too long, I'll split it up while I'm here"
- "This naming is inconsistent, I'll clean it up"
- "This could be a shared utility"
- "I'll just reorganize these imports"
- "This would be cleaner if I extracted a class"
- "I'll upgrade this deprecated API while I'm in this file"

None of these are in scope unless the contract says so. Stop. Do only what the contract says.

---

## When Refactoring IS Required

The contract may require refactoring. Signs:
- "Extract X into a shared function"
- "Move this logic to a service layer"
- "Replace direct DB access with the repository pattern"

In these cases, the refactor is the task. Still keep it narrow.

---

## Checklist Before Every Edit

1. Is this file in "Files allowed to modify"?
2. Is this specific change required by a task in the contract?
3. Am I changing something outside the task to make my task easier?
   (If yes, stop. Find a way to do the task without the extra change.)

---

## What to Do With Debt You Notice

When you notice technical debt, security issues, or improvement opportunities
in files you are working in:

1. Do not fix them now.
2. Note them in your implementation report under "Issues encountered".
3. The Planner can create a separate task if appropriate.

---

## Safety Rules

- A refactor that is not in the contract is not a safe refactor.
- Every line you change is a line the reviewer must check.
- Every file you touch is a file that could break.
- Discipline here reduces bugs, review time, and rollback risk.