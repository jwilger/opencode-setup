---
description: Stage-aware git commit helper that enforces "why"-first messages and pre-commit hygiene.
agent: build
---

## Execution Steps

1. **Resolve the commit message**
   - If the user passed an argument to `/commit`, use it verbatim.
   - Otherwise:
     1. Run `git status -sb` (bash) and show the output.
     2. Run `git diff --staged --stat` (bash) and show the summary.
     3. Draft a why-focused message from the diff context. If the motivation is still unclear, ask the user directly in the main conversation and wait for their answer before continuing.

2. **Handle unstaged/untracked changes before committing**
   - Run `git status -sb` (bash) and display the result.
   - If there are unstaged or untracked changes, ask the user how to proceed and wait for their choice:
     - `stage-all`: Run `git add -A` (bash).
     - `stage-tracked`: Run `git add -u` (bash) to stage only modified/deleted tracked files.
     - `stage-select <paths...>`: Stage only the provided paths with `git add <paths...>` (bash). If no paths were provided yet, print the changed paths and ask for a space-separated list.
     - `proceed-staged`: Continue using only what is currently staged (may be empty).
     - `stash-and-proceed [message]`: Run `git stash push -u -m "<message-or-'commit-helper'>"` (bash) to stash all unstaged/untracked changes and proceed with only staged files.
     - `cancel`: Abort without committing.
   - After performing the selected action (unless `cancel`), run `git status -sb` (bash) again to show the new state.
   - If nothing is staged after the selected action and the user did not choose `proceed-staged`, ask once more whether to `stage-all`, `stage-tracked`, `stage-select`, `proceed-staged`, or `cancel`. If `proceed-staged` is chosen with an empty stage, stop with a clear message that there is nothing to commit.

3. **Present the staged diff for confirmation**
   - Run `git diff --staged` (bash) and show the diff so the user can confirm the staged hunks.
   - If the user indicates the diff is incorrect, abort and let them restage.

4. **Create the commit**
   - Execute `git commit -m "<resolved message>"` (bash).
   - Surface stdout/stderr so hook output is visible.

5. **Handle pre-commit hook modifications**
   - If the commit fails because hooks modified files:
     1. Run `git status --porcelain` (bash) and display the result.
     2. Stage hook-generated changes by running `git add <file>` for each modified/created path.
     3. Re-run `git commit -m "<same message>"` once.
     4. If the second attempt fails, stop and show the error so the user can intervene manually.

6. **Confirm the working tree is clean**
   - Run `git status -sb` (bash) and show the final state. Call out any remaining staged or unstaged files.

7. **Report the result**
   - Run `git log -1 --oneline` (bash) and present the new commit SHA/message.
   - Confirm completion back to the user.

## Failure Handling

- If `git commit` fails for reasons other than hooks (merge conflicts, verification errors, etc.), stop immediately, surface the error output, and include `git status -sb` to help the user resolve the issue.
- Never create empty commits. If hooks remove every staged change, report success without attempting another commit.
- Do not amend someone else’s commit unless the user explicitly instructs you to do so.

## Notes

- Commit messages must describe the **why** behind the change; reuse user-supplied text only if it already captures the motivation.
- Mention rollout or migration considerations in the body if the commit introduces new configuration or flags.
