# Outreach plan

*Strategy for getting encrypted-linux + evasive-linux widely seen
and tried. State as of 2026-05-13: Medium post drafted + LinkedIn
share done; arXiv preprint drafted but not submitted; everything
else not started.*

## Ordering (highest-leverage first)

### 1. arXiv preprint — submit this week

LaTeX source at `evasive-linux/paper/main.tex`. Compiles clean.
12 pages.

**To submit:**
1. Read `paper/main.tex` carefully — §3 (threat model) and §7
   (limitations) especially. Adjust if anything overclaims.
2. Verify §5 (Evaluation) captured output matches current
   `make demo-03` output.
3. Submit at <https://arxiv.org/submit>. Categories: `cs.CR`
   primary, `cs.OS` secondary. Upload tarball of `main.tex` +
   `refs.bib`. License "arXiv perpetual non-exclusive". Comments
   field: "12 pages. Source and reproducible CI at
   github.com/encrypted-execution/evasive-linux".
4. Wait 24h for arXiv processing. Note the assigned URL.

**Why first:** Citable URL with priority date. Every other channel
gets better with this in hand.

### 2. LWN tip — within 24h of arXiv landing

Email `corbet@lwn.net` directly. Subject line suggestion:
*"Linux livepatch as a non-CVE moving-target transport (arXiv
preprint + reproducible demo)"*.

Body (template):
```
Hi Jon,

Drafted a preprint on using CONFIG_LIVEPATCH as the transport for
continuous in-kernel code re-randomization, with kprobe-based
honey-traps at superseded function addresses. Both ideas appear to
be unexplored in the public literature.

  arXiv: <URL>
  Repo + reproducible CI: github.com/encrypted-execution/evasive-linux
  TL;DR: make demo-03

The repo demonstrates the trap firing end-to-end in QEMU. Adelie
(ASPLOS '22) is the closest competitor (modules only, no livepatch,
no honey-traps).

Happy to answer questions. The repo is Apache-2.0; the underlying
patent (USPTO 10,733,303) is publicly pledged to public domain.

Archis Gore
me@archisgore.com
```

**Why second:** Corbet's coverage cascades through the kernel
community for weeks. He knows livepatch well — he covered the
hybrid consistency model when it merged.

### 3. Direct outreach to named individuals — week of arXiv landing

Six concrete recipients. Each gets a short, polite, *individual*
email (not a CC list). Include arXiv URL + repo URL + one-sentence
pitch.

| Person | Affiliation | Why them |
|---|---|---|
| **Josh Poimboeuf** | Red Hat, livepatch consistency-model author | The 4.12 unified consistency model is the foundation of demo 03. He'd be amused to see this use. |
| **Per Larsen** | Immunant, selfrando | MTD veteran. Selfrando is in the related-work section. |
| **Cristiano Giuffrida** | VUSec, Adelie author | Adelie is the closest published prior work. Pre-empting any "they ignored prior art" critique. |
| **Brad Spengler** | grsecurity / PaX | RANDKSTACK is the only continuous-rerand mechanism shipping in production Linux. He'll have strong opinions either way. |
| **Mike Perry** | Tor Project | Tor cares about MTD operationally; selfrando came from this collaboration. |
| **Jiri Kosina / Petr Mladek / Miroslav Benes** | SUSE livepatch maintainers | Domain experts on what's plausible. |

Email template:
```
Subject: evasive-linux — livepatch-driven kernel code re-randomization

Hi <Name>,

You may find this interesting given your work on <specific
relevant thing>. I've written up a preprint on using Linux's
livepatch subsystem as a continuous moving-target transport, with
kprobe-based tripwires at superseded function addresses:

  arXiv: <URL>
  Repo: github.com/encrypted-execution/evasive-linux

Working demo: `make demo-03` runs end-to-end in QEMU, including the
honey-trap firing on a simulated stale-pointer attacker. The trap
construction appears unexplored in the literature.

Would value your thoughts when you have a moment. Apache-2.0;
underlying patent USPTO 10,733,303 publicly pledged to public
domain.

Archis Gore
```

