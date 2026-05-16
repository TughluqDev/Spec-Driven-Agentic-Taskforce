---
name: tester-playwright-ui-check
description: >
  Teaches the Tester how to use Playwright MCP for frontend browser testing.
  Covers navigation, interaction, assertion patterns, screenshot evidence, and
  what to do when Playwright MCP is not available.
---

# Skill: tester-playwright-ui-check

## Purpose

Verify that UI behavior is correct by controlling a real browser.
This is the only way to reliably test forms, buttons, navigation, error states,
and visual rendering. Static analysis cannot replace it.

## When to Use

When the test contract includes frontend or browser checks.
When the feature adds or changes UI behavior.

## Input

- `.claude/work/TEST_CONTRACT.md` (browser check scenarios)
- The running dev server URL (usually `http://localhost:3000` or similar)

## Output

- Browser check results recorded in `TEST_REPORT.md`
- Screenshots as evidence (if supported)

---

## Checking Playwright MCP Availability

Before starting browser checks, verify Playwright MCP is available.
If you see Playwright MCP tools in your available tools list, it is available.

If not available:
1. Document in TEST_REPORT.md: "Browser checks: MANUAL-REQUIRED — Playwright MCP not available"
2. Write out exactly what a manual tester should do for each scenario
3. Continue with all other non-browser checks

---

## Using Playwright MCP

### Start the browser

Use the Playwright MCP `browser_navigate` tool to open a URL:
```
browser_navigate: { url: "http://localhost:3000/page" }
```

### Take a screenshot to confirm the page loaded

```
browser_screenshot: {}
```

Always take a screenshot before and after key interactions. This is your evidence.

### Interact with elements

Click a button:
```
browser_click: { selector: "button[type=submit]", description: "Submit button" }
```

Type into a field:
```
browser_type: { selector: "input[name=email]", text: "test@example.com" }
```

### Assert state

Check for visible text:
```
browser_snapshot: {}
```

Read the accessibility tree or screenshot to confirm expected text/elements are present.

---

## Standard Test Scenarios

### Form submission — happy path
1. Navigate to the page
2. Screenshot (before state)
3. Fill in valid inputs
4. Click submit
5. Screenshot (after state)
6. Confirm success message or redirect

### Form submission — validation error
1. Navigate to the page
2. Leave required fields empty
3. Click submit
4. Screenshot
5. Confirm error message is visible

### Navigation
1. Navigate to start page
2. Click a link or nav item
3. Screenshot
4. Confirm URL changed and correct page is shown

### Loading states
1. Navigate to page
2. Screenshot immediately (before data loads)
3. Wait for load (screenshot after)
4. Confirm loading indicator was shown and is now gone

### Empty states
1. Navigate to a list page with no items
2. Screenshot
3. Confirm empty state message is shown

### Error states
1. Trigger an error condition (e.g., load a page for a non-existent resource)
2. Screenshot
3. Confirm appropriate error message is shown

---

## Recording Results

In TEST_REPORT.md, for each browser scenario:

```markdown
### Browser check: <scenario name>
**URL:** http://localhost:3000/page
**Actions:**
1. Navigated to /page
2. Typed "test@example.com" into email field
3. Clicked submit

**Expected:** Success message "Account created"
**Actual:** Success message "Account created" was visible
**Screenshot:** [taken after submit]
**Result:** PASS
```

---

## Quality Checklist

- [ ] Every browser scenario from the test contract has an entry
- [ ] Screenshots were taken at key moments
- [ ] Error states and empty states were checked (not just happy path)
- [ ] Results describe actual observations, not expected behavior
- [ ] MANUAL-REQUIRED sections include exact steps for a human tester

## Safety Rules

- Always use the dev/test server — never run browser checks against production.
- If the server is not running, start it first. Note the startup command in the report.
- Never click destructive actions (delete all, drop database) during browser testing.