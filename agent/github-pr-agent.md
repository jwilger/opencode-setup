---
name: github-pr-agent (deprecated)
description: Legacy prompt retained for historical context. GitHub PR operations now run via slash commands executed by the build agent.
model: openai/gpt-5-codex
mode: subagent
tools:
  write: false
  edit: false
  bash: false
---

# Deprecated: GitHub PR Agent

This prompt is intentionally retired. Use the slash commands instead:

- `/pr:create` – opens PRs/MRs and enforces quality gates.
- `/pr:review` – checks out and reviews incoming PRs/MRs.
- `/pr:respond` – replies to review feedback after updates are pushed.

Do not launch this agent. Retained only so older conversations referencing it resolve gracefully.
