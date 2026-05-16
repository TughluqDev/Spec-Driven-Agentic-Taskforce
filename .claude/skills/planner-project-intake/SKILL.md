---
name: planner-project-intake
description: >
  Teaches the Planner how to inspect a new or existing project and write a
  complete PROJECT_BRIEF.md. Covers what to read, what to extract, and how
  to represent the tech stack, entry points, conventions, and unknowns.
---

# Skill: planner-project-intake

## Purpose

Build accurate, evidence-based understanding of the project before planning anything.
Every claim in PROJECT_BRIEF.md must be sourced from actual files — never assumed.

## When to Use

At the start of every workflow, before writing the master plan.
Update whenever project structure or dependencies change significantly.

---

## Process

### Step 1 — Directory survey

```bash
# List top-level files and folders
ls -la

# Survey up to 3 levels (skip build artifacts and dependencies)
find . -maxdepth 3 \
  -not -path '*/.git/*' \
  -not -path '*/node_modules/*' \
  -not -path '*/__pycache__/*' \
  -not -path '*/target/*' \
  -not -path '*/.next/*' \
  -not -path '*/dist/*' \
  -not -path '*/build/*'
```

**Windows PowerShell:**
```powershell
Get-ChildItem -Recurse -Depth 3 | 
  Where-Object { $_.FullName -notmatch 'node_modules|\.git|__pycache__|target|\.next|dist|build' } |
  Select-Object FullName
```

Understand the top-level structure before reading any files.

### Step 2 — Read project config

Read whichever apply (in priority order):

| Config file | What to extract |
|-------------|----------------|
| `package.json` | name, scripts, dependencies, devDependencies |
| `package-lock.json` / `pnpm-lock.yaml` | confirm exact package manager |
| `pyproject.toml` / `setup.py` / `setup.cfg` | project name, dependencies, entry points |
| `go.mod` | module name, Go version, dependencies |
| `Cargo.toml` | crate name, edition, dependencies |
| `Makefile` | available make targets |
| `docker-compose.yml` | services, ports, volumes |
| `.env.example` / `env.example` | required environment variables |
| `tsconfig.json` | TypeScript compiler options, paths |
| `.eslintrc.*` | lint rules in effect |
| `vitest.config.*` / `jest.config.*` | test configuration |

### Step 3 — Read entry points

Find and read the main entry point(s):

**JavaScript / TypeScript:**
- `src/index.ts`, `src/main.ts`, `src/app.ts`, `src/server.ts`
- `app.js`, `server.js`, `index.js`

**Python:**
- `main.py`, `app.py`, `src/main.py`
- `wsgi.py`, `asgi.py`

**Go:**
- `main.go`, `cmd/main.go`, `cmd/<name>/main.go`

**Rust:**
- `src/main.rs`, `src/lib.rs`

### Step 4 — Read 3 representative source files

To understand conventions, read 3 files from the core of the codebase:
- One route/handler file
- One utility or helper file
- One data model or schema file

Extract:
- Naming conventions (camelCase / snake_case / PascalCase)
- Import organization (stdlib first? grouped?)
- Error handling pattern (throw / return error tuple / callback)
- Comment style (or absence thereof)
- Test colocation vs. separate test directory

### Step 5 — Identify the tech stack

From actual file evidence, identify:

| Category | What to look for |
|----------|-----------------|
| Frontend framework | React, Vue, Svelte, Angular — imports in source files |
| Backend framework | Express, Fastify, FastAPI, Django, Gin, Axum — imports + config |
| Database | pg, prisma, typeorm, sqlalchemy, gorm — deps + connection files |
| Auth method | passport, jwt, sessions, oauth — deps + middleware files |
| Test framework | jest, vitest, pytest, go test — config files + test file imports |
| Build tools | vite, webpack, esbuild, tsc, rollup — config files |
| Package manager | presence of `package-lock.json` (npm), `pnpm-lock.yaml` (pnpm), `yarn.lock` (yarn) |
| CSS / styling | tailwind, styled-components, CSS modules — config or imports |

Only list a technology if you have **file evidence** for it. Never guess.

### Step 6 — Extract all runnable commands

From `package.json` scripts, `Makefile` targets, or README:

- Install dependencies
- Start dev server
- Run tests
- Run lint
- Run typecheck
- Build for production
- Run migrations (if applicable)

Only list commands that actually exist. Verify they exist in the config file.

### Step 7 — Write PROJECT_BRIEF.md

---

## Output Format: PROJECT_BRIEF.md

```markdown
# Project Brief

## Project name
<name from config file — not invented>

## Purpose
<1-2 sentences: what this project does. Source: README or entry point code.>

## Tech stack

### Frontend
<framework + version + key UI libraries — or "None">

### Backend
<framework + language + version — or "None">

### Database
<type, ORM if any — or "None">

### Auth
<method (JWT / sessions / OAuth / API key) — or "None">

### Testing
<test framework(s), browser testing tools>

### Build / dev tools
<bundler, compiler, package manager>

## Key commands

| Task | Command |
|------|---------|
| Install | <exact command> |
| Dev server | <exact command> |
| Build | <exact command> |
| Test | <exact command> |
| Lint | <exact command> |
| Typecheck | <exact command or "N/A"> |
| Migrations | <exact command or "N/A"> |

## Important folders

| Folder | Purpose |
|--------|---------|
| <path> | <what it contains> |

## Important files

| File | Purpose |
|------|---------|
| <path> | <what it does> |

## Environment variables
<List variables from .env.example — or "None found / check with team">

## Current assumptions
- <assumption 1 — clearly labelled as assumption>

## Unknowns
- <question 1 — things that need clarification>
```

---

## Quality Checklist

- [ ] Tech stack is identified from actual file evidence, not guessed
- [ ] All key commands verified to exist in package.json, Makefile, or README
- [ ] At least 5 important files or folders are listed
- [ ] Assumptions are clearly separated from facts
- [ ] Unknowns are listed honestly for the clarifying questions step

## Safety Rules

- Do not invent commands that don't exist in the project config.
- Do not modify any source files during intake — read only.
- Do not assume a framework without file evidence (config file or import).
- Do not list environment variables that are not in `.env.example` or documentation.
