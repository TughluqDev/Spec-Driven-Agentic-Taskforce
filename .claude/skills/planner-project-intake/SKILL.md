---
name: planner-project-intake
description: >
  Teaches the Planner how to inspect a new or existing project and write a
  complete PROJECT_BRIEF.md. Covers what to read, what to extract, and how
  to represent the tech stack, entry points, conventions, and unknowns.
---

# Skill: planner-project-intake

## Purpose

Build accurate understanding of the project before planning anything.
Write this understanding into `.claude/work/PROJECT_BRIEF.md` so every agent
has a shared, reliable reference.

## When to Use

Use at the start of every workflow, before writing the master plan.
Update when the project structure changes significantly.

## Input

- User's request
- Project root directory listing
- Project config files (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, etc.)
- Entry point files
- Existing README

## Output

`.claude/work/PROJECT_BRIEF.md`

---

## Process

### Step 1 — Read the directory

```bash
ls -la
find . -maxdepth 2 -not -path '*/.git/*' -not -path '*/node_modules/*'
```

List files and folders. Understand the top-level structure.

### Step 2 — Read project config

Read whichever applies:
- `package.json` — name, scripts, dependencies
- `pyproject.toml` / `setup.py` — project name, dependencies, scripts
- `go.mod` — module name, go version
- `Cargo.toml` — crate name, dependencies
- `.env.example` or `env.example` — environment variables
- `docker-compose.yml` — services and ports
- `Makefile` — common tasks

### Step 3 — Read the entry point

Find and read the main entry point:
- `src/index.ts`, `src/main.ts`, `src/app.ts`
- `main.go`, `cmd/main.go`
- `app.py`, `main.py`
- `src/main.rs`

Read 2–3 key files to understand conventions (naming, imports, patterns).

### Step 4 — Identify the tech stack

From what you read, identify:
- Frontend framework (React, Vue, Svelte, none)
- Backend framework (Express, FastAPI, Gin, Axum, none)
- Database (Postgres, SQLite, MongoDB, none)
- Auth method (JWT, sessions, OAuth, none)
- Test framework (Jest, Vitest, pytest, go test, etc.)
- Build tools (Vite, Webpack, esbuild, etc.)
- Package manager (npm, pnpm, yarn, pip, cargo, etc.)

### Step 5 — Extract build and run commands

From `package.json` scripts or Makefile:
- How to install dependencies
- How to start the dev server
- How to run tests
- How to run lint
- How to build for production

### Step 6 — Write PROJECT_BRIEF.md

---

## Output Format: PROJECT_BRIEF.md

```markdown
# Project Brief

## Project name
<name from config or directory>

## Purpose
<what this project does, in 1-2 sentences>

## Tech stack

### Frontend
<framework, version, key libraries — or "None">

### Backend
<framework, language, version — or "None">

### Database
<type and name — or "None">

### Auth
<method — or "None">

### Testing tools
<test frameworks and tools>

### Build / dev tools
<bundler, compiler, etc.>

## Key commands

| Task | Command |
|------|---------|
| Install | <command> |
| Dev server | <command> |
| Build | <command> |
| Test | <command> |
| Lint | <command> |
| Typecheck | <command> |

## Important folders

| Folder | Purpose |
|--------|---------|
| <path> | <what it contains> |

## Important files

| File | Purpose |
|------|---------|
| <path> | <what it does> |

## Current assumptions
- <assumption 1>
- <assumption 2>

## Unknowns
- <unknown 1>
- <unknown 2>
```

---

## Quality Checklist

- [ ] Tech stack is identified from actual files, not guessed
- [ ] All key commands are verified to exist in project config
- [ ] At least 3 important files are listed
- [ ] Assumptions are clearly separated from facts
- [ ] Unknowns are listed honestly

## Safety Rules

- Do not invent commands. Only list commands that exist in the project.
- Do not modify any source files during intake.
- Do not assume a framework is present without evidence (an import or config file).
