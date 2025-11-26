---
description: Address review feedback by updating code and replying to specific comments.
agent: build
subtask: true
---

> Treat this file as the authoritative runbook for `/pr:respond`. Execute every step below, iterating through each unresolved comment without skipping.

## Execution Steps


1. **Resolve the comment context**
   - Detect the hosting provider by running `git remote get-url origin` (bash).
   - If a comment ID or URL argument was provided, use it. Otherwise:
     1. Run `git rev-parse --abbrev-ref HEAD` (bash) to identify the branch.
     2. Run `gh pr status --json headRefName,number,url` (bash) or the GitLab equivalent to locate the open PR/MR.
     3. Fetch all unresolved review threads:
        - GitHub: `gh api repos/:owner/:repo/pulls/<number>/comments?per_page=100` (bash).
        - GitLab: `glab api projects/:id/merge_requests/:iid/discussions` (bash).
     4. Work through every unresolved thread in a deterministic order (e.g., oldest first); do not wait for additional user input.
   - Abort with guidance if no PR/MR exists for the branch.

2. **Ensure the branch is ready**
   - Run `git status -sb` (bash). If there are staged or unstaged changes, stop and instruct the user to finish the "normal" workflow (`/commit` + `/push`) before addressing review feedback.

3. **Iterate over each unresolved comment**
   - For every comment gathered in Step 1 (oldest first):
     1. Retrieve the comment details (`gh api repos/:owner/:repo/pulls/comments/<comment-id>` or GitLab equivalent) and restate the ask.
     2. Decide whether the feedback requires code changes, documentation updates, or only a clarification. If you disagree, craft a respectful reply anyway.
     3. When changes are required:
        - Implement them using the standard editing workflow.
        - After each cohesive change, run the appropriate tests/linters (document the exact commands and ensure they pass). Follow the repository's Red/Green workflow as applicable.
        - Create a dedicated commit for that change via `/commit`, using a why-focused message that references the feedback context.
        - `/push` immediately so the PR stays up to date.
     4. Post a reply that references the commit hash (or explains why no change was needed). Never ignore a comment.

4. **Post the reply**
   - GitHub: `gh api repos/:owner/:repo/pulls/comments/<comment-id>/replies -f body='<reply>'` (bash).
   - GitLab: `glab api projects/:id/merge_requests/:iid/discussions/<comment-id>/notes -f body='<reply>'` (bash).

5. **Summarize and follow up**
   - After all comments are addressed, provide a recap of the commits pushed and the tests run. Remind the user if any threads still need manual resolution toggles or additional reviewer confirmation.

## Failure Handling

- If the API call fails (stale comment, missing permissions), surface the error and provide guidance (e.g., re-run `/push`, authenticate via `gh auth login`).
- If no unresolved threads are found, report that the review is already fully addressed.