**Why third:** Direct outreach builds credibility with the small set
of people whose opinions move the field. Six well-targeted emails
beat any broadcast.

### 4. Hacker News + Reddit — Tuesday or Wednesday morning Pacific

**Title:** *"Two Linuxes Should Be As Different As Mac and Windows"*
(or the Medium post's actual title if different).

**URL:** Medium blog post (primary). arXiv URL and repo URL in the
*first comment*.

**Timing:** Tuesday or Wednesday, 8–10am Pacific. Avoid weekends
(low engagement) and Mondays (high competition).

**Cross-post to:** `/r/netsec`, `/r/linux`. Stagger by 2–4 hours;
don't post all three at the same minute.

### 5. Linux Plumbers Microconference proposal — check next CFP

Linux Plumbers Conference has microconferences each year. The
"Live Patching" or "Kernel Hardening" MCs are the natural fits.
Submit a 10-minute lightning talk: "Kernel livepatch as a
moving-target defense transport — what works, what doesn't, what's
left to do."

LPC is where the right ~200 kernel people are in one room. A
lightning talk converts better than a 45-min generic conference
talk.

### 6. Black Hat / DEF CON / OffensiveCon / HITB

The "two Linuxes" framing translates well to a Black Hat briefing
(practitioner audience, attack-economics framing, live demo).

Open CFPs to track:
- **HITB Amsterdam** — CFP usually closes ~Feb
- **Black Hat USA** — CFP closes ~Feb
- **OffensiveCon** (Dec) — smaller but high signal
- **DEF CON** — CFP closes ~May

Submit for the next open one. The live-demo angle (run `make
demo-03` on stage, watch the trap fire) is the differentiator.

### 7. SREcon / KubeCon — operator audience

Frame as *"execution deterrence for sovereign cloud"* — the Medium
post's pitch translated for operators. Less obvious but
high-leverage for *adoption* (these people have budget to actually
run this).

## What to NOT do

- **Don't dilute by posting in 12 places at once.** Let each
  channel cascade naturally before the next push. Roughly: arXiv
  + LWN week one; direct outreach + HN week two; conference
  submissions month two.
- **Don't write a vision/manifesto paper.** The system paper is
  the right shape. The Medium post is the vision artifact; let
  that carry the digital-sovereignty framing.
- **Don't oversell.** Reviewers and the security community are
  allergic to "we cured X" framings. The honesty in §7 of the
  paper is the credibility move.

## Existing artifacts

- Medium blog post — drafted previously, posted by Archis.
- LinkedIn share — done by Archis.
- arXiv LaTeX preprint — drafted, not submitted.
- Reproducible CI — green on every push.

## Things that would amplify (low effort, high payoff)

- **Asciinema cast of `make demo-03` ending with the trap line.**
  60-second visual artifact for social shares.
- **Pre-built QEMU image as a GitHub release.** Lowers the "try it"
  friction from 15-minute build to 30-second boot.
- **Public bypass challenge.** "First person to bypass the
  honey-trap on the canonical demo gets [credit / co-author
  credit]." Engages the offensive community, gives the work
  attacker-side credibility.

## Public bypass challenge — draft framing

If/when this gets posted:
```
Challenge: bypass the evasive-linux honey-trap.

Setup: `make demo-03` (15 min build, runs end-to-end in QEMU).
After all 5 generations have loaded, lp_02 has armed a kprobe at
lp_01's superseded cmdline_proc_show. Any call to that address
fires the trap and logs the caller.

Find a kernel-context construction that calls lp_01's old address
without tripping the kprobe. Co-author credit on a follow-up paper
for any non-trivial bypass.

Out of scope: full root with /dev/kvm / /dev/mem access. We
acknowledge in SECURITY.md that those defeat this defense.
```
