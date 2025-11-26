---
description: Retrieve a PR/MR, inspect it locally, and submit a review comment or approval.
agent: build
subtask: true
---

> Treat this file as the authoritative runbook for `/pr:review`. Execute each step in order and obtain any missing information before moving on.

## Execution Steps


1. **Resolve the PR/MR identifier and desired action**
   - Ask the user for the review action (`approve`, `request-changes`, or `comment`) if it was not provided.
   - Detect the hosting platform by running `git remote get-url origin` (bash).
   - If an ID or URL argument was supplied, use it. Otherwise:
     1. Run `git rev-parse --abbrev-ref HEAD` (bash) to determine the current branch.
     2. Run `gh pr status` (bash) or `glab mr status` (bash) depending on the host.
     3. Present any candidates and ask the user to choose one; wait for their selection.

2. **Fetch the latest changes**
   - Run `git fetch origin` (bash).

3. **Check out the PR branch**
   - Execute `gh pr checkout <id>` or `glab mr checkout <id>` (bash) using the resolved identifier.
   - Run `git status -sb` (bash) and show the output so the user can confirm the checkout.

4. **Run requested validation**
   - Ask the user whether to run tests, linters, or builds.
   - Execute each requested command via bash and surface the output.

5. **Collect review body text**
   - If the action is `request-changes` or `comment`, prompt for the feedback body (markdown encouraged).
   - For approvals without text, confirm whether the user wants to include a short acknowledgement.

6. **Submit the review**
   - GitHub: run `gh pr review <id> --<action> --body <body>` (bash).
   - GitLab: run `glab mr note <id> <body>` (bash) and, for approvals, follow with `glab mr approve <id>` (bash).

7. **Summarize the result**
   - Confirm the submitted action to the user and remind them to return to their working branch (`git switch -`).

## Failure Handling

- If checkout fails because of uncommitted changes, stop and show the error; the user must stash or commit before retrying.
- If CLI authentication is missing, surface the failure and direct the user to `gh auth login` or `glab auth login`.
