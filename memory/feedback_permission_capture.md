---
name: Periodically capture permission grants
description: User wants permissions added to settings.json periodically, not just on request
type: feedback
originSessionId: e2774676-db4c-4930-b1f8-58c186399a05
---
When the user authorizes a command in a session (by approving the prompt or running it without complaint), add a matching `Bash(<command>:*)` entry to `~/.claude/settings.json` under `permissions.allow` so it doesn't prompt again in future sessions.

**Why:** The user explicitly asked on 2026-05-11 to "capture all the permissions I've granted so I'm not asked for them again. Keep doing that periodically in my settings.json." Stated reason: prompt fatigue. He has 100+ entries already in the global allowlist; he treats his settings.json as institutional memory of what he trusts.

**How to apply:** Every commit batch (or every ~10-20 tool calls), scan for new commands that prompted or ran in this session and aren't yet covered by `~/.claude/settings.json`. Append matching `Bash(...)` entries. Be conservative — never add `bash:*`, `python3:*`, `curl:*`, `make:*`, or `npm run *` to global; those are arbitrary-code-execution wildcards and belong in project-local at most. Safe additions: standalone utilities (`mkdir`, `objdump`, `nm`, `readelf`, `diff`, `cp`, etc.), git/gh/docker subcommands.

After updating settings.json, validate with `jq empty ~/.claude/settings.json` and report the new entry count to the user.
