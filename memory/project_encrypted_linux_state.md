---
name: encrypted-linux-session-state-cross-vm-demo-complete-2026-05-11
description: Cross-VM + cross-host demos working. Asciicast recorded. In-VM gcc deferred.
metadata: 
  node_type: memory
  type: project
  originSessionId: e2774676-db4c-4930-b1f8-58c186399a05
---

**State as of 2026-05-11 night (commit 57d9510):** Phase 1 + Phase 2
both integrated into the rootfs end-to-end:
- hello calls libc with PERMUTED argument-register ABI (Phase 1)
- musl libc.a built with the scrambling GCC (Phase 1 internal)
- syscalls use PERMUTED numbers (Phase 2)

Bridge fix needed: kernel→userspace ELF entry is fixed by Linux
spec (args via canonical %rdi), but the C side compiled with
scrambling GCC reads args from the permuted register (%rdx for
seed A). `scripts/patch-musl-crt-arch.py` rewrites musl's
arch/x86_64/crt_arch.h asm to bridge.

Of the original 4 items:

1. **In-VM compile (deferred)** — bundled `/usr/local-gcc/` segfaults
   inside the VM because it's glibc-linked and glibc uses canonical
   syscalls our kernel doesn't have. Fix: build gcc statically linked
   against scrambled musl. See `docs/IN-VM-GCC-PATH.md`. Same
   blocker as Phase 1 rootfs-musl rebuild.
2. **Asciicast recorded** — `docs/demo.cast` (~6 KB).
3. **Phase 1 ABI scrambling for rootfs musl (deferred)** — same
   blocker as #1 (libgcc-against-musl bootstrap).
4. **Two-seed cross-VM test (working)** — seed-A binary hits #GP
   fault on seed-B kernel.

**Verified failure matrix:**
- seed-A hello on seed-A kernel → works
- seed-B hello on seed-B kernel → works
- seed-A hello on seed-B kernel → #GP fault (exitcode 0x0000000b)
- seed-A hello on stock ubuntu → segfault (exit 139)

**Existing artifacts on disk (gitignored under build/):**
- `image/bzImage` + `image/rootfs.cpio.gz` — seed-A image
- `image/hello` + `image/hello-seedA` — seed-A hello (same)
- `image-seedB/bzImage` + `image-seedB/rootfs.cpio.gz` — seed-B
  image (bundles both seed-B and seed-A hellos for cross-VM demo)
- `native-gcc/install/` — native x86_64 scrambling gcc (glibc-linked;
  doesn't work in-VM yet)

**How to resume / replay:**
```
cd /Users/archisgore/github/encrypted-execution/encrypted-linux
bash scripts/run-demo.sh             # full 4-step demo
asciinema play docs/demo.cast        # replay recording
```

**Rebuild from source if artifacts are gone:**
```
make test-image
bash scripts/build-image.sh                 # seed-A: kernel + musl + busybox + hello
bash scripts/rebuild-image-userland.sh      # patch musl asm + rebuild
bash scripts/assemble-initramfs.sh          # assemble seed-A initramfs
cp build/image/hello build/image/hello-seedA
bash scripts/build-second-seed.sh           # builds seed-B with bundled seed-A hello
```

**Important fixes documented in this session:**
- Boot needs ≥ 4 GB RAM (471 MB initramfs OOMs at 512 MB).
- musl has 7 hardcoded canonical syscall numbers in x86_64 inline
  asm. `scripts/patch-musl-syscalls.py` rewrites them.
- `busybox --install` hangs under QEMU TCG; use ln -sf with
  hardcoded applet list (in `scripts/assemble-initramfs.sh`).
- `gen-unistd-seeded.py` reads ./seed file ONLY (not env var).
  `scripts/build-second-seed.sh` swaps the file temporarily.
