---
description: Push the current branch to its remote with safety checks.
agent: build
---

> Treat this file as the authoritative runbook for `/push`. Execute all checks sequentially and stop if any prerequisite fails.

## Execution Steps


1. **Resolve remote and branch**
   - Run `git rev-parse --abbrev-ref HEAD` (bash) to identify the current branch.
   - Apply user-supplied arguments in this order:
     - If the user provided a branch argument, use it instead of the current branch.
     - If the user provided a remote argument, use it; otherwise default to the remote tracked by the branch.
   - Run `git rev-parse --abbrev-ref --symbolic-full-name @{u}` (bash).
     - If an upstream exists, extract remote/branch from the output and keep them unless the user overrode either value.
     - If no upstream exists, default the remote to `origin` when none was supplied and plan to add `--set-upstream`.
   - If the remote is still ambiguous (e.g., multiple remotes in the repo), ask the user to choose and wait for their response.
   - Run `git remote get-url <remote>` (bash) to confirm the remote exists.

2. **Ensure the working tree is clean**
   - Run `git status -sb` (bash) and show the output.
   - If staged or unstaged changes exist, stop with a clear message instructing the user to commit or stash before pushing.

3. **Perform the push**
   - If the branch has no upstream, run `git push --set-upstream <remote> <branch>` (bash).
   - Otherwise run `git push <remote> <branch>` (bash).
   - Surface stdout/stderr so rejection or authentication failures are visible.

4. **Confirm the result**
   - Run `git log -1 --oneline` (bash) and present the latest commit so the user sees what was pushed.
   - Report success including remote, branch, and new commit SHA.

## Failure Handling

- On non fast-forward or other push rejections, surface the git error and advise the user to pull/rebase before retrying.
- On authentication failure, stop immediately and show the error; do not retry automatically.
- If no remote can be determined after prompting, stop and let the user resolve the ambiguity.
