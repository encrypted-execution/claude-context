# Gotchas — the trenches lessons

*Every entry here cost at least one debug cycle to find. Future
sessions should consult this before reinventing.*

## Docker / build environment

### Docker Desktop containerd image store ate `docker run`

**Symptom:** After `docker build`, `docker run` fails with:
```
Unable to find image 'X:latest' locally
docker: Error response from daemon: pull access denied for X
```
Even though `docker image ls` shows the image.

**Root cause:** Docker Desktop on macOS with the containerd image
store enabled builds the image into the OCI cache. `docker run`
looks in the legacy daemon image store. They don't always agree.

**Fix:** Use `docker buildx build --load -t name -f Dockerfile .`
instead of `docker build`. The `--load` flag pushes the result
into the daemon image store.

### `--platform linux/amd64` on `docker run` fails on arm64 macOS

**Symptom:** Even with a working amd64 image:
```
docker: image with reference X:latest was found but does not
provide the specified platform (linux/amd64)
```

**Root cause:** Docker Desktop's containerd store treats
single-arch images as not satisfying the platform selector.

**Fix:** Drop `--platform linux/amd64` from `docker run`. The image
is already amd64; Rosetta handles the emulation transparently.
Encrypted-linux's earlier scripts pass `--platform linux/amd64` to
`docker run` because they were written before this rule landed.

## Kernel build

### `make modules_prepare` doesn't populate Module.symvers

**Symptom:** Out-of-tree module build fails with `undefined: seq_printf`.

**Root cause:** `modules_prepare` sets up the build infrastructure
but does NOT populate `Module.symvers` with `EXPORT_SYMBOL` entries.
Without it, out-of-tree modules can't resolve standard kernel
symbols.

**Fix:** Use `make modules` instead. It builds in-tree modules
*and* populates `Module.symvers` fully. Even if you don't intend to
ship the in-tree modules, the build is fast and the side-effect is
required.

### `yes "" | make oldconfig` SIGPIPEs under pipefail

**Symptom:** Build script exits with code 141 immediately after
`scripts/config --enable RANDSTRUCT_FULL`.

**Root cause:** `yes` produces infinite output. When `make oldconfig`
exits, `yes` hits a closed pipe and dies with SIGPIPE (exit 141).
With `set -o pipefail`, that propagates as the pipeline's exit code.

**Fix:** Wrap in `( ... ) || true`, and follow up with `make
olddefconfig` to clean-resolve the config.

```bash
(yes "" | make oldconfig >/dev/null 2>&1) || true
make olddefconfig >/dev/null 2>&1 || true
```

### `CONFIG_KPROBES=n` silently breaks kprobe-using modules

**Symptom:** Module loads, init runs, but `register_kprobe()`
returns -95 (`-EOPNOTSUPP`). No warnings at compile time.

**Root cause:** When `CONFIG_KPROBES=n`, the `register_kprobe()`
declaration in `linux/kprobes.h` becomes a `static inline` stub
that returns `-EOPNOTSUPP`. Modules compile fine (no external
symbol reference); they just never install probes.

**Fix:** Explicitly enable `CONFIG_KPROBES=y` in the kernel config.
The evasive-linux fragment also sets `CONFIG_OPTPROBES=y` and
`CONFIG_KPROBE_EVENTS=y` for kprobe-jump-optimization and
debugfs-visible probes.

### tinyconfig kernel doesn't poweroff cleanly in QEMU

**Symptom:** Demo's `poweroff -f` results in the kernel printing
`reboot: System halted` and QEMU hanging until host-side timeout
(exit 124 in CI).

**Root cause:** `tinyconfig` doesn't include ACPI. Without ACPI,
`kernel_power_off()` falls through to `machine_halt()` which just
spins. With QEMU `-no-reboot`, the halt isn't intercepted as a
shutdown signal.

**Fix:** Add `CONFIG_MAGIC_SYSRQ=y` and trigger emergency reboot
from init:
```sh
echo 1 > /proc/sys/kernel/sysrq 2>/dev/null
echo b > /proc/sysrq-trigger
```
`sysrq b` routes through `machine_emergency_restart()` →
triple-fault, which QEMU `-no-reboot` intercepts as a prompt exit.

