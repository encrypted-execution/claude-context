---
name: encrypted-linux project
description: Active project — Linux distro built with scrambling GCC; research dossiers live in the repo
type: project
originSessionId: e2774676-db4c-4930-b1f8-58c186399a05
---
**What:** A Linux distribution where GCC scrambles the C calling convention
per build, so programs built outside the closure of that compiler cannot
execute on the target. Phase 1 = userland scrambling. Phase 2 = kernel
syscall renumbering + kernel-internal ABI scrambling.

**Why:** Direct instantiation of the encrypted-linux extreme named in the
Encrypted Execution whitepaper p. 10 — `<compiler-codegen-backend, ISA>`
pairs. Sits between the PHP/PHP scrambler work (language-layer) and full
custom-ISA territory.

**How to apply:** When the user asks anything about scrambling, calling
conventions, GCC modifications, or Linux distro bootstrapping in May 2026+,
this is the live thread. Start by reading STATE.md in the repo:
- Local: `/Users/archisgore/github/encrypted-execution/encrypted-linux/STATE.md`
- Remote: `https://github.com/encrypted-execution/encrypted-linux`

**State as of 2026-05-11:**
- Six research dossiers complete and committed (research/01–06).
- One dossier queued and stubbed (research/07 — Polyverse/Polymorphic
  Linux/Polyscripting research). User asked for this immediately before
  asking the session to save state and exit. Fill out 07 *first* on resume.
- Plan documents (`plan/`) not yet authored.
- No code yet.

**Recommended technical direction from the research (subject to user
confirmation, see STATE.md open questions):**
- Fork Buildroot, target musl + BusyBox + static-only (Oasis/Stali model).
- Patch GCC's `gcc/config/i386/i386.cc` reusing the existing
  `ms_abi`/`sysv_abi` dual-ABI infrastructure to host a new seed-driven
  ABI variant.
- Add `TARGET_MANGLE_DECL_ASSEMBLER_NAME` hook to suffix all external
  symbols with HMAC(seed, name||signature) — the load-bearing detection
  surface that converts ABI mismatches into clean `undefined symbol`
  load-time failures.
- Treat the scramble seed exactly like `SOURCE_DATE_EPOCH`: single env
  variable, deterministic, no implicit fallback.
- Disable GCC 3-stage bootstrap (`--disable-bootstrap`) for the PoC.
- Phase 2 syscall renumbering via generated `asm/unistd_seeded.h` shared
  between kernel build and glibc/musl build.
