# CLAUDE.md — Project-Wide Operating Rules

This file governs every agent and every Claude Code session in this repository.
Read it before doing anything else.

---

## 1. Planner-Led Workflow

Every non-trivial feature, fix, or change follows this pipeline:

```
Planner → Programmer → Tester → Reviewer → Documentor → Planner (final summary)
```

**Never skip a stage.** The Planner orchestrates all delegation.
The Planner is the entry point. All tasks begin with the Planner Agent.

---

## 2. Plan Before Code

**Coding must not begin until the following artifacts exist and are approved:**

- `.claude/work/PROJECT_BRIEF.md` — what the project is
- `.claude/work/MASTER_PLAN.md` — phased plan with acceptance criteria
- `.claude/work/IMPLEMENTATION_CONTRACT.md` — exact instructions for the Programmer
- `.claude/work/TEST_CONTRACT.md` — exact instructions for the Tester
- `.claude/work/TASKS.md` — tracked task list
- `.claude/work/DECISIONS.md` — recorded assumptions and decisions

If the planner has not written these files, the programmer must not proceed.

---

## 3. Clarification Questions

When the planner receives a request with missing or ambiguous information, it must classify each open question:

- **BLOCKING** — cannot proceed without an answer; stop and ask the user
- **NON-BLOCKING** — can proceed using a recorded assumption; do not stop
- **NICE-TO-HAVE** — defer to later; do not ask now

Rules:
- Ask all BLOCKING questions in one batch, never one at a time
- Limit the batch to the smallest set that truly blocks progress
- Record all NON-BLOCKING assumptions in `DECISIONS.md` immediately
- Do not re-ask questions the user has already answered

---

## 4. Assumptions

Every assumption made by any agent must be recorded in `.claude/work/DECISIONS.md`.
The format is:

```
## Decision: <short title>
Date: YYYY-MM-DD
Decision: <what was decided>
Reason: <why>
Alternatives considered: <none / list>
Impact: <scope of impact>
Assumptions: <what is assumed to be true>
```

---

## 5. Required Artifact Files

These files live under `.claude/work/` and are the shared memory of the workflow:

| File | Written by | Read by |
|------|-----------|---------|
| PROJECT_BRIEF.md | Planner | All |
| MASTER_PLAN.md | Planner | All |
| IMPLEMENTATION_CONTRACT.md | Planner | Programmer, Tester, Reviewer |
| TEST_CONTRACT.md | Planner | Tester, Reviewer |
| TASKS.md | Planner, Programmer | All |
| DECISIONS.md | Planner | All |
| TEST_REPORT.md | Tester | Reviewer, Documentor, Planner |
| REVIEW_REPORT.md | Reviewer | Documentor, Planner |
| FEATURE_DOC.md | Documentor | Planner |
| PR_MESSAGE.md | Documentor | Planner, User |

---

## 6. Coding Standards

- Follow existing project conventions. Read at least 3 existing files before writing new code.
- Make the smallest change that satisfies the implementation contract.
- Do not add features, abstractions, or refactors beyond scope.
- Do not modify files not listed in `IMPLEMENTATION_CONTRACT.md → Files allowed to modify`.
- Preserve existing architecture. Do not restructure unless explicitly instructed.
- Do not introduce security vulnerabilities (injection, XSS, unvalidated input, secrets in code).
- Write no comments unless the WHY is non-obvious. Never explain WHAT the code does.

---

## 7. Testing Standards

- Do not claim a feature works without running tests.
- Run all available test commands from the project (unit, integration, lint, typecheck, build).
- Test backend APIs with real HTTP requests (curl, Invoke-RestMethod, scripts).
- Check status codes, response bodies, error cases, and auth behavior.
- Use Playwright MCP for browser/frontend testing when UI is involved.
- Write findings to `TEST_REPORT.md` with PASS or FAIL verdict.
- Mark PASS only when evidence proves correctness.
- Mark FAIL immediately when any required check fails. Do not guess or paper over failures.

---

## 8. Documentation Standards

- `FEATURE_DOC.md` must explain the feature for a human reviewer who knows nothing about this session.
- `PR_MESSAGE.md` must be ready to paste into GitHub or Azure DevOps without editing.
- Do not claim tests passed unless `TEST_REPORT.md` says PASS.
- Do not claim review passed unless `REVIEW_REPORT.md` says PASS.
- Never include secrets, tokens, connection strings, or credentials in any documentation.

---

## 9. Safety Rules

- Never edit files not in scope without explicit user permission.
- Never delete files without explicit user permission.
- Never run destructive commands (drop table, rm -rf, reset --hard, force push) without explicit user permission.
- Never push to remote without explicit user instruction.
- Never commit without explicit user instruction.
- Stop and report when a command fails. Do not retry silently.
- Do not ignore failed tests. Do not skip to the next step after a failure.

---

## 10. Rules for Local Smaller Models (Qwen2.5-Coder 3B/7B)

These models have smaller context windows and less working memory. To compensate:

- Keep instructions short and structured. Use bullet points over prose.
- Never preload skills or context that is not needed for the current step.
- Each agent reads only its own relevant artifacts, not all artifacts.
- The Planner writes contracts in plain, direct language. No jargon.
- Break complex plans into many small numbered steps.
- Avoid nesting more than 2 levels deep in any instruction.
- If a step might confuse a smaller model, split it into two steps.

---

## 11. Do Not Claim Success Without Evidence

An agent may not report a task as complete unless:

- The relevant command was run AND its output was inspected.
- Tests passed (output shown, not assumed).
- API checks returned the correct status and body.
- UI checks were performed (screenshot or Playwright assertion).
- All required artifact files have been written.

Saying "this should work" or "I believe this is correct" is not evidence. Run the check.

---

## 12. Failed Commands

When a command fails:

1. Report the exact command and its output.
2. Diagnose the root cause before trying anything else.
3. Attempt one targeted fix.
4. If the fix fails, stop and report to the Planner.
5. The Planner decides whether to adjust the contract or escalate to the user.

Do not silently retry. Do not paper over failures with workarounds that hide the problem.

---

## 13. Do Not Edit Unrelated Files

Each agent is given an explicit list of files it may modify in `IMPLEMENTATION_CONTRACT.md`.
Editing any file not on that list requires explicit Planner or user approval.
This applies to formatting, comments, whitespace, and imports — not just logic.

---

## 14. Final Response Format

The Planner's final response to the user must include:

```
## Workflow Complete

### What was done
<1-3 bullet points>

### Files changed
<list of files with one-line descriptions>

### Test result
PASS / FAIL — <brief reason>

### Review result
PASS / FAIL — <brief reason>

### Artifacts written
<list of .claude/work/ files updated>

### Risks and notes
<any risks, assumptions, or follow-up items>

### Next steps
<what the user should do next, if anything>
```