Locally this issue is invisible because the visible demo output
ends well before the host-side timeout; CI's strict exit-code
propagation surfaces it.

## printk / kernel logging

### `%px` prints raw hex *without* the `0x` prefix

**Symptom:** Module-parameter ulong parsing fails with `invalid
parameter` when you pass an address captured from a previous
`pr_info("addr=%px")`.

**Root cause:** Kernel `%px` prints 16 raw hex digits with no
prefix. The `0x` is only added with `%#px` (the `#` flag).
`module_param(..., ulong, ...)` calls `kstrtoul(val, 0, ...)` with
`base=0`, which means auto-detect: leading `0x` → hex, leading `0`
→ octal, else decimal. Without `0x`, the parser assumes decimal,
overflows, errors.

**Fix:** When extracting an address from dmesg for use as a module
parameter, prepend `0x`:
```sh
CUR=$(dmesg | grep "new_func at" | tail -1 | awk '{print $NF}')
insmod foo.ko addr=0x${CUR}
```

### `loglevel=3` hides `pr_info` from the console

**Symptom:** Module's `pr_info` doesn't appear on QEMU stdout, even
though `dmesg` (read from the script) shows it.

**Root cause:** `loglevel=3` is `KERN_ERR`; only `KERN_EMERG`,
`KERN_ALERT`, `KERN_CRIT`, `KERN_ERR` get printed to the console.
`pr_info` is `KERN_INFO=6`. The ring buffer keeps everything; the
console filter is separate.

**Fix:** For demo visibility, pass `loglevel=7` (or higher) on the
kernel command line. evasive-linux's `run-qemu.sh` defaults to 7.

## Livepatch specifics

### Kprobe attaches *after* the endbr64 byte

**Observation:** A function whose C pointer is at `0xa0000023`
gets its kprobe armed at `0xa0000027` — 4 bytes higher.

**Why:** On a kernel built with `CONFIG_X86_KERNEL_IBT`, every
function entry begins with `endbr64` (4 bytes). Kprobes won't
attach to the `endbr64` itself; it advances past it. This is why
demo 03's trap log shows:
```
HONEY-TRAP: superseded patch 02 hit at 0xffffffffa0000027
```
when the function pointer was `0xffffffffa0000023`. The offset is
not a bug.

### Atomic-replace transitions take seconds to converge

The unified consistency model converges "usually within a few
seconds" per upstream docs. Sub-second cadence is probably not
achievable with the current consistency model. The user's demo init
script sleeps 3 seconds after disabling and that has been
consistently sufficient.

### Pre-handler return 0 = let original execute

The kprobe `pre_handler` returns int. Returning 0 means: log the
trap, but let the original instruction run. Returning non-zero is
*supposed* to skip the original on some configs but is unreliable
across kernels. Demo 03's attacker crashes inside the stale
function (NULL deref on bad seq_file arg) because we return 0; the
attacker_thread isolation in `attacker.c` keeps init alive.

## CI

### Don't run heavy demos in series in CI

The three evasive-linux demos run in parallel as separate jobs.
Each is ~5-7 min on ubuntu-latest. Running them in series would
push the total over GitHub's free-tier limits unnecessarily.

### Shellcheck SC2086 is intentional

The init script in `scripts/build-continuous-demo.sh` deliberately
relies on word-splitting in `insmod /lib/modules/lp_NN.ko
${PARAM_ARG}` so that empty PARAM_ARG results in no extra
arguments. CI's shellcheck step excludes SC2086.

## arXiv / LaTeX

### `\^{X}` in BibTeX titles → biber NFD decomposition → LaTeX error

**Symptom:** `pdflatex` fails with:
```
! LaTeX Error: Unicode character ̂ (U+0302) not set up for use
```
when compiling with a bib entry containing `\^{X}`.

**Root cause:** biber processes the entry and decomposes `\^{X}`
into `X` plus a combining circumflex (U+0302) in NFD form. The
default LaTeX encoding can't render the combining mark.

**Fix:** Use `\textasciicircum{}` instead, or just omit the
accent. evasive-linux's `paper/refs.bib` does this for the krx
reference.
