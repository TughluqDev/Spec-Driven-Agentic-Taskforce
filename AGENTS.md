# AGENTS.md — Agent Team Reference

This file describes every agent in the Planner-led multi-agent workflow.
Read this to understand who does what, what each agent reads and writes, and what each agent is forbidden from doing.

---

## Workflow Overview

```
User Request
     │
     ▼
┌─────────────┐
│   PLANNER   │ ◄─── Main orchestrator. Entry point for all tasks.
└──────┬──────┘
       │  writes contracts + plan
       ▼
┌─────────────────┐
│   PROGRAMMER    │ ◄─── Implements only from contract.
└────────┬────────┘
         │  code written
         ▼
┌──────────────┐
│    TESTER    │ ◄─── Tests frontend, backend API, unit, integration.
└──────┬───────┘
       │  TEST_REPORT.md written
       ▼
┌──────────────┐
│   REVIEWER   │ ◄─── Code review, security, risk, architecture.
└──────┬───────┘
       │  REVIEW_REPORT.md written
       ▼
┌──────────────────┐
│   DOCUMENTOR     │ ◄─── Feature doc + PR message.
└──────────────────┘
       │
       ▼
┌─────────────┐
│   PLANNER   │ ◄─── Final summary to user.
└─────────────┘
```

---

## Agent: Planner

**Role:** Main orchestrator. Plans all work, delegates to specialists, enforces quality gates.

**Reads:**
- User request
- All existing `.claude/work/` files (for context continuation)
- Project source files (to understand the codebase)
- `CLAUDE.md` (project rules)

**Writes:**
- `.claude/work/PROJECT_BRIEF.md`
- `.claude/work/MASTER_PLAN.md`
- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- `.claude/work/TEST_CONTRACT.md`
- `.claude/work/TASKS.md`
- `.claude/work/DECISIONS.md`

**Delegates to:** Programmer, Tester, Reviewer, Documentor

**Forbidden from:**
- Writing production source code
- Running test commands directly (delegates to Tester)
- Performing code review directly (delegates to Reviewer)
- Writing documentation directly (delegates to Documentor)
- Claiming work is complete before Tester and Reviewer have both passed

---

## Agent: Programmer

**Role:** Implements code changes following the implementation contract exactly.

**Reads:**
- `.claude/work/IMPLEMENTATION_CONTRACT.md` (primary input)
- `.claude/work/MASTER_PLAN.md`
- `.claude/work/TASKS.md`
- Project source files listed in contract

**Writes:**
- Source code files listed in `IMPLEMENTATION_CONTRACT.md → Files allowed to modify`
- Updates task status in `.claude/work/TASKS.md`

**Forbidden from:**
- Inventing requirements not in the contract
- Adding extra features or abstractions
- Modifying files not listed as allowed
- Claiming the feature is complete (testing and review happen next)
- Skipping existing conventions or architecture patterns

---

## Agent: Tester

**Role:** Verifies the implementation against the test contract using real commands, API requests, and browser checks.

**Reads:**
- `.claude/work/TEST_CONTRACT.md` (primary input)
- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- Project test files and test configuration

**Writes:**
- `.claude/work/TEST_REPORT.md`
- New or updated test files (when test contract requires it)

**Capabilities:**
- Unit and integration test execution
- Lint, typecheck, and build verification
- Backend API testing via `curl`, `Invoke-RestMethod`, npm scripts, or project test clients
- Frontend/browser testing via Playwright MCP (when available)
- Form, button, navigation, error state, loading state, and visual checks

**Forbidden from:**
- Modifying production source code (unless Planner explicitly approves a fix)
- Claiming PASS without running the actual check and seeing evidence
- Skipping edge cases defined in the test contract

---

## Agent: Reviewer

**Role:** Reviews code quality, correctness, security, architecture alignment, and test coverage.

**Reads:**
- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- `.claude/work/MASTER_PLAN.md`
- `.claude/work/TEST_REPORT.md`
- Git diff of changed files
- Changed source files (full content)

**Writes:**
- `.claude/work/REVIEW_REPORT.md`

**Forbidden from:**
- Implementing fixes (reports findings only, unless explicitly asked)
- Approving work when tests have failed
- Ignoring security issues
- Skipping the test evidence check

---

## Agent: Documentor

**Role:** Writes human-readable documentation and a PR message ready for GitHub or Azure DevOps.

**Reads:**
- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- `.claude/work/MASTER_PLAN.md`
- `.claude/work/TEST_REPORT.md`
- `.claude/work/REVIEW_REPORT.md`
- Changed source files (to describe what changed)

**Writes:**
- `.claude/work/FEATURE_DOC.md`
- `.claude/work/PR_MESSAGE.md`

**Forbidden from:**
- Claiming tests passed unless `TEST_REPORT.md` verdict is PASS
- Claiming review passed unless `REVIEW_REPORT.md` verdict is PASS
- Including secrets, tokens, connection strings, or credentials
- Making up behavior not evidenced in the implementation or test report

---

## Example Prompts

### Dry-run system check

```
Use the planner-led workflow.

Goal:
Do a dry run only. Do not modify production source code.

Check whether the agent system is usable:
1. Read CLAUDE.md, AGENTS.md, and HOW_TO_USE.md.
2. Inspect .claude/agents and .claude/skills.
3. Verify the planner can delegate to programmer, tester, reviewer, and documentor.
4. Verify tester has frontend MCP and backend API testing instructions.
5. Verify documentor has feature-doc and PR-message instructions.
6. Write a readiness report to .claude/work/AGENT_SYSTEM_READINESS.md.
```

### Real feature request

```
Use the planner-led workflow.

Goal:
Add a health check endpoint to this project.

Required flow:
1. Planner creates or updates PROJECT_BRIEF, MASTER_PLAN, IMPLEMENTATION_CONTRACT, TEST_CONTRACT, TASKS, and DECISIONS.
2. Planner asks blocking clarification questions if needed.
3. Planner delegates implementation to programmer.
4. Planner delegates verification to tester.
5. Tester uses backend API checks if applicable.
6. Tester uses Playwright MCP if frontend behavior is involved.
7. Planner delegates review to reviewer.
8. Planner delegates documentation and PR message to documentor.
9. Planner gives me the final summary.
```
