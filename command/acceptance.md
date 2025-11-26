---
description: Run acceptance and integration validation checks and summarize gaps
agent: build
---

> Treat this file as the authoritative runbook for `/acceptance`. Execute each step below exactly as written; reference notes may appear before the numbered steps but do not skip the checklist.

## Execution Steps

1. **Confirm a clean working tree**

   - Run `git status -sb` (bash) and stop if the tree is dirty; ask the user to commit or stash before retrying.

2. **Load acceptance and integration instructions**
   - Read `~/.config/opencode/instructions/INTEGRATION_VALIDATION.md` and any project-level acceptance rules if present.

3. **Run end-to-end and integration tests**
   - Execute the appropriate test commands for the current project (bash), preferring real integrations over mocks.
   - Surface stdout/stderr so the user can see exactly what failed or passed.

4. **Summarize results and gaps**
   - Provide a concise summary of which acceptance criteria are covered, which failed, and any notable gaps in test coverage.
   - Propose next steps, but do not auto-fix code or configuration.

5. **Reconfirm a clean working tree**
   - Run `git status -sb` (bash) again and report the final state, calling out any remaining staged or unstaged changes.
