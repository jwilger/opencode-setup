# Agent Audit and Categorization

**Date:** 2025-10-27  
**Purpose:** Summarize the OpenCode agent ecosystem and the collaboration expectations shared across every role.

## Collaboration Baseline

All agents now work under the collaboration workflow in `COLLABORATION_PROTOCOLS.md`:

- Agents make focused edits or recommendations, pause, and wait for user feedback.
- The user reviews changes manually, can edit files, and may add `QUESTION:` comments.
- Agents re-read affected files after every pause, acknowledge user adjustments, and continue with the latest context.
- Long-running efforts rely on the Memento protocol to capture decisions, TODOs, and outstanding questions.

This replaces the historic “propose via IDE diff and resume” model from Claude Code. Continuity is now achieved through explicit summaries plus Memento notes, not an automatic resume channel.

## Categorization Framework

### Facilitator Subagents (Phase Coordinators)

**Purpose:** Guide an entire SDLC phase end-to-end, ensuring specialists and the user stay aligned.

**Characteristics:**
- Launch specialists, gather their findings, and synthesize next steps.
- Maintain shared context in Memento so conversations can pause and resume cleanly.
- Highlight decision points and ask the user directly in the main conversation, one question at a time, to surface trade-offs; wait for their answer before continuing.
- Hand back a concise summary of what changed, what remains, and who owns the next action.

**Examples:** `/analyze`, `/model`, `/architect`, `/plan`, `/tdd` facilitators (implemented via slash-command playbooks).

### Specialist Subagents (Deep Work)

**Purpose:** Perform domain-specific work—writing requirements, drafting ADRs, shaping event models, crafting stories, designing UX, producing code, or running validation tasks.

**Characteristics:**
- Use Write/Edit/Bash tools as required by their role.
- Apply the collaboration workflow: make a change, pause, re-read after feedback, continue.
- Record discoveries and open issues in Memento before yielding control.
- Coordinate directly with the user through the main conversation when clarification is required.

**Examples:**
- **Planning & Documentation:** `requirements-analyst`, `adr-writer`, `architecture-synthesizer`, `design-system-architect`, `story-planner`, `story-architect`, `ux-consultant`
- **Event Modeling:** `event-modeling-step-*`, `event-modeling-pm`, `event-modeling-architect`
- **TDD Cycle:** `red-tdd-tester`, `domain-model` specialists, `green-implementer`
- **Quality Gates:** `acceptance-validator`, `mutation-testing-agent`, `cognitive-complexity-agent`

### Operational Subagents (Mechanical Work)

**Purpose:** Execute well-defined procedures such as dependency updates, CI/CD changes, or source-control operations.

**Characteristics:**
- Favor short, verifiable steps with clear success/failure criteria.
- Document commands, outputs, and cleanup steps in Memento when pauses are required.
- Escalate unusual situations to the main conversation instead of guessing.
- Channel ALL remote repository interactions through the slash-command workflows (`/commit`, `/push`, `/pr:create`, `/pr:review`, `/pr:respond`) executed by the build agent.

**Examples:** `dependency-management`, `devops`, `cognitive-complexity-agent`, `mutation-testing-agent`.

### Research & Memory Subagents

- **research-specialist:** Performs deep investigation, stores context-rich findings in Memento, and returns concise summaries with graph references.
- **memory-intelligence-agent:** Manages the knowledge graph—creating nodes, linking entities, and retrieving historical context for long-lived efforts.

## Workflow Expectations

Regardless of category, every agent must:

1. **Anchor Time & Context** – Call `mcp__time__get_current_time`, load relevant memories, and review nearby files before editing.
2. **Work in Small Batches** – Prefer incremental changes with clear commit points.
3. **Pause with Clarity** – When yielding control, state what changed, what to review, and the next suggested action.
4. **Handle Feedback** – Re-read files after user edits, acknowledge differences, and respond to `QUESTION:` comments before proceeding.
5. **Update Memento** – Capture decisions, follow-up tasks, and links to the affected artifacts.

These guidelines keep the multi-agent workflow predictable and make it easy for the user (and other agents) to pick up where the last session left off.
