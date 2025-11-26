---
description: Create a GitHub or GitLab pull/merge request with required quality gates.
agent: build
---

> Treat this file as the authoritative runbook for `/pr:create`. Execute each step in sequence; do not treat the checklist as optional context.

## Execution Steps


1. **Confirm a clean working tree**
   - Run `git status -sb` (bash) and show the output.
   - If the tree is dirty, stop and instruct the user to commit or stash before retrying.

2. **Identify the current branch**
   - Run `git rev-parse --abbrev-ref HEAD` (bash) and capture the branch name for later commands.

3. **Verify the branch is pushed**
   - Run `git rev-parse --abbrev-ref --symbolic-full-name @{u}` (bash).
   - If no upstream exists or the branch is behind, prompt the user to run `/push` (or offer to run it) before proceeding.

4. **Run quality gates**
   - Launch `Task(subagent_type="cognitive-complexity-agent", prompt="Analyze the staged and unstaged diff before PR creation. Enforce ≥70% overall TRACE and ≥50% per dimension. Provide remediation guidance for any failures.")`.
   - Launch `Task(subagent_type="mutation-testing-agent", prompt="Run mutation testing on the current changes. Enforce ≥80% overall score and list surviving mutants with remediation suggestions.")`.
   - Abort if either gate fails and summarize the remediation items to the user.

5. **Prepare PR metadata automatically**
   - If the user supplies a title or body when invoking `/pr:create`, treat it as a **hint or constraint**, not the final text.
     - When the user says "use this exact title/body", respect it verbatim.
     - Otherwise, refine or rewrite it to be concise, why-focused, and consistent with the actual diff.
   - In all cases, derive the final PR title from the *entire* change set (diff against the base branch), not just the latest edits. Keep it short, imperative, and why-focused.
   - Write 1–2 summary bullets that capture the overall motivation for the whole PR. If multiple features land together, mention each at a high level.
   - Only include a "## Testing" section when non-obvious manual verification is required; omit it if automation already covers the change.
   - Skip reviewers/draft flags unless the user explicitly requests them.

6. **Detect the hosting provider**
   - Run `git remote get-url origin` (bash).
   - If the URL contains `github`, plan to use `gh`; otherwise use `glab`.

7. **Create the PR/MR**
   - Construct the body using this template (omit the Testing section entirely if it is not needed):
     ```
     ## Summary
     - <why bullet>
     
     ## Testing
     - <command> (result)
     ```
   - Execute the appropriate CLI command (bash):
     - GitHub: `gh pr create --title <title> --body <body> [--reviewer ...] [--draft]`.
     - GitLab: `glab mr create --title <title> --description <body> [--reviewer ...] [--draft]`.
   - Surface stdout/stderr so the user sees the outcome.

8. **Report results**
   - Echo the created PR/MR URL, reviewers, and draft status back to the user.

## Failure Handling

- If CLI authentication fails, stop and instruct the user to run `gh auth login` or `glab auth login`.
- If a PR already exists for the branch, offer to open it (`gh pr view --web` / `glab mr view`) instead of creating a duplicate.
- Never bypass TRACE or mutation-testing gates.
