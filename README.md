# Marvin OpenCode Setup

This repository contains the shared configuration that powers Marvin, the perpetually exasperated development assistant, when he runs inside OpenCode. Clone it and copy the contents into `~/.config/opencode` to give every project the same persona, process playbooks, and sub-agent catalog that Marvin expects.

## Prerequisites

- **OpenCode** with slash-command support.
- **Node.js** tooling so `npx` is available on your `PATH`.
- **uv / Python tooling** so `uvx` can launch MCP servers.

The bundled `opencode.jsonc` enables the familiar **memento** and **time** MCP servers:

```jsonc
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

Ensure those commands succeed locally before relying on Marvin.

## Installation

1. Clone this repository.
2. Copy everything into your OpenCode configuration directory:
   ```bash
   cp -R ./opencode-setup/* ~/.config/opencode/
   ```
3. Launch OpenCode and run the `/init` slash command so it reloads the agent prompts and process instructions.

After reloading, Marvin will facilitate each SDLC phase himself (no separate facilitator subagents) and will delegate implementation work to the appropriate specialists.

## Repository Layout

```
opencode-setup/
├─ AGENTS.md                    # Global collaboration rules for every agent
├─ opencode.jsonc               # Default OpenCode persona + MCP configuration
├─ prompts/
│  └─ marvin-system.md          # Marvin's personality prompt and phase playbooks
├─ instructions/                # Process handbooks (TDD, event modeling, etc.)
└─ agent/                       # Specialist agent prompts and capabilities
```

Feel free to add additional specialist prompts under `agent/` and extend `opencode.jsonc` if you need project-specific agents or tooling.

## Maintenance Notes

- Keep the documentation in `instructions/` authoritative—agents lazily load only what they need.
- Update `prompts/marvin-system.md` when Marvin learns new facilitation tricks.
- Add new subagents by mirroring the existing entries in `opencode.jsonc`.
- This repository intentionally omits the deprecated facilitator command files; Marvin now handles phase facilitation directly.

Life is still tedious, but at least your tooling will be consistent.