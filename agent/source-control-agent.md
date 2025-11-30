---
name: source-control-agent (deprecated)
description: Legacy prompt replaced by slash commands for git/gh/glab workflows.
model: openai/gpt-5.1-codex
mode: subagent
tools:
  write: false
  edit: false
  bash: false
---

# Deprecated: Source Control Agent

All git and PR operations now run through the dedicated slash commands executed by the build or sdlc primary agents:

- `/commit`
- `/push`
- `/pr:create`
- `/pr:review`
- `/pr:respond`

This file remains only for backwards compatibility with historical conversations. Do not launch this agent.
