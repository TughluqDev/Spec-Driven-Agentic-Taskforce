---
name: reviewer-risk-analysis
description: >
  Teaches the Reviewer how to identify and assess risks: architecture, data,
  deployment, performance, and user-impact risks. Each risk gets a severity
  and a mitigation recommendation.
---

# Skill: reviewer-risk-analysis

## Purpose

Identify risks that could cause problems after deployment. Code that passes
all tests can still have architectural, performance, or deployment risks that
surface in production.

## When to Use

As part of the review process, after code review findings are documented.

## Input

- Changed files
- `.claude/work/IMPLEMENTATION_CONTRACT.md`
- `.claude/work/MASTER_PLAN.md`
- `.claude/work/TEST_REPORT.md`

## Output

Risk findings in `.claude/work/REVIEW_REPORT.md`

---

## Risk Categories

### 1. Regression Risk

Could this change break existing behavior?

Ask:
- Does this change a function or module that is used in multiple places?
- Does this change a database schema or query that affects existing data?
- Does this change the behavior of a shared middleware or utility?
- Were regression tests run and did they pass?

Severity: HIGH if shared utilities were changed and regression tests were not run.

### 2. Data Risk

Could this change corrupt, delete, or transform existing data incorrectly?

Ask:
- Does this run any database migrations?
- Does this change how data is written or read?
- Is the migration reversible?
- What happens to existing records if the schema changes?

Severity: HIGH for any irreversible data operation.

### 3. Performance Risk

Could this degrade performance under load?

Look for:
- N+1 queries (a query inside a loop that iterates over query results)
- Missing database indexes on newly-queried columns
- Synchronous I/O in an async context
- Unbounded loops over user-controlled input sizes
- Large payloads loaded entirely into memory

Severity: MEDIUM by default; HIGH if the endpoint is high-traffic.

### 4. Deployment Risk

Does this require manual steps to deploy?

Ask:
- Does this require a new environment variable?
- Does this require a database migration to run first?
- Does this require a service restart?
- Does this require a feature flag to be enabled?
- Does this require infrastructure changes?

Severity: HIGH if deployment steps are required and not documented.

### 5. Rollback Risk

Can this be safely rolled back if it causes issues?

Ask:
- If we revert this code, will the app work correctly with existing data?
- If a migration ran, is it reversible?
- Are there any forward-only changes?

Severity: HIGH if the change cannot be safely rolled back.

### 6. User Impact Risk

Could this change degrade the user experience?

Ask:
- Could this cause errors for existing users with existing data?
- Could this break existing integrations or API clients?
- Does this change any public API contracts (field names, types, error formats)?

Severity: HIGH for any breaking API change.

---

## Output Format in REVIEW_REPORT.md

```markdown
## Risk Analysis

### Regression risk: LOW / MEDIUM / HIGH
<description and evidence>

### Data risk: LOW / MEDIUM / HIGH
<description and mitigation>

### Performance risk: LOW / MEDIUM / HIGH
<description, specific location in code, mitigation>

### Deployment risk: LOW / MEDIUM / HIGH
<required steps, or "None">

### Rollback risk: LOW / MEDIUM / HIGH
<description, or "Safe to revert">

### User impact risk: LOW / MEDIUM / HIGH
<description, or "No user-facing changes">
```

---

## Quality Checklist

- [ ] All 6 risk categories are assessed
- [ ] Each risk has a severity (LOW / MEDIUM / HIGH)
- [ ] HIGH risks have a specific mitigation or recommendation
- [ ] Deployment steps are explicitly listed if required
- [ ] Rollback safety is explicitly assessed

## Safety Rules

- Do not downgrade a risk to LOW just to make the review easier.
- HIGH risk does not block deployment — it requires a plan.
- Document risks even if they are acceptable.