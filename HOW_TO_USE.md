# HOW_TO_USE.md — Practical Guide to the Planner-Led Agent System

---

## What is this system?

This repository contains a **Planner-led multi-agent Claude Code workflow**.
Instead of asking Claude to do everything at once, it divides engineering work into five specialist agents:

| Agent | Role |
|-------|------|
| **Planner** | Orchestrates all work. Plans, delegates, gates, summarizes. |
| **Programmer** | Implements code from a contract. Nothing more. |
| **Tester** | Runs real checks: unit tests, API requests, browser checks. |
| **Reviewer** | Reviews code, security, risks, and test evidence. |
| **Documentor** | Writes feature docs and a PR message. |

The Planner is the entry point. You always talk to the Planner.

---

## Folder structure

```
CLAUDE.md                   — Project-wide rules (all agents read this)
AGENTS.md                   — Human-readable agent team reference
HOW_TO_USE.md               — This file

.claude/
├── settings.json           — Claude Code permissions config
├── agents/
│   ├── planner.md          — Planner agent definition
│   ├── programmer.md       — Programmer agent definition
│   ├── tester.md           — Tester agent definition
│   ├── reviewer.md         — Reviewer agent definition
│   └── documentor.md       — Documentor agent definition
│
├── skills/
│   ├── planner-project-intake/SKILL.md
│   ├── planner-master-plan/SKILL.md
│   ├── planner-clarifying-questions/SKILL.md
│   ├── planner-implementation-contract/SKILL.md
│   ├── planner-test-contract/SKILL.md
│   ├── programmer-implementation/SKILL.md
│   ├── programmer-refactor-safety/SKILL.md
│   ├── programmer-debugging/SKILL.md
│   ├── tester-test-report/SKILL.md
│   ├── tester-playwright-ui-check/SKILL.md
│   ├── tester-api-check/SKILL.md
│   ├── tester-regression-check/SKILL.md
│   ├── reviewer-code-review/SKILL.md
│   ├── reviewer-risk-analysis/SKILL.md
│   ├── reviewer-security-check/SKILL.md
│   ├── documentor-feature-doc/SKILL.md
│   └── documentor-pr-message/SKILL.md
│
└── work/
    ├── PROJECT_BRIEF.md        — Project understanding
    ├── MASTER_PLAN.md          — Phased plan
    ├── IMPLEMENTATION_CONTRACT.md  — Exact instructions for Programmer
    ├── TEST_CONTRACT.md        — Exact instructions for Tester
    ├── TEST_REPORT.md          — Tester's findings
    ├── REVIEW_REPORT.md        — Reviewer's findings
    ├── FEATURE_DOC.md          — Human feature documentation
    ├── PR_MESSAGE.md           — Ready-to-paste PR message
    ├── TASKS.md                — Task tracking
    └── DECISIONS.md            — Recorded assumptions
```

---

## How to start Claude Code

### Standard (cloud Claude)

```bash
claude
```

### With local Ollama / Qwen (smaller models)

Claude Code supports local models via Ollama. Use the `--model` flag:

```bash
# With Qwen2.5-Coder 3B (faster, less context)
claude --model qwen2.5-coder:3b

# With Qwen2.5-Coder 7B (better quality, more context)
claude --model qwen2.5-coder:7b
```

Make sure Ollama is running and the model is pulled:
```bash
ollama pull qwen2.5-coder:3b
ollama pull qwen2.5-coder:7b
ollama serve
```

---

## How to make sure the Planner is active

The agents in this system use `model: inherit`, which means they inherit whatever
model you started Claude Code with.

To activate a specific agent, you can either:

1. **Tell Claude in your prompt** — include "Use the planner-led workflow" in your request
2. **Use the `/agent` command** (if your Claude Code version supports it):
   ```
   /agent planner
   ```
3. **Reference the Planner directly** — Claude Code discovers agents from `.claude/agents/*.md`
   and can activate them by name

If you are unsure, include the phrase **"Use the planner-led workflow"** at the top of every request.
This tells the system to start with the Planner agent.

---

## Running the dry-run system check

Before using the system for real work, verify it is set up correctly.

Copy and paste this prompt:

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

Expected output: a file at `.claude/work/AGENT_SYSTEM_READINESS.md` saying the system is ready.

