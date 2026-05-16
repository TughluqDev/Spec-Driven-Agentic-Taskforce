# Agentic Taskforce

A **production-grade multi-agent engineering workflow** with five specialist AI agents collaborate in a structured pipeline to plan, implement, test, review, and document software changes, with built-in quality gates that prevent any agent from claiming success without real evidence.

---

## How it works

Every task flows through a fixed pipeline. No stage can be skipped.

```
User Request
     │
     ▼
┌─────────────┐
│   PLANNER   │  Orchestrates everything. Asks questions, writes contracts, gates progress.
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│   PROGRAMMER    │  Implements code — only from the contract, nothing else.
└────────┬────────┘
         │
         ▼
┌──────────────┐
│    TESTER    │  Runs real checks: unit tests, API calls, browser automation.
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   REVIEWER   │  Code review, security audit, risk analysis.
└──────┬───────┘
       │
       ▼
┌──────────────────┐
│   DOCUMENTOR     │  Feature doc + PR message, ready to paste into GitHub.
└──────────────────┘
       │
       ▼
┌─────────────┐
│   PLANNER   │  Final summary back to the user.
└─────────────┘
```

The Planner is the **only** entry point. You always talk to the Planner.

---

## The agents

| Agent | Model | What it does | What it cannot do |
|-------|-------|--------------|-------------------|
| **Planner** | Opus 4.7 | Orchestrates all work. Plans, delegates, enforces quality gates. | Write production code. Skip any stage. |
| **Programmer** | Sonnet 4.6 | Implements code from a contract exactly as specified. | Invent requirements. Modify out-of-scope files. Claim the feature works. |
| **Tester** | Sonnet 4.6 | Runs unit tests, integration tests, real HTTP API calls, Playwright browser checks. | Claim PASS without running the actual check and seeing real output. |
| **Reviewer** | Opus 4.7 | Checks code correctness, maintainability, security (OWASP Top 10), and architectural risk. | Approve work when tests have failed. Ignore security findings. |
| **Documentor** | Sonnet 4.6 | Writes FEATURE_DOC.md and a PR message ready for GitHub/Azure DevOps. | Claim tests or review passed unless the actual report says so. |

---

## Quality gates

Every gate is enforced — not suggested.

| Gate | Enforced by | What blocks progress |
|------|-------------|----------------------|
| Plan before code | Planner | No implementation until 6 artifact files exist |
| Evidence-only PASS | Tester | No PASS without real command output |
| No ship with failing tests | Reviewer | Cannot approve if TEST_REPORT.md says FAIL |
| No undocumented security issues | Reviewer | Any security finding is REQUIRED severity |
| Retry budget | Planner | Max 2 retries per phase, then escalate to user |

---

## Artifacts written per workflow run

All artifacts live in `.claude/work/` and form the shared memory of the pipeline.

| File | Written by | Purpose |
|------|-----------|---------|
| `PROJECT_BRIEF.md` | Planner | What the project is, its stack, entry points |
| `MASTER_PLAN.md` | Planner | Phased plan with acceptance criteria and risk table |
| `IMPLEMENTATION_CONTRACT.md` | Planner | Exact step-by-step instructions for the Programmer |
| `TEST_CONTRACT.md` | Planner | Exact checks, commands, and pass/fail criteria for the Tester |
| `TASKS.md` | Planner | Tracked task list with owner and status |
| `DECISIONS.md` | Planner | Every assumption recorded with reason and impact |
| `TEST_REPORT.md` | Tester | Real test output with PASS / FAIL verdict |
| `REVIEW_REPORT.md` | Reviewer | Code review findings with PASS / FAIL verdict |
| `FEATURE_DOC.md` | Documentor | Human-readable explanation of what changed and why |
| `PR_MESSAGE.md` | Documentor | PR title and body, paste-ready for GitHub or Azure DevOps |

---

## Skills system

Each agent loads a set of **skills** — detailed instruction sets that govern exactly how to perform specialized tasks. Skills prevent agents from improvising in high-stakes moments.

| Agent | Skills |
|-------|--------|
| Planner | `project-intake`, `master-plan`, `clarifying-questions`, `implementation-contract`, `test-contract` |
| Programmer | `implementation`, `refactor-safety`, `debugging` |
| Tester | `test-report`, `playwright-ui-check`, `api-check`, `regression-check` |
| Reviewer | `code-review`, `risk-analysis`, `security-check` |
| Documentor | `feature-doc`, `pr-message` |

---

## Getting started

### 1. Start Claude Code

```bash
claude                                  # Cloud Claude (recommended)
claude --model qwen2.5-coder:7b         # Local Ollama model
```

### 2. Activate the Planner

```
/agent planner
```

Or prefix any prompt with:

```
Use the planner-led workflow.
```

### 3. Send your request

