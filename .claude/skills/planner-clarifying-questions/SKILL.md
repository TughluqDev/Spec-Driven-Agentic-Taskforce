---
name: planner-clarifying-questions
description: >
  Teaches the Planner when and how to ask clarification questions. Covers
  classifying questions as BLOCKING, NON-BLOCKING, or NICE-TO-HAVE, and
  how to record assumptions for non-blocking unknowns.
---

# Skill: planner-clarifying-questions

## Purpose

Ask only the questions that genuinely block planning. Record everything else
as an assumption. Never waste the user's time with questions you can answer
yourself by reading the codebase.

## When to Use

After project intake, before writing the master plan.
Also usable mid-workflow if a new genuine blocker appears.

## Input

- User's request
- `.claude/work/PROJECT_BRIEF.md`
- Anything already read from the codebase

## Output

- A list of BLOCKING questions sent to the user (if any)
- Updated `.claude/work/DECISIONS.md` with all non-blocking assumptions

---

## Question Classification

### BLOCKING
Cannot write a valid plan without this answer.

Examples:
- "Should this endpoint require authentication? I cannot write a secure contract without knowing this."
- "Which database are you using? I see both Postgres and SQLite configs and cannot determine which is active."
- "Is this a new feature or a replacement for an existing one? The answer determines whether I modify or create."

Stop the workflow. Ask in one batch.

### NON-BLOCKING
Can proceed with a safe, reasonable assumption. Record the assumption.

Examples:
- Assuming the existing error format for new error responses
- Assuming existing naming conventions apply to new code
- Assuming English locale for display text
- Assuming 200 OK for success unless another pattern is observed

Record in `DECISIONS.md`. Continue.

### NICE-TO-HAVE
Not needed now. Defer completely.

Examples:
- "Would you like dark mode support too?"
- "Should we add rate limiting to this endpoint?"
- "Do you want analytics tracking on this page?"

Do not ask. Do not record. Continue.

---

## Process

### Step 1 — List all unknowns

Write down everything you do not know.

### Step 2 — For each unknown, ask:
- Can I find the answer in the codebase? (If yes → read it, do not ask)
- If I assume X, does the plan stay valid and safe? (If yes → NON-BLOCKING)
- If I get this wrong, does it cause a real problem? (If yes → BLOCKING)

### Step 3 — For BLOCKING questions

Group them. Ask all at once. Keep the list short. Write questions that are:
- Specific (not "how should auth work?" — instead "should GET /api/users require a Bearer token?")
- Binary or multiple-choice when possible
- Self-contained (include context so the user does not need to re-read the code to answer)

### Step 4 — For NON-BLOCKING assumptions

Write each to `DECISIONS.md` immediately:

```markdown
## Decision: <short title>
Date: <today>
Decision: <what was decided>
Reason: <why this assumption is safe>
Alternatives considered: <other options>
Impact: <what this affects>
Assumptions: <what must be true for this to be correct>
```

---

## Quality Checklist

- [ ] Questions that can be answered by reading code were answered by reading code
- [ ] BLOCKING questions are genuinely blocking (not just nice-to-know)
- [ ] All BLOCKING questions are in one batch
- [ ] Each question includes context so the user can answer quickly
- [ ] Every non-blocking assumption is recorded in DECISIONS.md

## Safety Rules

- Never ask for information already in the codebase.
- Never ask more than one round of BLOCKING questions.
- Never let "nice-to-have" questions delay the plan.
- Never assume something that has a security implication — always ask.
