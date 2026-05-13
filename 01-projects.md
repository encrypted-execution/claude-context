# Projects — state, structure, and what's shipped

## encrypted-linux

[github.com/encrypted-execution/encrypted-linux](https://github.com/encrypted-execution/encrypted-linux)

**What it is.** A Linux distribution where the kernel, libc, and
compiler all agree on a per-build private dialect derived from a
single seed.

**What ships:**

| Layer | What it diversifies | Seed label |
|---|---|---|
| Syscall numbers | 64-bit overkill, HMAC-derived | `syscall.numbers` |
| Struct layouts | `RANDSTRUCT_FULL` via GCC plugin | `kernel.randstruct` |
| musl `__NR_*` | matches kernel | (same) |
| Register ABI | `x86_64_int_parameter_registers[6]` permuted | `user.abi` + `x86_64.arg_regs` |
| Symbol mangling | `__abi_<hex>` suffix per external function | `user.abi` (per symbol) |
| ELF entry bridge | musl `_start` bridges canonical→permuted | (uses `user.abi`) |
| Errno permutation | UAPI `asm-generic/errno{,-base}.h` + musl bits/errno.h | `kernel.errno` |
| ELF `EI_OSABI` gate | per-build byte, `binfmt_elf.c` rejects mismatched | `elf.osabi` |
| `/proc/[pid]/status` field rename | `task_mmu.c` field literals | `kernel.proc_schema` |

**Build pipeline:**
- Top-level orchestrators in `scripts/build-*.sh`.
- Kernel source at `/opt/linux` inside the `encrypted-linux-image-build`
  Docker container (Linux 6.6.30 LTS).
- musl source at `/opt/musl` inside the same container.
- Patches applied via Python scripts (`scripts/apply-*.py`,
  `scripts/patch-*.py`).
- Final image: `build/v3/{bzImage, hello, busybox, rootfs.cpio.gz}`.

**CI:** `.github/workflows/{ci,overkill}.yml`. CI green as of
2026-05-13 (commit `91fa0be` is the latest green main).

**Backlog:** `plan/06-seed-hardening.md` — five ranked options for
making the seed undiscoverable. Deferred until other work done; see
`memory/feedback_seed_hardening_backlog.md`.

## evasive-linux

[github.com/encrypted-execution/evasive-linux](https://github.com/encrypted-execution/evasive-linux)

**What it is.** Continuous in-kernel code re-randomization via Linux's
livepatch subsystem. Same image — different `.text` layout every cycle.
Plus: kprobe-based honey-traps at every superseded function's old
address.

**What ships (three demos):**

| Demo | What it proves | Trigger |
|---|---|---|
| 01 | Vanilla kernel + livepatch sample loads, runs, reverts cleanly | `make demo-01` |
| 02 | Two livepatches land at distinct kernel-virtual addresses | `make demo-02` |
| 03 + 03b | Five generations atomic-replace each other; each arms a kprobe trap at the previous; attacker dispatches a call to the first; trap fires and logs the intrusion with caller info | `make demo-03` |

**The killshot from demo 03:**

```
attacker: dispatching call to 0xffffffffa0000023 ...
evasive-linux HONEY-TRAP: superseded patch 02 hit at 0xffffffffa0000027
    (caller=evasive_lp_01_cmdline_show+0x5/0x18 [lp_01])
```

**Kernel built with every randomization knob:**
`CONFIG_RANDOMIZE_BASE`, `RANDOMIZE_MEMORY`, `VMAP_STACK`,
`RANDOMIZE_KSTACK_OFFSET`, `STACKPROTECTOR_STRONG`,
`SLAB_FREELIST_RANDOM`, `SHUFFLE_PAGE_ALLOCATOR`, `RANDSTRUCT_FULL`.

**CI:** `.github/workflows/ci.yml` — three demo jobs + one hygiene
job, all green as of 2026-05-13 (`0ed048c`+).

**Hygiene files:** `CONTRIBUTING.md`, `SECURITY.md`, `.editorconfig`,
top-level `Makefile`. Apache-2.0.

**Research dossiers:**
- `research/00-linux-livepatch-internals.md`
- `research/01-continuous-rerandomization-prior-art.md`
- `research/02-honeytrap-old-code-prior-art.md`

**Paper:** `paper/main.tex` — 12-page system paper in LaTeX,
compiles clean in `texlive/texlive` Docker image, refs in
`paper/refs.bib`. **Not submitted to arXiv yet.**

## Relationship between the two

| Axis | encrypted-linux | evasive-linux |
|---|---|---|
| When | Build time | Runtime |
| Granularity | Every contract (syscall, ABI, errno, `/proc`, `EI_OSABI`, struct) | Function code layout |
| Cardinality | Per-build (one dialect per image) | Per-cycle (many layouts per boot) |
| Defense mode | Stale tooling can't compile or run | Stale pointers can't dereference |
| Telemetry | None (silent rejection) | Yes (honey-trap on superseded code) |
| Seed source | `./seed` file (HMAC-SHA256 labels) | Kernel PRNG per cycle, optionally chained to encrypted-linux master seed |

Both projects share the **Encrypted Execution thesis**: diversify
every contract an attacker depends on, at every layer where you can
afford to. encrypted-linux diversifies in space, evasive-linux in
time. Stacked, they get you both.

## PHP scrambler (`php-v2`)

[github.com/encrypted-execution/php-v2](https://github.com/encrypted-execution/php-v2)

Older language-layer instantiation of the same thesis. Not actively
being touched as of this snapshot, but it's part of the body of work
and Archis may pivot back. Reference notes in
`memory/reference_encrypted_execution.md`.

## Polyscripted-wordpress

[github.com/archisgore/polyscripted-wordpress](https://github.com/archisgore/polyscripted-wordpress)

WordPress integration of the PHP scrambler. Also dormant. Reference
in memory.
