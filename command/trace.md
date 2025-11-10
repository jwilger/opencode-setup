---
description: Run TRACE cognitive complexity analysis and enforce thresholds on the current changes.
agent: build
subtask: true
---

## Execution Steps

1. **Determine the analysis scope**
   - If the user supplied file/path arguments, use them.
   - Otherwise:
     1. Run `git diff --staged --name-only` (bash). If the result is non-empty, analyze the staged diff.
     2. If nothing is staged, run `git diff main...HEAD --name-only` (bash) (replace `main` with the project’s default branch if different) and analyze those files.
   - If no files are detected, report “Nothing to analyze” and exit.

2. **Launch the TRACE agent**
   - Call `Task(subagent_type="cognitive-complexity-agent", prompt="Analyze the selected diff for TRACE cognitive complexity. Enforce ≥70% overall and ≥50% for each dimension (Type-first, Readability, Atomic scope, Cognitive budget, Essential only). Provide per-file scores, aggregate scores, and prioritized remediation guidance.")`.

3. **Present the results**
   - Output the aggregate score, per-dimension breakdown, and per-file highlights returned by the TRACE agent.
   - If the agent flags violations, surface the remediation checklist verbatim.

4. **Enforce the gate**
   - If overall or per-dimension scores fall below the thresholds, mark the gate as failed and instruct the user to address the suggested refactors before proceeding to `/pr:create`.
   - If all thresholds pass, confirm the gate succeeded.

## Failure Handling

- If the TRACE agent cannot determine the default branch, prompt the user to supply it and rerun the command with that context.
- If the diff tools report no changes, exit with a note that there is nothing to analyze.
