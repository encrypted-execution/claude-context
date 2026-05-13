# claude-context

Portable handoff context for working with Claude on the
encrypted-execution stack ([encrypted-linux](https://github.com/encrypted-execution/encrypted-linux)
and [evasive-linux](https://github.com/encrypted-execution/evasive-linux)).

## What this repo is

A snapshot of everything a future Claude session needs to know to
pick up where the last one left off, *without* having to scroll
through the prior conversation. It exists because Claude sessions
have a context window and conversations don't carry over; the
files here are the durable handoff.

## If you're Claude landing in a new session

Read **[00-START-HERE.md](00-START-HERE.md)** first. It's the
single-page brief that situates the rest. Then follow whichever of
the numbered files is relevant to the user's current request.

## If you're a human reading this

You're looking at how Archis collaborates with Claude on the
encrypted-execution / evasive-linux work. Nothing here is secret;
the projects are public, the user's communication preferences are
fine to share, and the gotchas section is where the engineering
lessons live.

## Layout

```
claude-context/
├── README.md                       # this file
├── 00-START-HERE.md                # single-page brief, read first
├── 01-projects.md                  # what's built, what's shipped, what's where
├── 02-pending.md                   # active items + the immediate next-actions
├── 03-decisions-and-history.md     # what we worked through and why
├── 04-gotchas.md                   # known engineering pitfalls
├── 05-preferences.md               # how Archis works
├── 06-outreach-plan.md             # the visibility/publicity strategy
├── 07-future-work.md               # backlog of ideas, abandoned threads
└── memory/                         # snapshot of Claude's auto-memory
    ├── README.md
    ├── MEMORY.md
    ├── user_profile.md
    ├── project_encrypted_linux.md
    ├── project_encrypted_linux_state.md
    ├── reference_encrypted_execution.md
    ├── feedback_permission_capture.md
    └── feedback_seed_hardening_backlog.md
```

## Refreshing this repo

This is a snapshot from the **session that ended on 2026-05-13**.
It will go stale. Recommended refresh policy:

- After each substantial session, ask Claude to update relevant
  files (especially `02-pending.md` and `01-projects.md`).
- The `memory/` directory mirrors what's auto-managed at
  `~/.claude/projects/-Users-archisgore-github/memory/`. That
  directory is the *live* source; this `memory/` is a static copy
  for portability.

## Authoritative pointers

- **Live CLAUDE.md** — `/Users/archisgore/github/archisgore/claude/CLAUDE.md`
  (referenced from the workspace's CLAUDE.md).
- **Live auto-memory** —
  `/Users/archisgore/.claude/projects/-Users-archisgore-github/memory/`
- **encrypted-linux** — <https://github.com/encrypted-execution/encrypted-linux>
- **evasive-linux** — <https://github.com/encrypted-execution/evasive-linux>
- **Encrypted Execution paper** — <https://www.encrypted-execution.com>

## License

Apache-2.0. See `LICENSE`.
