---
description: Address review feedback by updating code and replying to specific comments.
agent: build
subtask: true
---

## Execution Steps

1. **Resolve the comment context**
   - Detect the hosting provider by running `git remote get-url origin` (bash).
   - If a comment ID or URL argument was provided, use it. Otherwise:
     1. Run `git rev-parse --abbrev-ref HEAD` (bash) to identify the branch.
     2. Run `gh pr status --json currentBranch` (bash) or the GitLab equivalent to locate the open PR/MR.
     3. Fetch unresolved review threads:
        - GitHub: `gh api repos/:owner/:repo/pulls/<number>/comments?per_page=100` (bash).
        - GitLab: `glab api projects/:id/merge_requests/:iid/discussions` (bash).
     4. Present unresolved threads to the user and ask which one to address; wait for their choice.
   - Abort with guidance if no PR/MR exists for the branch.

2. **Ensure the branch is ready**
   - Run `git status -sb` (bash). If there are staged or unstaged changes, instruct the user to `/commit` and `/push` before replying.

3. **Retrieve the specific comment**
   - GitHub: `gh api repos/:owner/:repo/pulls/comments/<comment-id>` (bash).
   - GitLab: `glab api projects/:id/merge_requests/:iid/discussions/<comment-id>` (bash).
   - Display the context (file, line, original message) to confirm the target.

4. **Collect the reply body**
   - If no response text was supplied, prompt the user for the message they want to post. Encourage including what changed and why it resolves the feedback.

5. **Post the reply**
   - GitHub: `gh api repos/:owner/:repo/pulls/comments/<comment-id>/replies -f body='<reply>'` (bash).
   - GitLab: `glab api projects/:id/merge_requests/:iid/discussions/<comment-id>/notes -f body='<reply>'` (bash).

6. **Summarize and follow up**
   - Confirm the reply was posted and remind the user to mark the thread resolved or re-request review if appropriate (`gh pr ready` / `glab mr ready`).

## Failure Handling

- If the API call fails (stale comment, missing permissions), surface the error and provide guidance (e.g., re-run `/push`, authenticate via `gh auth login`).
- If no unresolved threads are found, report that the review is already fully addressed.
