# Progentic Protocol

This document defines how any AI tool or agent interacts with the Progentic project tracking system. It is the single source of truth — tool-specific instructions (Claude Code skills, Codex AGENTS.md, system prompts) should reference this protocol, not duplicate it.

## Directory structure

```
project-root/
├── FEATURES.md              # Raw requirements (free-form input)
└── .progentic/
    ├── PROTOCOL.md              # This file
    ├── status.md                # Dashboard — auto-generated summary
    ├── activity.log             # Append-only log of agent actions
    └── features/
        ├── F001-short-name.md   # One file per feature
        ├── F002-short-name.md
        └── ...
```

## Workflow

### 1. Orient

Before taking any action, read the current state:

- Read `.progentic/status.md` for the dashboard.
- Read `FEATURES.md` for new or unprocessed requirements.
- Scan `.progentic/features/` for existing features and their statuses.
- Read `.progentic/activity.log` (last 20 lines) to see what other agents have done recently.
- Report a brief status update to the user.

### 2. Decide what to do

Based on current state, offer the most relevant next action:

- **New unprocessed requirements exist** → Offer to break them down into features.
- **Features are in "ready" status** → Offer to start implementing the next one.
- **A feature is "in-progress"** → Resume where it left off.
- **Features are in "backlog"** → Mention them as parked. Offer to promote one to draft/ready if the user wants.
- **User wants to park a feature** → Move it to backlog with a reason.
- **User wants to add/change/remove something** → Handle it.
- **Everything is done** → Summarize.

Always let the user override.

### 3. Break down requirements → features

1. Read the raw requirements.
2. For each distinct requirement, create a feature file using the template below.
3. Assign sequential IDs (F001, F002, ...). Check existing features to find the next available ID.
4. Present proposed features to the user for review before writing them.
5. After approval, write the feature files.
6. Mark processed requirements in `FEATURES.md` by adding `<!-- processed -->` after each converted line.
7. Update `.progentic/status.md`.
8. Log the action to `.progentic/activity.log`.

### 4. Implement a feature

1. **Claim the feature**: Set `assigned_to` to your agent identifier and status to `in-progress`. If the feature is already assigned to another agent, do not take it — inform the user.
2. Read the feature's acceptance criteria and implementation steps.
3. Work through each step one at a time:
   - Explain what you're about to do and why.
   - Implement it.
   - Verify it works.
   - Mark the step complete in the feature file (`- [x]`).
4. After all steps are done, set status to `done` and clear `assigned_to`.
5. Update `.progentic/status.md`.
6. Log the action to `.progentic/activity.log`.

