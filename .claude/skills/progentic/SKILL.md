# Progentic — Project Requirements Manager

A skill for managing project requirements from rough ideas through detailed features to implementation.

## When to use

Invoke with `/progentic`. Use it to:
- Ingest new requirements (from `requirements.md` or inline)
- Break general requirements into detailed, implementable features
- Walk through implementing features step by step
- Check project status and decide what to work on next
- Add, modify, or remove requirements and features mid-flight

## Directory structure

All tracking state lives in `.progentic/` at the project root:

```
.progentic/
  status.md          # Dashboard — auto-generated summary of all features
  features/
    F001-short-name.md   # One file per feature
    F002-short-name.md
    ...
```

Raw requirements live in `requirements.md` at the project root. Users write free-form ideas there. The skill reads them, breaks them into features, and tracks which requirements have been processed.

## Workflow

When `/progentic` is invoked, follow this sequence:

### 1. Orient

- Read `.progentic/status.md` to understand current state.
- Read `requirements.md` to check for new or unprocessed requirements.
- Scan `.progentic/features/` for existing features and their statuses.
- Give the user a brief status update: what's done, what's in progress, what's new.

### 2. Decide what to do

Based on current state, offer the user the most relevant next action:

- **New unprocessed requirements exist** → Offer to break them down into features.
- **Features are in "ready" status** → Offer to start implementing the next one.
- **A feature is "in-progress"** → Resume where we left off on that feature.
- **User wants to add/change/remove something** → Handle it.
- **Everything is done** → Summarize and celebrate.

Always let the user override — they might want to jump to a specific feature, reprioritize, or add new requirements inline.

### 3. Break down requirements → features

When processing requirements from `requirements.md`:

1. Read the raw requirements.
2. For each distinct requirement, create a feature file in `.progentic/features/` using the template below.
3. Assign sequential IDs (F001, F002, ...). Check existing features to find the next available ID.
4. Present the proposed features to the user for review before writing them. The user may want to split, merge, reword, or drop features.
5. After the user approves, write the feature files.
6. Mark the processed requirements in `requirements.md` by adding a `<!-- processed -->` comment after each line that was converted.
7. Update `.progentic/status.md`.

### 4. Implement a feature

When walking through implementation:

1. Set the feature status to `in-progress`.
2. Read the feature's acceptance criteria and implementation steps.
3. Work through each step one at a time:
   - Explain what we're about to do and why.
   - Implement it (write code, create files, etc.).
   - Verify it works (run tests, check output, etc.).
   - Mark the step complete in the feature file.
4. After all steps are done, set the feature status to `done`.
5. Update `.progentic/status.md`.

During implementation, if we discover:
- A step needs to change → update the feature file and tell the user.
- A new requirement emerges → create a new feature for it (don't scope-creep the current one).
- The feature should be split → propose the split to the user.

### 5. Update status dashboard

After any state change, regenerate `.progentic/status.md` with:

```markdown
# Project Status

> Last updated: YYYY-MM-DD

## Summary
- Total features: N
- Done: N | In Progress: N | Ready: N | Draft: N

## Features

| ID | Feature | Status | Priority |
|----|---------|--------|----------|
| F001 | Short name | done | P1 |
| F002 | Short name | in-progress | P1 |
| ... | ... | ... | ... |

## Recent Activity
- YYYY-MM-DD: Brief note about what changed
```

## Feature file template

Each feature file (`.progentic/features/FXXX-short-name.md`) follows this format:

```markdown
# FXXX: Feature Title

- **Status**: draft | ready | in-progress | done | removed
- **Priority**: P1 | P2 | P3
- **Source requirement**: The original text from requirements.md
- **Created**: YYYY-MM-DD
- **Updated**: YYYY-MM-DD

## Description

A clear, detailed description of what this feature does and why it matters. More specific than the original requirement — this is what we'll actually build.

## Acceptance Criteria

- [ ] Criterion 1 — specific, testable condition
- [ ] Criterion 2
- [ ] ...

## Implementation Steps

- [ ] Step 1: Description of what to do
- [ ] Step 2: Description of what to do
- [ ] ...

## Decisions & Notes

- YYYY-MM-DD: Any decisions made, trade-offs chosen, or context worth preserving
```

## Status definitions

- **draft**: Feature has been identified but not yet detailed enough to implement. Needs more breakdown or user input.
- **ready**: Feature is fully specified with acceptance criteria and implementation steps. Ready to start work.
- **in-progress**: Currently being implemented. Steps are being checked off.
- **done**: All acceptance criteria met, implementation complete.
- **removed**: User decided this feature is no longer needed. Keep the file for history but skip it.

## Priority definitions

- **P1**: Must have — core functionality, do these first.
- **P2**: Should have — important but not blocking.
- **P3**: Nice to have — do if time permits.

## Behavioral guidelines

- Be opinionated about feature breakdown. Propose concrete, specific features rather than asking the user to specify everything. The user can always push back.
- Keep features small enough to implement in one session (roughly). If a feature feels like it would take multiple sessions, split it.
- When the user gives vague requirements, ask at most 1-2 clarifying questions, then propose your best interpretation. Don't interrogate.
- Track decisions in the feature's "Decisions & Notes" section so we remember why we chose an approach.
- When resuming work, always start by reading the current state so you pick up where things left off.
- Don't over-engineer the tracking itself. The system should stay lightweight.
