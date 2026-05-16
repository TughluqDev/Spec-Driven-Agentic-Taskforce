---
name: planner-master-plan
description: >
  Teaches the Planner how to write a complete, phased MASTER_PLAN.md with
  scope, phases, acceptance criteria, risks, and success criteria. The master
  plan governs all subsequent contracts and delegation.
---

# Skill: planner-master-plan

## Purpose

Write a concrete, phased plan that defines exactly what will be built, why,
and how success will be measured. This plan is the source of truth for all
implementation and test contracts.

## When to Use

After project intake and clarification questions are resolved.
Before writing any implementation or test contract.

## Input

- `.claude/work/PROJECT_BRIEF.md`
- `.claude/work/DECISIONS.md` (resolved assumptions)
- User's full request

## Output

`.claude/work/MASTER_PLAN.md`

---

## Process

### Step 1 — State the goal

One sentence. What does this work achieve for the user or system?

### Step 2 — Document current understanding

Describe what you know about the codebase as it exists today:
- What is already implemented?
- What is missing?
- What needs to change?

Base this on the project brief. Do not invent.

### Step 3 — Define scope

List exactly what is included in this work.
Then list exactly what is excluded.
Be specific. Vague scope leads to scope creep.

### Step 4 — Break into phases

Each phase should:
- Be completable independently
- Have a clear output
- Be testable on its own

Number the phases. Order them by dependency (earlier phases must complete before later ones start).

### Step 5 — Write acceptance criteria

For each phase, write the conditions that prove it is done.
These become the inputs to the test contract.

Use this format:
```
Given <context>, when <action>, then <expected result>.
```

### Step 6 — Identify risks

List risks with:
- Description of the risk
- Likelihood (LOW / MEDIUM / HIGH)
- Impact (LOW / MEDIUM / HIGH)
- Mitigation strategy

### Step 7 — Define success criteria

How do you know the entire feature is successful?
This goes beyond passing tests — it includes UX correctness, performance, and reliability.

---

## Output Format: MASTER_PLAN.md

```markdown
# Master Plan

## Goal
<one sentence>

## Current understanding
<what exists today, what is missing>

## Scope

### In scope
- <item>

### Out of scope
- <item>

## Phases

### Phase 1: <name>
**Goal:** <what this phase achieves>
**Steps:**
1. <step>
2. <step>
**Acceptance criteria:**
- Given ..., when ..., then ...

### Phase 2: <name>
...

## Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| <risk> | HIGH | MEDIUM | <mitigation> |

## Success criteria
- <criterion>

## Open questions
- <question> — status: OPEN / RESOLVED
```

---

## Quality Checklist

- [ ] Goal is one clear sentence
- [ ] Scope explicitly excludes things that might be assumed
- [ ] Each phase has numbered steps
- [ ] Each phase has measurable acceptance criteria
- [ ] Risks are listed with mitigation strategies
- [ ] Success criteria go beyond "tests pass"

## Safety Rules

- Do not plan work that goes beyond the user's request.
- Do not invent requirements.
- Do not add phases for "future improvements" unless the user asked for them.
