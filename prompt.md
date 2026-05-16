Use the planner-led workflow.

Goal:
Create a small demo web app called Task Board.

I want this to be a simple full-stack project that proves my agent system works.

The app should let a user manage simple tasks.

The user should be able to:
1. See a list of tasks.
2. Add a new task.
3. Mark a task as done.
4. Mark a done task as not done.
5. Delete a task.
6. Filter tasks by All, Active, and Done.
7. See a helpful message when there are no tasks.
8. See a validation error if they try to add an empty task.

Keep the app simple.

Use a simple frontend and backend.

Frontend:
- Use React if this project does not already have a frontend.
- Make one clean page.
- The page should have a title, input box, add button, task list, filter buttons, and simple error messages.
- The UI does not need to be fancy, but it should be clean and easy to test.

Backend:
- Create a simple API for tasks.
- Do not use a real database for this demo unless the project already has one.
- Use in-memory storage or a simple JSON file.
- Add a health check endpoint.

The backend should support:
- GET /api/health
- GET /api/tasks
- POST /api/tasks
- PATCH /api/tasks/:id
- DELETE /api/tasks/:id

Each task should have:
- id
- title
- completed
- createdAt

Validation:
- A task title is required.
- Empty task titles should return a clear error.
- Very long task titles should be rejected.
- Deleting a task that does not exist should return a clear error.
- Updating a task that does not exist should return a clear error.

Testing:
The tester must verify the backend API.
The tester should use available tools such as curl, Invoke-RestMethod, npm scripts, or project test tools.

The tester must check:
1. GET /api/health returns success.
2. GET /api/tasks returns a list.
3. POST /api/tasks creates a task.
4. POST /api/tasks with an empty title fails.
5. PATCH /api/tasks/:id can mark a task done.
6. PATCH /api/tasks/:id can mark a task not done.
7. DELETE /api/tasks/:id deletes a task.
8. Invalid task IDs return useful errors.

The tester must also verify the frontend.
If Playwright MCP is available, use it.
If Playwright MCP is not available, explain the fallback test method.

Frontend checks:
1. The page loads.
2. The user can add a task.
3. The new task appears on the page.
4. The user cannot add an empty task.
5. The user can mark a task as done.
6. The user can mark it as not done.
7. The user can filter All, Active, and Done tasks.
8. The user can delete a task.
9. The empty state message appears when there are no tasks.

Documentation:
The documentor must create:
- FEATURE_DOC.md
- PR_MESSAGE.md

The feature documentation should explain:
- What the app does.
- What changed.
- What files were added or changed.
- What API endpoints exist.
- What frontend behavior exists.
- What tests were run.
- Any known limitations.

The PR message should be ready to paste into GitHub or Azure DevOps.

Important rules:
- The planner should ask clarification questions only if they are truly blocking.
- If something is not blocking, make a reasonable assumption and record it in DECISIONS.md.
- The programmer must not start coding until the planner creates the plan and contracts.
- The programmer must follow the implementation contract.
- The tester must not claim success without evidence.
- The reviewer must inspect the final diff.
- The documentor must not claim tests passed unless the test report says they passed.
- Keep this project small enough for a local model like Qwen2.5-Coder 3B or 7B.

Required flow:
1. Planner reads the project.
2. Planner creates or updates PROJECT_BRIEF.md.
3. Planner creates MASTER_PLAN.md.
4. Planner creates IMPLEMENTATION_CONTRACT.md.
5. Planner creates TEST_CONTRACT.md.
6. Planner creates TASKS.md.
7. Planner records assumptions in DECISIONS.md.
8. Planner delegates coding to Programmer.
9. Programmer implements the app.
10. Planner delegates testing to Tester.
11. Tester writes TEST_REPORT.md.
12. Planner delegates review to Reviewer.
13. Reviewer writes REVIEW_REPORT.md.
14. Planner delegates docs to Documentor.
15. Documentor writes FEATURE_DOC.md and PR_MESSAGE.md.
16. Planner gives me the final summary.