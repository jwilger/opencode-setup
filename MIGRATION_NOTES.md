# Marvin → OpenCode Migration Notes

## File Drop Targets
Copy the contents of this folder into `~/.config/opencode` on your workstation. The resulting structure should look like:

```
~/.config/opencode/
  AGENTS.md
  opencode.jsonc
  prompts/
    marvin-system.md
  instructions/
    *.md
  command/
    analyze.md
    model.md
    architect.md
    plan.md
    tdd.md
```

Run `/init` inside OpenCode after copying so it registers the new instructions and agents.

## MCP Servers
The generated `opencode.jsonc` enables the same memento and time MCP servers that we previously used in Claude Code. You still need to ensure `npx`, `uvx`, and `mcp-server-time` are installed and reachable on PATH.

```
"mcp": {
  "memento": {
    "command": "npx",
    "args": ["-y", "@gannonh/memento-mcp"]
  },
  "time": {
    "command": "uvx",
    "args": ["mcp-server-time"]
  }
}
```

## Agents & Modes
- `build`: Default Marvin persona with full tool access, referencing `prompts/marvin-system.md`.
- `plan`: Same persona but read-only (write/edit/bash disabled) for dry-run analysis.
- `research-specialist`: Subagent for investigation; read-only and biased toward storing results in Memento.

Add more subagents later by mirroring the pattern in `opencode.jsonc` and pointing to new prompt files if you want lighter-weight instructions.

## Facilitator Commands
The `/analyze`, `/model`, `/architect`, `/plan`, and `/tdd` commands now live in `command/` with the original facilitator copy. Each command runs as a subtask of the build agent so the main session stays clean.

## Instructions Library
All process handbooks from `~/.claude/processes` were copied to `instructions/` and internal references now point to `~/.config/opencode/instructions/...`. Agents should lazy-load only the documents they need.

## Next Steps / Open Work
1. Port the remaining 30+ specialist agent prompts from `~/.claude/agents` into OpenCode subagent definitions. The current config gives you the core trio (build, plan, research) to start.
2. Add any custom slash commands beyond the five facilitators if you relied on them in the prior Claude setup.
3. Verify permissions once you copy the files—OpenCode defaults differ from the old Claude environment. Adjust `tools` or per-command `agent` selection as needed.
4. If you have project-specific Marvin variations, create project-level `opencode.jsonc` files that extend this global config.

With these files copied, OpenCode mirrors the prior workflow: same persona, same processes, plus the familiar memento and time MCP servers.