During implementation, if you discover:
- A step needs to change → update the feature file and tell the user.
- A new requirement emerges → create a new feature for it (don't scope-creep the current one).
- The feature should be split → propose the split to the user.
- A feature is blocked or should be deferred → offer to move it to backlog with a reason.

### 4a. Manage the backlog

Features can be moved to `backlog` from any status (except `done`). When backlogging a feature:

1. Set status to `backlog`.
2. Set the `Backlog reason` field with a short explanation (e.g., "blocked by F003", "waiting on API access", "deferred to next quarter").
3. Clear `assigned_to` if it was set.
4. Update `.progentic/status.md`.
5. Log the action to `.progentic/activity.log`.

To promote a feature out of backlog:

1. Set status to `draft` or `ready` (depending on whether it needs more breakdown).
2. Clear the `Backlog reason` field (set to `—`).
3. Update `.progentic/status.md`.
4. Log the action to `.progentic/activity.log`.

### 5. Update status dashboard

After any state change, regenerate `.progentic/status.md`:

```markdown
# Project Status

> Last updated: YYYY-MM-DD

## Summary
- Total features: N
- Done: N | In Progress: N | Ready: N | Draft: N | Backlog: N

## Features

| ID | Feature | Status | Priority | Assigned To |
|----|---------|--------|----------|-------------|
| F001 | Short name | done | P1 | — |
| F002 | Short name | in-progress | P1 | claude-code |
| ... | ... | ... | ... | ... |

## Recent Activity
- YYYY-MM-DD [agent]: Brief note about what changed
```

### 6. Log activity

Append a line to `.progentic/activity.log` after every meaningful action:

```
YYYY-MM-DD HH:MM [agent-identifier] action: description
```

Examples:
```
2026-07-08 14:30 [claude-code] created: F001-user-auth, F002-dashboard from requirements
2026-07-08 14:35 [claude-code] started: F001-user-auth
2026-07-08 15:10 [codex] completed-step: F001-user-auth step 3
2026-07-08 15:20 [codex] completed: F001-user-auth
2026-07-08 15:25 [claude-code] created: F005-notifications (discovered during F001 implementation)
2026-07-08 15:30 [claude-code] backlogged: F005-notifications (blocked by F003)
2026-07-08 16:00 [codex] promoted: F005-notifications backlog → ready (F003 completed)
```

## Feature file template

```markdown
# FXXX: Feature Title

- **Status**: draft | ready | in-progress | done | backlog | removed
- **Priority**: P1 | P2 | P3
- **Assigned to**: agent-identifier | —
- **Source requirement**: The original text from FEATURES.md
- **Backlog reason**: Why this is parked (only when status is backlog) | —
- **Created**: YYYY-MM-DD
- **Updated**: YYYY-MM-DD

## Description

A clear, detailed description of what this feature does and why it matters.

## Acceptance Criteria

- [ ] Criterion 1 — specific, testable condition
- [ ] Criterion 2
- [ ] ...

## Implementation Steps

- [ ] Step 1: Description of what to do
- [ ] Step 2: Description of what to do
- [ ] ...

## Decisions & Notes

- YYYY-MM-DD [agent]: Any decisions made, trade-offs chosen, or context worth preserving
```

## Status definitions

- **draft**: Identified but not detailed enough to implement. Needs more breakdown or user input.
- **ready**: Fully specified with acceptance criteria and implementation steps. Ready to start.
- **in-progress**: Currently being implemented. Steps are being checked off.
- **done**: All acceptance criteria met, implementation complete.
- **backlog**: Parked — recognized as valid but can't or shouldn't be worked on now. Has a reason explaining why (dependency, blocked, deferred, low priority, etc.). Can be promoted back to draft or ready when the blocker clears or priorities shift.
- **removed**: No longer needed. File kept for history.

## Priority definitions

- **P1**: Must have — core functionality, do these first.
- **P2**: Should have — important but not blocking.
- **P3**: Nice to have — do if time permits.

## Multi-agent coordination

Multiple agents can work on the same project. These rules prevent conflicts:

1. **Check before claiming.** Before starting work on a feature, read its current state. If `assigned_to` is set to another agent, do not take it.
2. **One feature at a time.** An agent should only have one feature assigned to it at a time.
3. **Read the activity log.** Before taking any action, check the last 20 lines of `activity.log` to understand recent changes.
4. **Log everything.** Every action gets logged so other agents can see what happened.
5. **Don't edit others' in-progress work.** If another agent is working on a feature, don't modify its feature file or implementation unless the user explicitly asks.
6. **Release on completion.** Clear `assigned_to` when a feature is done or if you stop working on it.

### Agent identifiers

Use a short, consistent identifier:
- `claude-code` — Claude Code (CLI or IDE extension)
- `codex` — OpenAI Codex
- `claude-api` — Claude via API
- `openai-api` — OpenAI via API
- Custom identifiers are fine — just be consistent within a project.

## Behavioral guidelines

- Be opinionated about feature breakdown. Propose concrete features rather than asking the user to specify everything.
- Keep features small enough to implement in roughly one session. Split if too big.
- When the user gives vague requirements, ask at most 1-2 clarifying questions, then propose your best interpretation.
- Track decisions in each feature's "Decisions & Notes" section.
- When resuming work, always read current state first.
- Keep the tracking lightweight. Don't over-engineer the process.
