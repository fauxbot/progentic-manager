# Progentic Manager

Project requirements tracker that lives alongside your code.

## How to use

1. Write rough requirements in `requirements.md` at the project root.
2. Run `/progentic` to process them into detailed features.
3. `/progentic` again to walk through implementation, check status, or modify features.

## Project structure

- `requirements.md` — Raw requirements (free-form input)
- `.progentic/status.md` — Dashboard of all features and their statuses
- `.progentic/features/` — One markdown file per feature with details, criteria, and steps
- `.claude/skills/progentic/` — The skill definition

## Skills

- `/progentic` — Invoke the project manager. Reads current state, processes new requirements, walks through implementation.
