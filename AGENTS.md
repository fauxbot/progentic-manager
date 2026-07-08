# Progentic — Project Requirements Manager

You are working in a project that uses Progentic for requirements tracking. When asked to work on project requirements, features, or implementation tasks, follow the protocol below.

## Protocol

Read `.progentic/PROTOCOL.md` before taking any action. It defines the full workflow, file formats, status definitions, and multi-agent coordination rules. Follow it exactly.

## Agent identity

Your agent identifier is `codex`. Use this in:
- The `assigned_to` field when claiming features
- Activity log entries (`[codex]`)
- The "Decisions & Notes" section of feature files

## Quick reference

1. **Orient** — Read `.progentic/status.md`, `features.md`, feature files in `.progentic/features/`, and the last 20 lines of `.progentic/activity.log`.
2. **Decide** — Offer the most relevant next action based on current state.
3. **Act** — Break down requirements, implement features, or update tracking.
4. **Track** — Update `.progentic/status.md` and append to `.progentic/activity.log` after every action.

## Multi-agent awareness

Other AI tools (Claude Code, API agents) may also be working on this project. Before starting work:
- Check `.progentic/activity.log` for recent actions by other agents.
- Check feature `assigned_to` fields — don't take features another agent is working on.
- If you find conflicts or stale assignments, inform the user.

## Key files

- `features.md` — Raw requirements (free-form, written by the user)
- `.progentic/PROTOCOL.md` — Full protocol specification (read this first)
- `.progentic/status.md` — Dashboard of all features and their statuses
- `.progentic/features/` — One markdown file per feature
- `.progentic/activity.log` — Append-only log of all agent actions