```
Use the planner-led workflow.

Goal:
Add a /health endpoint that returns { status: "ok" } and the current server uptime.

Required flow:
1. Planner creates PROJECT_BRIEF, MASTER_PLAN, IMPLEMENTATION_CONTRACT, TEST_CONTRACT, TASKS, DECISIONS.
2. Planner asks blocking clarification questions if needed.
3. Planner delegates implementation to programmer.
4. Planner delegates verification to tester.
5. Tester uses backend API checks if applicable.
6. Tester uses Playwright MCP if frontend behavior is involved.
7. Planner delegates review to reviewer.
8. Planner delegates documentation and PR message to documentor.
9. Planner gives me the final summary.
```

The Planner handles everything from there.

---

## Clarification question protocol

The Planner classifies every unknown before starting work:

| Class | Meaning | Action |
|-------|---------|--------|
| **BLOCKING** | Cannot write a valid plan without an answer | Stops and asks — all at once, never one at a time |
| **NON-BLOCKING** | Safe assumption exists | Records assumption in `DECISIONS.md`, continues |
| **NICE-TO-HAVE** | Not critical now | Defers entirely, does not ask |

You will never be pestered with optional questions. If the Planner stops, it genuinely cannot proceed without your answer.

---

## What "evidence-only" means

No agent may claim a task is complete by saying "this should work" or "I believe it's correct."

Every PASS requires:
- The actual command that was run
- The actual output of that command
- For API checks: method, URL, status code, and response body
- For browser checks: Playwright assertions or a screenshot

A `TEST_REPORT.md` that says PASS without this evidence is invalid and the Reviewer will reject it.

---

## Folder structure

```
CLAUDE.md                   — Project-wide rules governing all agents
AGENTS.md                   — Human-readable agent team reference
HOW_TO_USE.md               — Full setup and usage guide
README.md                   — This file

.claude/
├── settings.json           — Claude Code permissions
├── agents/
│   ├── planner.md          — Planner (Opus 4.7) — orchestrator
│   ├── programmer.md       — Programmer (Sonnet 4.6) — code only
│   ├── tester.md           — Tester (Sonnet 4.6) — verification
│   ├── reviewer.md         — Reviewer (Opus 4.7) — quality gate
│   └── documentor.md       — Documentor (Sonnet 4.6) — docs + PR
│
├── skills/                 — 17 skill files, one per specialized task
│   ├── planner-*/
│   ├── programmer-*/
│   ├── tester-*/
│   ├── reviewer-*/
│   └── documentor-*/
│
└── work/                   — Workflow artifacts (written and read by agents)
    ├── PROJECT_BRIEF.md
    ├── MASTER_PLAN.md
    ├── IMPLEMENTATION_CONTRACT.md
    ├── TEST_CONTRACT.md
    ├── TEST_REPORT.md
    ├── REVIEW_REPORT.md
    ├── FEATURE_DOC.md
    ├── PR_MESSAGE.md
    ├── TASKS.md
    └── DECISIONS.md
```

---

## Copying this system into another project

1. Copy `CLAUDE.md`, `AGENTS.md`, and `HOW_TO_USE.md` to the project root.
2. Copy the `.claude/` folder (agents, skills, work templates, settings).
3. Start Claude Code in the new project directory.
4. Run the dry-run check to verify the system is intact:

```
Use the planner-led workflow.

Goal:
Do a dry run only. Do not modify production source code.
Check whether the agent system is usable and write a readiness report to .claude/work/AGENT_SYSTEM_READINESS.md.
```

---

## System status

The system has been validated with a full dry-run check. Verdict: **PASS**

- 5 agent files — all present, valid YAML frontmatter, correct tool and skill assignments
- 17 skill files — all present and non-empty
- 10 work artifact templates — all present
- Planner confirmed as sole orchestrator with Agent tool access
- Programmer confirmed without Agent tool (cannot delegate — by design)
- Tester confirmed with backend API check instructions (curl + PowerShell) and Playwright MCP fallback
- Reviewer confirmed with security check skill covering OWASP Top 10
- Documentor confirmed with honest PASS/FAIL rules enforced

See [`.claude/work/AGENT_SYSTEM_READINESS.md`](.claude/work/AGENT_SYSTEM_READINESS.md) for the full report.

---

## Design principles

**Specialization over generalism.** Each agent does one job. A Programmer that also reviews its own work is a conflict of interest. Separating roles catches what a single agent misses.

**Contracts prevent drift.** The Planner writes an exact implementation contract before any code is written. The Programmer cannot add features the contract doesn't mention. The Tester cannot skip checks the contract requires.

**Evidence prevents hallucination.** Agents cannot self-certify. The Tester produces a report with real output. The Reviewer reads that report before approving. Nothing ships on the agent's word alone.

**Assumptions are recorded, not hidden.** Every NON-BLOCKING decision the Planner makes goes into `DECISIONS.md` with a reason. The user can inspect every judgment call the system made.
