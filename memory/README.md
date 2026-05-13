# Memory snapshot

This directory mirrors the contents of Claude's auto-memory at
`/Users/archisgore/.claude/projects/-Users-archisgore-github/memory/`
as of **2026-05-13**.

## What auto-memory is

Claude has a persistent, file-based memory system that survives
across sessions. The schema is documented in Claude's system prompt;
each file has frontmatter with `name`, `description`, and a `type`
of `user`, `feedback`, `project`, or `reference`. The
`MEMORY.md` file is the index — one line per memory, loaded into
every session's context automatically.

## Why mirror it here

Three reasons:

1. **Portability.** If Archis works from a different machine, or
   shares the project with a collaborator who runs their own Claude,
   the auto-memory directory isn't accessible. The mirror is.

2. **Provenance.** Auto-memory updates silently. The mirror is a
   timestamped snapshot — useful for understanding "what did Claude
   know on date X."

3. **Onboarding.** A future session that reads this directory
   gets the same starting context that the auto-memory provides,
   without depending on the auto-memory file paths being
   accessible.

## Files

| File | Type | One-line summary |
|---|---|---|
| `MEMORY.md` | index | the auto-memory index |
| `user_profile.md` | user | who Archis is + how he works |
| `project_encrypted_linux.md` | project | encrypted-linux project state (pre-evasive-linux) |
| `project_encrypted_linux_state.md` | project | mid-build session-state snapshot from 2026-05-11 |
| `reference_encrypted_execution.md` | reference | pointers to paper, PHP scrambler, polyscripting work |
| `feedback_permission_capture.md` | feedback | policy: capture granted permissions to settings.json periodically |
| `feedback_seed_hardening_backlog.md` | feedback | seed-hardening is deferred backlog; don't pursue proactively |

## Things this snapshot does *not* include

The mirror covers Claude's *auto-managed* memory. It doesn't
include:

- The user's `~/.claude/settings.json` (which has 100+ `Bash(...)`
  permission entries). That file is more sensitive and should stay
  local.
- The authoritative `CLAUDE.md` at
  `/Users/archisgore/github/archisgore/claude/CLAUDE.md`. That's a
  larger file with general collaboration guidance, also outside
  the scope of this snapshot.
- Conversation transcripts. Those are at
  `/Users/archisgore/.claude/projects/-Users-archisgore-github/<session-id>.jsonl`
  and stay private to the user.

## Refreshing

If a future session wants to update this directory:

```bash
cp /Users/archisgore/.claude/projects/-Users-archisgore-github/memory/*.md \
   <this directory>/
```

Or, equivalently:
```bash
rsync -av --delete \
    /Users/archisgore/.claude/projects/-Users-archisgore-github/memory/ \
    <this directory>/
```
(but `rsync --delete` will remove the `README.md` you're reading;
the simple `cp` form is safer.)

## Reading order

If you're a future Claude session pulling context from this mirror:

1. `user_profile.md` — who you're working with.
2. `project_encrypted_linux.md` — the active body of work.
3. `feedback_permission_capture.md` and
   `feedback_seed_hardening_backlog.md` — standing rules and
   deferred work.
4. `reference_encrypted_execution.md` — where things live.

The MEMORY.md index is one-line summaries; useful as a quick
overview before drilling in.
