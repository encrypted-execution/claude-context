---
name: Encrypted Execution stack — where to find what
description: Pointers to the Encrypted Execution paper, PHP scrambler repos, and the WordPress polyscripting integration
type: reference
originSessionId: e2774676-db4c-4930-b1f8-58c186399a05
---
Public material:
- Paper: `https://www.encrypted-execution.com` → `whitepaper.pdf`
  (11 pages, Archis Gore, 2025-07-27).
- Author email: archis@encrypted-execution.com / me@archisgore.com.
- Patent: USPTO 10,733,303 (Polyverse Corporation assignee; pledged
  public domain by author).

Local checkouts:
- PHP scrambler (active): `/Users/archisgore/github/encrypted-execution/php-v2/`
  - Go scrambler at `tools/scrambler/` — `scrambler.go`,
    `dictionaryHandler.go`, `randomizeString.go`.
  - Build pipeline: `scripts/recompile-php.sh` (re2c + bison + incremental
    make).
  - Dictionary written to `/var/lib/encrypted-execution/token-map.json`
    at runtime.
- PHP scrambler (legacy / pre-mortem docs):
  `/Users/archisgore/github/encrypted-execution/php/`
  - `SYMBOL_SCRAMBLING.md`, `SYMBOL_SCRAMBLING_ANALYSIS.md` —
    documentation of why operator-level scrambling fails.
- WordPress integration: `/Users/archisgore/github/archisgore/polyscripted-wordpress/`
  - `scripts/scramble.sh` — temp-dir scramble + atomic swap pattern.
  - `scripts/dispatch.sh` — "merge mode" for re-scrambling when mounted
    source changes.
- Active spinoff project: `/Users/archisgore/github/encrypted-execution/encrypted-linux/`
  (research dossiers; see `project_encrypted_linux.md` memory).

Polyverse historical work — needs research, not yet catalogued. See
`research/07-polyverse-polymorphic-linux.md` in the encrypted-linux repo
for the charter.