---

## How to give a real feature request

Copy and paste this template, filling in your actual goal:

```
Use the planner-led workflow.

Goal:
Add a health check endpoint to this project.

Required flow:
1. Planner creates or updates PROJECT_BRIEF, MASTER_PLAN, IMPLEMENTATION_CONTRACT,
   TEST_CONTRACT, TASKS, and DECISIONS.
2. Planner asks blocking clarification questions if needed.
3. Planner delegates implementation to programmer.
4. Planner delegates verification to tester.
5. Tester uses backend API checks if applicable.
6. Tester uses Playwright MCP if frontend behavior is involved.
7. Planner delegates review to reviewer.
8. Planner delegates documentation and PR message to documentor.
9. Planner gives me the final summary.
```

Replace the goal with your actual task. Keep the required flow section — it activates the full pipeline.

---

## How clarification questions work

The Planner will ask clarifying questions only when genuinely blocked.
It classifies every question as:

- **BLOCKING** — asked in one batch. Answer these to proceed.
- **NON-BLOCKING** — recorded as an assumption. No answer needed.
- **NICE-TO-HAVE** — deferred. Not asked.

**How to answer:**
Reply directly in the chat. The Planner will continue after your answers.

**If you want to skip a question:**
Say "Make a reasonable assumption and continue." The Planner will record the assumption and proceed.

---

## How each agent works

### Programmer
- Reads only `.claude/work/IMPLEMENTATION_CONTRACT.md`
- Modifies only files explicitly listed in the contract
- Runs lint/typecheck when available
- Does NOT claim the feature is complete

### Tester
- Reads `.claude/work/TEST_CONTRACT.md`
- Runs unit tests, API checks, and browser checks
- Writes TEST_REPORT.md with PASS or FAIL verdict backed by real evidence

### Reviewer
- Reads the git diff and changed files
- Checks code quality, security, architecture, and risk
- Writes REVIEW_REPORT.md with PASS or FAIL verdict

### Documentor
- Reads TEST_REPORT.md and REVIEW_REPORT.md
- Writes FEATURE_DOC.md and PR_MESSAGE.md
- Never claims PASS unless the reports say PASS

---

## How the Tester does backend API checks

The Tester sends real HTTP requests using the tools available on your machine.

**On Linux/Mac:**
```bash
curl -s -w "\n[HTTP %{http_code}]" \
  -X POST http://localhost:3000/api/health \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'
```

**On Windows (PowerShell):**
```powershell
$resp = Invoke-WebRequest -Uri "http://localhost:3000/api/health" -Method GET
Write-Output "Status: $($resp.StatusCode)"
Write-Output "Body: $($resp.Content)"
```

The Tester checks:
- HTTP status codes
- Response body contents
- Validation error behavior
- Auth enforcement (401 / 403)
- Edge cases

---

## How to set up Playwright MCP for frontend testing

Playwright MCP enables the Tester to control a real browser and verify UI behavior.

### Step 1 — Install Playwright MCP in Claude Code settings

Add this to your Claude Code MCP configuration (`~/.claude/claude_desktop_config.json` or equivalent):

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

### Step 2 — Verify it is available

In Claude Code, check that Playwright MCP tools appear in your tool list.
You should see tools like `browser_navigate`, `browser_click`, `browser_screenshot`.

### Step 3 — Include browser checks in your request

When your feature has a UI, tell the Planner:

```
Tester must verify the UI using Playwright MCP.
```

The Tester will navigate to pages, interact with elements, and take screenshots as evidence.

### If Playwright MCP is not available

The Tester will:
- Note which browser checks could not run automatically
- Write step-by-step manual testing instructions in TEST_REPORT.md
- Mark those checks as MANUAL-REQUIRED (not FAIL)

---

## How to copy this system into another project

1. Copy these files and folders to the root of the new project:
   ```
   CLAUDE.md
   AGENTS.md
   HOW_TO_USE.md
   .claude/
   ```

2. Clear the work files so they are blank templates again:
   ```bash
   # Reset all work artifacts to templates (keeps the files, clears content)
   # Run this from the project root
   ```
   Or see "How to reset .claude/work/" below.

3. Update `CLAUDE.md` if the new project has different coding standards.

4. Run the dry-run check to verify the system works.

---

