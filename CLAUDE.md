# Progentic Manager

Project requirements tracker that lives alongside your code. Works with Claude Code, OpenAI Codex, and any file-capable AI agent.

## How to use

1. Write rough requirements in `requirements.md` at the project root.
2. Run `/progentic` to process them into detailed features.
3. `/progentic` again to walk through implementation, check status, or modify features.

## Project structure

- `requirements.md` — Raw requirements (free-form input)
- `.progentic/PROTOCOL.md` — Shared protocol (the source of truth for all agents)
- `.progentic/status.md` — Dashboard of all features and their statuses
- `.progentic/activity.log` — Append-only log of agent actions
- `.progentic/features/` — One markdown file per feature with details, criteria, and steps
- `.claude/skills/progentic/` — Claude Code skill definition
- `AGENTS.md` — OpenAI Codex instructions

## Skills

- `/progentic` — Invoke the project manager. Reads current state, processes new requirements, walks through implementation.
