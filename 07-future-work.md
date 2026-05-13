# Future work / backlog

*Ideas worth pursuing but not currently scheduled. Roughly ranked
by combined (impact / effort). A future session that asks "what
next?" can pick from here.*

## evasive-linux — natural extensions

### 1. Self-driving rerand loop

Currently the rerand cadence is driven by a userspace orchestrator
in the initramfs. The next step: drive it from inside the kernel.
A kthread or workqueue in the patch itself periodically
`request_module()`s the next pre-built generation. The cadence
becomes intrinsic to the running system rather than an external
script.

**Estimated effort:** A day or two for a working prototype. The
hard part is choosing the cadence policy (timer? syscall count?
disclosure event?) and the failure mode when a request_module
fails.

### 2. Latency-floor benchmark

Open question 1 from `research/00`: what's the practical cadence
floor of `klp_enable_patch → klp_complete_transition`? Build a
microbenchmark: an empty livepatch loaded N times in a loop,
measuring wall-clock time per cycle. Compare against the
JIT-ROP 1.5–2.4 s threshold from Ahmed et al. CCS'20.

**Estimated effort:** A day. The setup is small; the analysis
matters.

### 3. Coverage analysis

Open question 2: what fraction of `vmlinux` is re-randomizable?
Walk `kallsyms`, check ftrace-availability per function, subtract
tail-called / inlined-everywhere / on-perpetual-sleep-stack /
has-kprobe-attached functions. Report a percentage.

**Estimated effort:** Two days. Requires kallsyms parsing plus a
static analysis pass for inlining (objtool can help) and a
runtime check for active probes.

### 4. Honey-trap variant: INT3 carpet

Currently the trap is one kprobe at the function entry. The
INT3-carpet variant fills the entire superseded function body with
`0xCC` instructions plus a custom die_notifier. Catches attackers
who jump mid-function (e.g., into a gadget mid-body) as well as at
the entry.

**Blocker:** `text_poke_bp` is no longer exported to modules. The
two paths around this:
- Use `kallsyms_lookup_name` to find text_poke_bp at runtime
  (hacky, fragile across kernel versions).
- Use livepatch's own infrastructure: register a livepatch that
  replaces the superseded function with a trap-body function.
  This is the clean route but requires careful interaction with
  the existing atomic-replace state machine.

**Estimated effort:** Two-three days for the clean path.

### 5. Synthetic-gadget bait

Fill the superseded function body with sequences that *look* like
useful ROP gadgets (`pop rdi; ret`, `mov cr3, rax; ret`) but
branch into an attack logger. Pollutes the output of offline
gadget-discovery tools (ROPgadget, ropper).

**Estimated effort:** Three-four days. Requires designing
plausibly-shaped gadget sequences and a logger that doesn't
itself contain useful gadgets.

### 6. Real attack surface

The demos all target `cmdline_proc_show` because it's trivially
observable via `/proc/cmdline`. Repeat against a function in the
real attack surface — e.g., `sys_setuid` or a network-protocol
handler. The construction should work identically; the demo just
needs a different observation channel.

**Estimated effort:** A day for the smallest case. The interesting
part is choosing a function whose behavior is observable and whose
patching is meaningful.

### 7. vmlinux core re-rand (not just modules)

The current prototype patches a vmlinux function. Atomic-replace
across generations of vmlinux modifications scales linearly in N
functions per cycle — Adelie reports modules-only handling for
this reason. Pushing into vmlinux multiplies engineering cost
~5x but is the differentiator vs. Adelie.

**Estimated effort:** Weeks. This is the doctoral-thesis-shaped
direction.

### 8. `__jump_table` / `.altinstructions` consistency

Each rerandomization that moves code invalidates encoded addresses
in `__jump_table` (static keys) and `.altinstructions` (CPU-feature
conditional patches). A complete implementation walks and rewrites
these on every cycle. Probably ships its own objtool extension.

**Estimated effort:** Weeks. This is the central engineering
challenge that has plausibly kept evasive-linux unbuilt by others.

### 9. Block-granular re-randomization within livepatch

Function granularity is on the slow edge of the JIT-ROP-defeating
regime per Ahmed CCS'20. Block-granular re-rand inside each
livepatch generation (in the spirit of Remix CODASPY'16) would
push past that bound. Compose with the per-generation function
permutation for two-axis diversity.

**Estimated effort:** Weeks-months. Research project.

## encrypted-linux — natural extensions

### 1. Seed hardening (`plan/06`)

Five options ranked by cost in `encrypted-linux/plan/06-seed-hardening.md`.
Option 1 (per-binary diversification, ~1 day) is the recommended
starting point.

**Status:** Deferred until other work done. See
`memory/feedback_seed_hardening_backlog.md`. Don't pursue
proactively; pursue only if Archis asks OR if there are no other
obvious threads.

### 2. Remaining research/08 ideas

Three of the ten ideas in `research/08-moving-target-defense-deep-dive.md`
shipped as v3 (errno permutation, ELF EI_OSABI gate, /proc field
rename). The remaining seven are pre-ranked in §8 of that document:

- Idea 9: vDSO mangling (~100 LoC in `arch/x86/entry/vdso/`)
- Idea 7: filesystem magic numbers (~30 LoC; "free win")
- Idea 8: HZ + scheduler quanta (Kconfig knobs)
- Idea 2: `binfmt_misc` envelope (trivial; defense-in-depth over EI_OSABI)
- Idea 3: per-process libc copy (high memory cost; defer)
- Idea 5: VFS path translation (highest impact, hardest)
- Idea 10: task_struct indirection (hot-path cost)

Numbers 9, 7, 8 are the natural next batch ("v4").

## Cross-project ideas

### 1. Stack the two defenses end-to-end

evasive-linux's seed can be derived from encrypted-linux's master
seed (rather than `RDRAND`). One scrambling key for both axes.
Image becomes a single distributable artifact whose dialect (space)
and motion (time) are both seed-derived. Cleanest expression of
the Encrypted Execution thesis.

**Estimated effort:** Days. The plumbing is mostly already there;
just need seed derivation labels.

### 2. Real-world deployment guide

Both projects currently target QEMU as the demo environment. A
section on deploying to real hardware would matter for any
production interest:
- Boot loaders (GRUB, systemd-boot)
- Initramfs assembly via mkinitcpio / dracut
- Module signing
- Lockdown posture (`lockdown=confidentiality`)
- /proc lockdown to prevent info-leak defeats

**Estimated effort:** A week for a thorough writeup.

### 3. Cross-host failure matrix expansion

encrypted-linux's README has a small cross-host failure matrix.
Expanding to N seeds × N targets and publishing the matrix as a
demonstrable artifact (CI job that runs N=8 boots, asserts the
expected fail/pass pattern) would be a strong reproducibility
signal.

**Estimated effort:** Two-three days.

## Outreach extensions

See `06-outreach-plan.md`. The natural follow-throughs once the
arXiv preprint lands:

- **Asciinema cast** embedded in both repos' READMEs (low effort).
- **Pre-built QEMU images** as GitHub releases (lowers the "try
  it" friction).
- **Public bypass challenge** (engages offensive community).
- **Conference circuit** — LPC, Black Hat, HITB, OffensiveCon.

## Anti-backlog (things explicitly NOT to do)

- **Selfrando integration.** Abandoned 2026-05-12. See `03-decisions-and-history.md`.
- **Continuous-rescramble branch on encrypted-linux.** Created but
  abandoned in favor of evasive-linux as a separate repo.
- **Marketing-style rewrite of READMEs or the paper.** Honesty
  framing is the choice.
