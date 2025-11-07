# Collaboration Protocols

## Core Principle

**All creative and decision work happens collaboratively in the main conversation with active user participation.**

- Specialist agents make changes with Write/Edit (or return recommendations) in small, reviewable increments.
- Agents must pause after each increment so the user can inspect or adjust the work manually.
- There is no automatic resume mechanism; continuity comes from clear summaries plus the Memento memory protocol.

## Collaboration Flow

1. **Launch & Context** – Main conversation invokes the specialist, provides scope, success criteria, and when to pause.
2. **Load Context** – Agent anchors time, loads relevant memories, and reviews nearby files before touching anything.
3. **Make a Change** – Agent applies a targeted change or drafts a recommendation, then lists exactly what was touched.
4. **Pause for Review** – Agent stops work and asks the user to review or modify the result. No further edits until feedback arrives.
5. **User Feedback** – The user may accept, edit, or add `QUESTION:` comments directly in the files/messages.
6. **Follow-up Invocation** – Main conversation relaunches the agent (optionally referencing the prior task) and includes a summary of user feedback.
7. **Continue** – Agent re-reads affected files, acknowledges the user’s modifications, answers any questions, and repeats the loop until the task is done.

## Maintaining Continuity

- Store key observations, TODOs, and outstanding questions in Memento before every pause.
- When re-invoked, call `mcp__time__get_current_time`, review the relevant memories, and verify file state before continuing.
- Do not redo already accepted work unless the user explicitly requests a revision.

## QUESTION: Comment Protocol

If the user adds a `QUESTION:` comment inside a file:

1. Re-read the surrounding code or prose to understand the context.
2. Answer the question explicitly in your response.
3. Remove or revise the comment as part of your next edit so the file stays clean.

## Using the AskUserQuestion Tool

- Present 2–4 well-defined options when user decisions are required.
- Capture trade-offs succinctly so the user can choose quickly.
- Wait for the tool response and incorporate the decision before proceeding.

## When to Pause

- After making any change that needs human review.
- When you need clarification or additional inputs.
- Before handing the task to another specialist.

## When Continuing After Feedback

1. Re-read every file you previously edited (or recommended edits for).
2. List the user’s adjustments and acknowledge them.
3. Address any `QUESTION:` comments or new requests.
4. Update Memento with a fresh summary of what remains.

## Verification Checklist

Before handing control back to the main conversation:

- ✅ You recorded what changed and why.
- ✅ All outstanding questions are tracked (via AskUserQuestion or Memento).
- ✅ Files you touched were re-read after the user’s edits.
- ✅ The next action for the user or next agent is clearly stated.
