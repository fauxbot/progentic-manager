# Progentic

A project requirements manager that lives inside your codebase. Drop in rough ideas, and it breaks them into detailed features with acceptance criteria and implementation steps, then walks you through building each one while tracking progress.

It runs as a [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill — no server, no setup, no external dependencies. Just `/progentic` in your terminal.

## How it works

1. **Write rough requirements** in `requirements.md` at your project root. Be as vague as you want:

   ```
   Add user authentication
   Need some kind of dashboard for analytics
   The app should work offline
   ```

2. **Run `/progentic`**. It reads your requirements and proposes a breakdown into concrete features — each with a description, acceptance criteria, and implementation steps. You review and approve (or adjust) before anything is written.

3. **Run `/progentic` again** whenever you're ready to work. It picks up where you left off: shows you what's done, what's in progress, and what's next. Choose a feature and it walks you through implementing it step by step, checking off progress as you go.

4. **Keep going.** Add new requirements to `requirements.md` at any time. Reprioritize, split, merge, or remove features mid-flight. The tracker adapts.

## What it tracks

Each feature gets its own markdown file in `.progentic/features/` with:

- **Status**: draft → ready → in-progress → done (or removed)
- **Priority**: P1 (must have), P2 (should have), P3 (nice to have)
- **Acceptance criteria**: Specific, testable conditions for "done"
- **Implementation steps**: Concrete tasks, checked off as you go
- **Decisions & notes**: Trade-offs and reasoning captured along the way

A dashboard at `.progentic/status.md` summarizes everything at a glance.

## Project structure

```
your-project/
├── requirements.md                    # Your raw requirements (free-form)
├── .progentic/
│   ├── status.md                      # Dashboard — auto-generated summary
│   └── features/
│       ├── F001-user-auth.md          # One file per feature
│       ├── F002-analytics-dashboard.md
│       └── ...
└── .claude/
    └── skills/
        └── progentic/
            └── SKILL.md               # The skill definition
```

## Installation

Copy the skill into any project:

```bash
# Clone this repo (or just grab the files you need)
git clone https://github.com/fauxbot/progentic-manager.git

# Copy the skill and tracking structure into your project
cp -r progentic-manager/.claude/skills/progentic your-project/.claude/skills/
cp progentic-manager/CLAUDE.md your-project/CLAUDE.md
mkdir -p your-project/.progentic/features
```

Or just copy these files manually:
- `.claude/skills/progentic/SKILL.md` — the skill definition (required)
- `.progentic/` — the tracking directory (created automatically on first run)

Then create a `requirements.md` in your project root and start writing.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (CLI, VS Code extension, or JetBrains extension)

That's it. No npm install, no build step, no runtime dependencies.

## Usage

### First run — break down requirements

```
you: /progentic

claude: I see 3 unprocessed requirements in requirements.md.
        Here's how I'd break them down:

        F001: User Authentication (P1)
        - Email/password login and registration
        - Session management with secure tokens
        - ...

        F002: Analytics Dashboard (P2)
        - ...

        Want me to create these feature files, or would you
        like to adjust anything first?
```

### Implementation — step by step

```
you: /progentic

claude: Current status:
        - F001 User Auth: ready
        - F002 Dashboard: ready
        - F003 Offline: draft

        F001 is the highest priority. Want to start implementing it?

you: yes

claude: Step 1: Set up the auth database schema.
        Here's what I'm going to do...
        [implements, verifies, marks step complete]

        Step 2: Create the registration endpoint...
```

### Adding requirements mid-project

Just edit `requirements.md` and run `/progentic` — it detects new entries and offers to break them down without disturbing existing features.

### Checking status

Run `/progentic` at any time for a status update. It reads the dashboard and all feature files to give you an accurate picture.

## Design principles

- **Opinionated defaults** — It proposes concrete features rather than asking you to specify everything. You can always push back.
- **Small features** — Each feature should be implementable in roughly one session. If something is too big, it suggests splitting.
- **Decisions are tracked** — Trade-offs and reasoning are captured in each feature file so you remember why you chose an approach.
- **Everything is markdown** — Readable, diffable, version-controllable. No database, no binary state.
- **Lightweight** — The tracking system stays out of your way. No ceremony, no process overhead.

## License

MIT