## How to reset .claude/work/

When starting a new feature or a new project, clear the work artifacts:

**Option A — Delete and let Planner recreate:**
```bash
# In the project root
rm .claude/work/PROJECT_BRIEF.md
rm .claude/work/MASTER_PLAN.md
rm .claude/work/IMPLEMENTATION_CONTRACT.md
rm .claude/work/TEST_CONTRACT.md
rm .claude/work/TEST_REPORT.md
rm .claude/work/REVIEW_REPORT.md
rm .claude/work/FEATURE_DOC.md
rm .claude/work/PR_MESSAGE.md
rm .claude/work/TASKS.md
rm .claude/work/DECISIONS.md
```

**Option B — Keep the templates:**
The work files in this repo are already blank templates. They are overwritten
by the Planner at the start of each workflow run.

---

## Common problems and fixes

### "Claude is just writing code without planning"

Include **"Use the planner-led workflow"** in your prompt.
The Planner agent must be explicitly activated.

### "The Planner is asking too many questions"

Reply: "Make reasonable assumptions and continue. Record your assumptions in DECISIONS.md."

### "The Tester says tests passed but didn't run them"

The Tester is instructed never to claim PASS without evidence. If it does:
- Reply: "Show me the actual command output for the test results."
- If you are using a small model (3B), the model may have limited ability to run commands. Switch to 7B.

### "Commands are failing"

The Tester reports failures to the Planner. The Planner may re-delegate to the Programmer.
If the failure is persistent, the Planner will report it to you.

### "The agent modified files it shouldn't have"

Check `.claude/work/IMPLEMENTATION_CONTRACT.md → Files allowed to modify`.
If a file was modified that is not on the list, that is a contract violation.
Report it: "The programmer modified `<file>` which is not in the allowed list. This is incorrect."

### "Playwright MCP is not working"

If browser tools do not appear, Playwright MCP is not configured.
The Tester will fall back to MANUAL-REQUIRED for browser checks.
See "How to set up Playwright MCP" above.

### "I'm using Qwen 3B and the agent seems confused"

Qwen2.5-Coder 3B has a small context window. Tips:
- Keep your requests short and specific
- Do one feature at a time (don't chain multiple features in one prompt)
- Use Qwen 7B if 3B cannot follow multi-step instructions reliably
- The work files help compensate — the agent reads contracts, not memory

---

## Best practices for smaller local models (Qwen 3B/7B)

- **One task at a time.** Do not ask for multiple features in one prompt.
- **Be explicit.** "Use the planner-led workflow" is better than "plan this."
- **Short prompts.** Long prompts consume context that the model needs for reasoning.
- **Check artifacts.** After each step, read `.claude/work/` files to verify the Planner is tracking correctly.
- **Prefer 7B over 3B** for complex features. 3B is for simple, single-endpoint changes.
- **Reset between features.** Clear `.claude/work/` files between unrelated features to avoid context confusion.

---

## Example prompts

### Dry run

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

### Feature request

```
Use the planner-led workflow.

Goal:
Add a health check endpoint to this project.

Required flow:
1. Planner creates or updates PROJECT_BRIEF, MASTER_PLAN, IMPLEMENTATION_CONTRACT,
   TEST_CONTRACT, TASKS, and DECISIONS.
2. Planner asks blocking clarification questions if needed.
3. Planner delegates implementation to programmer.
4. Planner delegates verification to tester.
5. Tester uses backend API checks if applicable.
6. Tester uses Playwright MCP if frontend behavior is involved.
7. Planner delegates review to reviewer.
8. Planner delegates documentation and PR message to documentor.
9. Planner gives me the final summary.
```

### Bug fix

```
Use the planner-led workflow.

Goal:
Fix the bug where the login endpoint returns 500 when the email field is missing.
It should return 400 with {"error": "email is required"}.

Required flow: full pipeline (plan → implement → test → review → document).
```

### Planner-only planning session

```
Use the planner-led workflow.

Goal:
Plan only. Do not implement anything yet.

I want to add pagination to the /api/posts endpoint.
Write the PROJECT_BRIEF, MASTER_PLAN, IMPLEMENTATION_CONTRACT, TEST_CONTRACT, and TASKS.
Ask me any blocking questions first.
Stop after planning — do not delegate to programmer.
```
