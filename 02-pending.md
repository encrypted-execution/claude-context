# Pending items + immediate next-actions

*This file goes stale fastest. Update after every substantial session.*

*Snapshot: 2026-05-13.*

## Active threads

### arXiv preprint submission

A 12-page LaTeX preprint is drafted at
`evasive-linux/paper/main.tex` and compiles clean. It has **not been
submitted to arXiv** yet.

**Suggested order of operations:**

1. Archis reads `paper/main.tex`, especially §3 (threat model) and
   §7 (limitations). Anything overclaimed? Fix.
2. Verify captured demo output in §5 (Evaluation) still matches
   what `make demo-03` produces on current `main`.
3. Submit at <https://arxiv.org/submit>. Categories: `cs.CR`
   primary, `cs.OS` secondary. Upload tarball of `main.tex` +
   `refs.bib`. License "arXiv perpetual non-exclusive".
4. After arXiv gives a URL, email LWN's Jonathan Corbet
   (`corbet@lwn.net`) with arXiv + repo links.
5. Tuesday-Wednesday 8–10am Pacific: HN post linking the Medium
   blog, with arXiv URL in the first comment.

See `06-outreach-plan.md` for the full sequence.

### Outreach (mostly not started)

- [x] Medium blog post drafted (in this session).
- [x] LinkedIn share (per Archis).
- [ ] arXiv submission.
- [ ] LWN tip to Jonathan Corbet.
- [ ] HN post timing.
- [ ] Direct outreach: Josh Poimboeuf, Per Larsen, Cristiano
      Giuffrida, Brad Spengler, Mike Perry. See
      `06-outreach-plan.md` for context on each.
- [ ] Linux Plumbers Microconference proposal (check next CFP).
- [ ] Black Hat / DEF CON / OffensiveCon / HITB submission.

### encrypted-linux backlog

- **Seed hardening** (`plan/06-seed-hardening.md` in the
  encrypted-linux repo). Defer until Archis asks or there's no
  other obvious thread; see
  `memory/feedback_seed_hardening_backlog.md`.

## Recently completed (don't redo)

- v3 demos for encrypted-linux: errno + ELF OSABI + /proc rename.
  Shipped 2026-05-12.
- Documentation update across encrypted-linux to reflect v3.
- evasive-linux demos 01/02/03 + 03b. Shipped 2026-05-13.
- evasive-linux CI workflow + Makefile + CONTRIBUTING/SECURITY/
  .editorconfig. Shipped 2026-05-13.
- arXiv preprint LaTeX source + bibliography. Drafted 2026-05-13.
- This claude-context repo. Drafted 2026-05-13.

## Things explicitly *not* in progress

- **selfrando integration.** Archis explicitly abandoned this
  2026-05-12 ("Abandon the selfrando idea and scrub it from your
  memory and context and from any of our docs"). Don't suggest it.
- **continuous-rescramble branch on encrypted-linux.** Created but
  unused; Archis pivoted to evasive-linux as a separate repo.
- **`<<autonomous-loop>>` self-driving cycles.** If you see this
  sentinel in a prompt, it's the harness re-invoking; not a task.

## Open questions / future work

See `07-future-work.md` for the backlog of ideas worth pursuing but
not currently scheduled.

## Suggested kickoff phrasings

If Archis opens a session without context, useful first-message
responses:

- *"State as of last session: encrypted-linux v3 shipped, evasive-linux
  demos working, arXiv preprint drafted but not submitted. What do
  you want to work on?"*
- *"Want to pick up arXiv submission, run outreach, or extend
  evasive-linux (self-driving loop / INT3-carpet variant /
  synthetic-gadget bait)?"*

Don't bury the lead in pleasantries. He likes a punchy state-line.
