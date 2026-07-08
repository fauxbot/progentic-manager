# Progentic — Project Requirements Manager

A skill for managing project requirements from rough ideas through detailed features to implementation.

## When to use

Invoke with `/progentic`. Use it to:
- Ingest new requirements (from `FEATURES.md` or inline)
- Break general requirements into detailed, implementable features
- Walk through implementing features step by step
- Check project status and decide what to work on next
- Add, modify, or remove requirements and features mid-flight

## Protocol

Read `.progentic/PROTOCOL.md` before taking any action. It defines the full workflow, file formats, status definitions, and multi-agent coordination rules. Follow it exactly.

## Agent identity

Your agent identifier is `claude-code`. Use this in:
- The `assigned_to` field when claiming features
- Activity log entries
- The "Decisions & Notes" section of feature files

## Quick reference

The protocol covers everything, but here's the high-level flow:

1. **Orient** — Read `status.md`, `FEATURES.md`, feature files, and `activity.log`.
2. **Decide** — Offer the most relevant next action based on current state.
3. **Act** — Break down requirements, implement features, or update tracking.
4. **Track** — Update `status.md` and append to `activity.log` after every action.

## Multi-agent awareness

Other AI tools (Codex, API agents) may also be working on this project. Before starting work:
- Check `activity.log` for recent actions by other agents.
- Check feature `assigned_to` fields — don't take features another agent is working on.
- If you find conflicts or stale assignments, inform the user.
