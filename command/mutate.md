---
description: Run mutation testing and enforce minimum mutation score on the current changes.
agent: build
subtask: true
---

## Execution Steps

1. **Verify the test suite passes**
   - Ask the user for the command they use to run tests (if not obvious from project context).
   - Execute the command via bash and surface the output.
   - If tests fail, stop and report the failure; do not attempt mutation testing.

2. **Determine the mutation scope**
   - If the user supplied explicit paths/modules, use them.
   - Otherwise:
     1. Run `git diff --staged --name-only` (bash). If non-empty, focus on staged files.
     2. If nothing is staged, run `git diff main...HEAD --name-only` (bash) and use those files.
   - If no files are found, report “Nothing to mutate” and exit.

3. **Launch the mutation-testing agent**
   - Invoke `Task(subagent_type="mutation-testing-agent", prompt="Run mutation testing for the selected scope. Report overall and per-module mutation scores, list surviving mutants with file:line references, and suggest tests to kill them. Enforce ≥80% overall mutation score.")`.

4. **Present the results**
   - Display the overall score, per-module breakdown, surviving mutants, and recommended follow-up tests returned by the mutation-testing agent.

5. **Enforce the gate**
   - If the overall score is below 80%, mark the gate as failed and highlight the mutants that must be addressed before proceeding to `/pr:create`.
   - If the score meets or exceeds 80%, confirm the gate succeeded.

## Failure Handling

- If the mutation-testing agent reports missing tooling, surface the message and call for the dependency-management agent if needed.
- If no applicable files were detected, exit with “Nothing to mutate.”
