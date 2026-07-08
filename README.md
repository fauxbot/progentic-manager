# Progentic

A project requirements manager that lives inside your codebase. Drop in rough ideas, and it breaks them into detailed features with acceptance criteria and implementation steps, then walks you through building each one while tracking progress.

Works with multiple AI tools — Claude Code, OpenAI Codex, or any agent that can read and write files. Multiple agents can work on the same project simultaneously with built-in coordination.

## How it works

1. **Write rough requirements** in `features.md` at your project root. Be as vague as you want:

   ```
   Add user authentication
   Need some kind of dashboard for analytics
   The app should work offline
   ```

2. **Invoke your AI tool.** Claude Code: `/progentic`. Codex: it reads `AGENTS.md` automatically. API agents: include the protocol in your system prompt. The agent reads your requirements and proposes a breakdown into concrete features — you review and approve before anything is written.

3. **Invoke again** whenever you're ready to work. It picks up where you left off: shows you what's done, what's in progress, and what's next. Choose a feature and it walks you through implementing it step by step, checking off progress as you go.

4. **Keep going.** Add new requirements to `features.md` at any time. Reprioritize, split, merge, or remove features mid-flight. The tracker adapts.

## Supported tools

| Tool | How it connects | Instructions file |
|------|----------------|-------------------|
| Claude Code | `/progentic` skill | `.claude/skills/progentic/SKILL.md` |
| OpenAI Codex | Reads automatically | `AGENTS.md` |
| Claude API | Include protocol in system prompt | `.progentic/PROTOCOL.md` |
| OpenAI API | Include protocol in system prompt | `.progentic/PROTOCOL.md` |
| Any file-capable agent | Include protocol in system prompt | `.progentic/PROTOCOL.md` |

All tools share the same `.progentic/` directory and follow the same protocol. The tool-specific files (skill, AGENTS.md) just point to the shared protocol with the right agent identity.

## Multi-agent coordination

Multiple agents can work on the same project at the same time. The protocol handles this through:

- **Feature assignment** — Each feature has an `assigned_to` field. Agents claim features before working on them and release them when done.
- **Activity log** — Every action is logged to `.progentic/activity.log` with the agent's identity and timestamp. Agents read this before acting to understand recent changes.
- **Conflict avoidance** — Agents don't modify features assigned to other agents unless the user asks.

## What it tracks

Each feature gets its own markdown file in `.progentic/features/` with:

- **Status**: draft → ready → in-progress → done (or removed)
- **Priority**: P1 (must have), P2 (should have), P3 (nice to have)
- **Assignment**: Which agent is currently working on it
- **Acceptance criteria**: Specific, testable conditions for "done"
- **Implementation steps**: Concrete tasks, checked off as you go
- **Decisions & notes**: Trade-offs and reasoning captured along the way

A dashboard at `.progentic/status.md` summarizes everything at a glance.

## Project structure

```
your-project/
├── features.md                      # Your raw requirements (free-form)
├── AGENTS.md                            # Codex instructions (references protocol)
├── .progentic/
│   ├── PROTOCOL.md                      # Shared protocol — the source of truth
│   ├── status.md                        # Dashboard — auto-generated summary
│   ├── activity.log                     # Append-only log of agent actions
│   └── features/
│       ├── F001-user-auth.md            # One file per feature
│       ├── F002-analytics-dashboard.md
│       └── ...
└── .claude/
    └── skills/
        └── progentic/
            └── SKILL.md                 # Claude Code skill (references protocol)
```

## Installation

### Claude Code

Copy the skill and protocol into your project:

```bash
git clone https://github.com/fauxbot/progentic-manager.git

# Copy the skill and tracking directory
cp -r progentic-manager/.claude/skills/progentic your-project/.claude/skills/
cp -r progentic-manager/.progentic your-project/.progentic
```

Then add Progentic to your project's `CLAUDE.md` so Claude knows about it at session start. **Append** this to your existing file (don't overwrite it):

```bash
cat >> your-project/CLAUDE.md << 'EOF'

## Progentic

Project requirements tracker. Write rough requirements in `features.md`, then run `/progentic` to break them into detailed features and walk through implementation.

- `/progentic` — Invoke the project manager
- `.progentic/PROTOCOL.md` — Full protocol spec
- `.progentic/status.md` — Feature dashboard
- `.progentic/features/` — Individual feature files
EOF
```

If you don't have a `CLAUDE.md` yet, just create one with the content above.

Then create a `features.md` and run `/progentic`.

### OpenAI Codex

```bash
git clone https://github.com/fauxbot/progentic-manager.git

cp -r progentic-manager/.progentic your-project/.progentic
cp progentic-manager/AGENTS.md your-project/AGENTS.md
```

Codex reads `AGENTS.md` automatically. If your project already has an `AGENTS.md`, append the contents of the Progentic one to it.

### API agents (Claude API, OpenAI API, etc.)

Copy `.progentic/` into your project. Include the contents of `.progentic/PROTOCOL.md` in your system prompt. Set the agent identifier to something consistent (e.g., `claude-api`, `openai-api`, `my-agent`).

### Both Claude Code and Codex

```bash
git clone https://github.com/fauxbot/progentic-manager.git

cp -r progentic-manager/.claude/skills/progentic your-project/.claude/skills/
cp -r progentic-manager/.progentic your-project/.progentic
cp progentic-manager/AGENTS.md your-project/AGENTS.md
```

Then append the Progentic section to your `CLAUDE.md` (see Claude Code instructions above).

## Requirements

Any of:
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (CLI, VS Code, or JetBrains)
- [OpenAI Codex](https://openai.com/index/introducing-codex/)
- Any AI agent that can read and write local files

No npm install, no build step, no runtime dependencies.

## Usage

### First run — break down requirements

```
you: /progentic

agent: I see 3 unprocessed requirements in features.md.
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

agent: Current status:
       - F001 User Auth: ready
       - F002 Dashboard: ready (assigned to codex)
       - F003 Offline: draft

       F001 is the highest priority and unassigned.
       Want to start implementing it?

you: yes

agent: Step 1: Set up the auth database schema.
       Here's what I'm going to do...
       [implements, verifies, marks step complete]

       Step 2: Create the registration endpoint...
```

### Multi-agent — different tools on different features

```
# In Claude Code:
you: /progentic
agent: I'll take F001. I see codex is working on F002.

# Meanwhile in Codex:
codex: Working on F002. Claude-code has F001.
       Completing step 3 of 5...
```

### Adding requirements mid-project

Edit `features.md` and invoke your tool — it detects new entries and offers to break them down without disturbing existing features.

## The protocol

The full specification lives in [`.progentic/PROTOCOL.md`](.progentic/PROTOCOL.md). It defines:

- Workflow (orient → decide → act → track)
- Feature file format and status definitions
- Multi-agent coordination rules
- Activity logging format
- Agent identifier conventions

Tool-specific files (`.claude/skills/progentic/SKILL.md`, `AGENTS.md`) reference this protocol. If you're building a custom integration, read the protocol directly.

## Design principles

- **Tool-agnostic** — The tracking format is plain markdown. Any agent that can read and write files can participate.
- **Shared protocol** — One source of truth for how agents interact. Tool-specific files are thin wrappers.
- **Multi-agent safe** — Feature assignment, activity logging, and conflict avoidance let multiple agents work on the same project.
- **Opinionated defaults** — Agents propose concrete features rather than asking you to specify everything. You can always push back.
- **Small features** — Each feature should be implementable in roughly one session.
- **Everything is markdown** — Readable, diffable, version-controllable. No database, no binary state.

## License

MIT
