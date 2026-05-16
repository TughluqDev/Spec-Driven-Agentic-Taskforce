# HOW_TO_USE.md — Practical Guide to the Planner-Led Agent System

---

## What is this system?

A **Planner-led multi-agent Claude Code workflow** that divides engineering work into five specialist agents, each with a dedicated model, instructions, and toolset:

| Agent | Model | Role |
|-------|-------|------|
| **Planner** | Opus 4.7 | Orchestrates all work. Plans, delegates, gates, summarizes. |
| **Programmer** | Sonnet 4.6 | Implements code from a contract. Nothing else. |
| **Tester** | Sonnet 4.6 | Runs real checks: unit tests, API requests, browser checks. |
| **Reviewer** | Opus 4.7 | Reviews code, security, risks, and test evidence. |
| **Documentor** | Sonnet 4.6 | Writes feature docs and a PR message. |

The Planner is the entry point. You always talk to the Planner.

---

## Folder structure

```
CLAUDE.md                   — Project-wide rules (all agents read this)
AGENTS.md                   — Human-readable agent team reference
HOW_TO_USE.md               — This file

.claude/
├── settings.json           — Claude Code permissions (auto-allow list)
├── agents/
│   ├── planner.md          — Planner (Opus 4.7)
│   ├── programmer.md       — Programmer (Sonnet 4.6)
│   ├── tester.md           — Tester (Sonnet 4.6)
│   ├── reviewer.md         — Reviewer (Opus 4.7)
│   └── documentor.md       — Documentor (Sonnet 4.6)
│
├── skills/                 — Detailed how-to instructions for each agent
│   ├── planner-project-intake/
│   ├── planner-master-plan/
│   ├── planner-clarifying-questions/
│   ├── planner-implementation-contract/
│   ├── planner-test-contract/
│   ├── programmer-implementation/
│   ├── programmer-refactor-safety/
│   ├── programmer-debugging/
│   ├── tester-test-report/
│   ├── tester-playwright-ui-check/
│   ├── tester-api-check/
│   ├── tester-regression-check/
│   ├── reviewer-code-review/
│   ├── reviewer-risk-analysis/
│   └── reviewer-security-check/
│   ├── documentor-feature-doc/
│   └── documentor-pr-message/
│
└── work/                   — Shared workflow artifacts (written/read by agents)
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

## HOW TO ACTIVATE THE SYSTEM

### Method 1 — Sub-agent selector (most reliable)

Claude Code automatically discovers agents from `.claude/agents/*.md`.
To launch the Planner specifically, type this at the start of any prompt:

```
/agent planner
```

Then describe your task. The Planner will orchestrate everything from there.

### Method 2 — Activation phrase in your prompt

Include this phrase at the top of your request:

```
Use the planner-led workflow.
```

Claude will recognize this phrase and activate the Planner agent.

### Method 3 — Full activation prompt (copy-paste ready)

```
Use the planner-led workflow.

Goal:
[Describe your feature or bug fix here]

Required flow:
1. Planner reads CLAUDE.md and the project, then writes planning artifacts.
2. Planner asks only blocking clarification questions.
3. Planner delegates to programmer, tester, reviewer, documentor in that order.
4. Planner gives me a final summary.
```

**Any of the three methods works.** Method 3 is the most explicit.

---

## Quick start — your first real task

1. Open a terminal in this project directory
2. Run: `claude`
3. Type this prompt (replace the goal with your actual task):

```
Use the planner-led workflow.

Goal:
Add a health check endpoint GET /api/health that returns {"status": "ok"}.

Required flow:
1. Planner creates PROJECT_BRIEF, MASTER_PLAN, IMPLEMENTATION_CONTRACT,
   TEST_CONTRACT, TASKS, DECISIONS.
2. Ask me any blocking questions first.
3. Delegate implementation → test → review → document.
4. Give me the final summary.
```

The system will:
- Ask you BLOCKING questions (if any) in one batch
- Build the implementation plan
- Write the code (Programmer)
- Test it with real HTTP requests (Tester)
- Review code quality and security (Reviewer)
- Write docs and a PR message (Documentor)
- Return a final summary to you

---

## Running the dry-run system check

Before using the system for real work, verify it is set up correctly.

```
Use the planner-led workflow.

Goal:
Do a dry run only. Do NOT modify any production source code.

Steps:
1. Read CLAUDE.md, AGENTS.md, and HOW_TO_USE.md.
2. Inspect .claude/agents/ and .claude/skills/.
3. Confirm all 5 agents exist with their model assignments.
4. Confirm all 17 skills are present.
5. Write a readiness report to .claude/work/AGENT_SYSTEM_READINESS.md.
```

Expected output: `.claude/work/AGENT_SYSTEM_READINESS.md` with a READY verdict.

---

## How clarification questions work

The Planner classifies every unknown:

| Class | Behavior |
|-------|----------|
| **BLOCKING** | Asked in one batch. You must answer to proceed. |
| **NON-BLOCKING** | Recorded as assumption in DECISIONS.md. No answer needed. |
| **NICE-TO-HAVE** | Deferred completely. Not asked. |

**To skip a question:** Reply "Make a reasonable assumption and continue."  
**If you want no questions at all:** Add "Make all assumptions needed and record them in DECISIONS.md." to your prompt.

---

## How each agent uses its model

| Agent | Why that model |
|-------|---------------|
| Planner (Opus 4.7) | Needs deep reasoning to synthesize requirements, detect ambiguities, write precise contracts, and manage the multi-step workflow |
| Programmer (Sonnet 4.6) | Excellent at code generation; follows precise instructions; fast |
| Tester (Sonnet 4.6) | Systematic verification; good at structured command execution and reporting |
| Reviewer (Opus 4.7) | Needs nuanced judgment for security issues, risk assessment, and subtle correctness bugs |
| Documentor (Sonnet 4.6) | Strong at structured writing; fast; cost-effective for documentation |

---

## How the Tester does backend API checks

The Tester sends real HTTP requests and records real responses.

**On Linux / Mac:**
```bash
curl -s -w "\n[HTTP %{http_code}]" \
  -X POST http://localhost:3000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Buy groceries"}'
```

**On Windows (PowerShell):**
```powershell
try {
  $resp = Invoke-WebRequest -Uri "http://localhost:3000/api/tasks" `
    -Method POST -ContentType "application/json" `
    -Body '{"title": "Buy groceries"}' -UseBasicParsing
  "Status: $($resp.StatusCode)"
  $resp.Content
} catch {
  "Status: $($_.Exception.Response.StatusCode.value__)"
  $_.ErrorDetails.Message
}
```

The Tester records: method, URL, request body, **actual** status code, **actual** response body.
It never fabricates results. If the server is not running, it marks the check FAIL.

---

## How to set up Playwright MCP (browser testing)

Add this to your Claude Code MCP configuration (`~/.claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"],
      "type": "stdio"
    }
  }
}
```

Restart Claude Code. The Tester will automatically use it when UI checks are needed.

If Playwright MCP is not available, the Tester writes manual testing steps and marks browser checks as `MANUAL-REQUIRED` — not FAIL.

---

## Retry logic and failure handling

The Planner has a **retry budget of 2 cycles per phase**:

- If the Tester returns FAIL → Planner fixes the implementation and retries (up to 2 times)
- If the Reviewer returns FAIL → Planner fixes and retries (up to 2 times)
- On 3rd failure → Planner **stops and escalates to you** with the full failure chain

This prevents infinite loops while still recovering from common failures automatically.

---

## How to use the system for different task types

### New feature
```
Use the planner-led workflow.
Goal: Add [feature description].
Full pipeline: plan → implement → test → review → document.
```

### Bug fix
```
Use the planner-led workflow.
Goal: Fix [describe the bug]. Expected: [what should happen].
Full pipeline: plan → implement → test → review → document.
```

### Planning only (no code)
```
Use the planner-led workflow.
Goal: Plan only. Do not implement.
I want to [describe what you want]. Write the MASTER_PLAN and IMPLEMENTATION_CONTRACT.
Stop before delegating to programmer.
```

### Code review only
```
Use the planner-led workflow.
Goal: Review only. Do not implement or test.
Review the current git diff against .claude/work/IMPLEMENTATION_CONTRACT.md.
Write REVIEW_REPORT.md with a PASS or FAIL verdict.
```

---

## How to copy this system into another project

1. Copy these to the root of the new project:
   ```
   CLAUDE.md
   AGENTS.md
   HOW_TO_USE.md
   .claude/
   ```

2. Clear the work artifacts:
   ```bash
   # Windows PowerShell
   Remove-Item .claude\work\*.md

   # Linux / Mac
   rm .claude/work/*.md
   ```

3. Update `CLAUDE.md` if the new project has different coding standards.

4. Run the dry-run check to verify everything is working.

---

## How to reset .claude/work/ between features

```bash
# Windows PowerShell
Remove-Item .\.claude\work\PROJECT_BRIEF.md, `
  .\.claude\work\MASTER_PLAN.md, `
  .\.claude\work\IMPLEMENTATION_CONTRACT.md, `
  .\.claude\work\TEST_CONTRACT.md, `
  .\.claude\work\TEST_REPORT.md, `
  .\.claude\work\REVIEW_REPORT.md, `
  .\.claude\work\FEATURE_DOC.md, `
  .\.claude\work\PR_MESSAGE.md, `
  .\.claude\work\TASKS.md, `
  .\.claude\work\DECISIONS.md `
  -ErrorAction SilentlyContinue

# Linux / Mac
rm -f .claude/work/{PROJECT_BRIEF,MASTER_PLAN,IMPLEMENTATION_CONTRACT,TEST_CONTRACT,TEST_REPORT,REVIEW_REPORT,FEATURE_DOC,PR_MESSAGE,TASKS,DECISIONS}.md
```

---

## Common problems and fixes

| Problem | Fix |
|---------|-----|
| Claude writes code immediately without planning | Add "Use the planner-led workflow" to your prompt |
| Planner asks too many questions | Reply: "Make reasonable assumptions and record them in DECISIONS.md" |
| Tester claims PASS without showing output | Reply: "Show me the exact command output" |
| Programmer modified a file not in the contract | Report: "The programmer modified `<file>` which is not in the allowed list" |
| Browser tests not running | Playwright MCP not configured — see setup section above |
| Reviewer is too strict / blocking on minor issues | Reply: "Accept the RECOMMENDED findings as non-blocking and continue" |
| Planner not delegating to sub-agents | Ensure your prompt includes "Required flow: ... delegate to programmer/tester/reviewer/documentor" |

---

## Local model support (Qwen 3B / 7B)

The system supports local models via Ollama. For local models, change the `model:` field in each agent file to `inherit`, then start Claude Code with:

```bash
claude --model qwen2.5-coder:7b
```

Tips for local models:
- Use 7B minimum for the Planner and Reviewer (need more reasoning)
- Keep prompts short and specific
- One feature at a time — no chained requests
- Clear `.claude/work/` between features to avoid context overflow
- If a model fails to follow the contract, simplify the contract task descriptions
