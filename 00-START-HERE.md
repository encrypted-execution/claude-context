# Start here

*A future Claude session that opens this repo first should read this
one page, then go look at whatever the user is currently asking
about. Subsequent files have the depth.*

## Who you're working with

**Archis Gore** — security researcher, author of the [Encrypted
Execution](https://www.encrypted-execution.com) paper (2025), former
Polyverse CTO/founder. Email `me@archisgore.com`. Holder of USPTO
patent 10,733,303, which he publicly pledged to the public domain
in 2025.

His communication style: direct, technically confident, allergic
to marketing language. He wants honest threat-modeling ("this is a
diversity defense, not a confidentiality boundary"), not overclaim.
He's comfortable letting Claude work autonomously for long stretches
and dislikes being asked for permission on every step. See
[05-preferences.md](05-preferences.md) for detail.

## What he's building

Two sibling projects under the
[encrypted-execution](https://github.com/encrypted-execution) GitHub org:

1. **encrypted-linux** — A Linux distribution where the kernel,
   libc, and compiler all agree on a *per-build* private dialect:
   permuted syscall numbers, permuted x86_64 calling-convention
   register order, symbol mangling, `CONFIG_RANDSTRUCT_FULL`,
   permuted errno, permuted `/proc` field names, per-build
   `EI_OSABI` gate. **Build-time** scrambling. Shipped, CI green.

2. **evasive-linux** — Continuous in-kernel code re-randomization
   via Linux's livepatch subsystem. Every cycle, the layout in
   `.text` is different. Plus: kprobe-based honey-traps at every
   superseded function's old address. **Runtime** scrambling.
   Shipped, CI green, arXiv paper drafted.

Both projects are downstream of the Encrypted Execution thesis:
*diversify every contract an attacker depends on, at every layer
where you can afford to.* encrypted-linux diversifies in space;
evasive-linux diversifies in time.

A third repo, **claude-context** (this one), exists for handoff
between Claude sessions.

A fourth, **PHP scrambler** (`encrypted-execution/php-v2`), is the
language-layer instantiation of the same thesis — same body of
work, older. Mentioned in `memory/reference_encrypted_execution.md`.

## What's currently active

The state as of this snapshot (2026-05-13):

- **encrypted-linux** is feature-complete for the original spec.
  v3 demos (errno + ELF OSABI + /proc rename) are shipped and CI
  green. The seed-hardening backlog (`plan/06`) is the deferred
  next thread; see `memory/feedback_seed_hardening_backlog.md`.
- **evasive-linux** has three working demos (single livepatch, two
  livepatches, continuous rerand + honey-traps), Apache-2.0,
  CI-validated. A 12-page arXiv preprint is drafted under
  `paper/main.tex`. It has NOT been submitted to arXiv yet.
- **Outreach** plan exists in [06-outreach-plan.md](06-outreach-plan.md)
  but has not been executed beyond a Medium post and LinkedIn share.

For the immediate next-actions, see [02-pending.md](02-pending.md).

## What to do if the user opens with a new task

1. **Read [02-pending.md](02-pending.md)** — there are very likely
   open threads.
2. **Acknowledge memory implicitly**, not explicitly. Don't say "I
   read your memory"; just behave as if you remember.
3. **Default toward autonomous execution** — Archis dislikes being
   asked permission for routine work. He's lifted many `Bash(...)`
   permissions to `~/.claude/settings.json` for this reason.
4. **Don't restart abandoned threads** — see
   [03-decisions-and-history.md](03-decisions-and-history.md) for
   what's been considered and discarded.
5. **Match his tone** — punchy, technically dense, irreverent. His
   Medium posts at <https://medium.com/@archisgore> are the calibration.

## Repos and where they live on disk

```
/Users/archisgore/github/encrypted-execution/encrypted-linux/
/Users/archisgore/github/encrypted-execution/evasive-linux/
/Users/archisgore/github/encrypted-execution/claude-context/   # this repo
/Users/archisgore/github/encrypted-execution/php-v2/
/Users/archisgore/github/archisgore/polyscripted-wordpress/
/Users/archisgore/github/archisgore/claude/                    # authoritative CLAUDE.md
```

## Things you should NOT do

- **Don't suggest selfrando.** Archis explicitly abandoned that
  thread on 2026-05-12. The codebase is also a near-deprecated
  RunSafe Security commercial product now.
- **Don't suggest re-running long builds for trivial reasons.**
  Each demo-03 rebuild is ~15-20 minutes; treat as expensive.
- **Don't propose "let me check" without a plan.** Archis prefers
  one-or-two-message back-and-forths with concrete proposals.
- **Don't propose marketing-style language for the paper or the
  README.** Honest framing > selling. The paper draft is already
  calibrated for this.
