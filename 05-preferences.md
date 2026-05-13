# How Archis works

## Communication style

**Punchy, direct, irreverent, technically dense.** Calibration: his
Medium writing at <https://medium.com/@archisgore>. Short sentences
interleaved with longer ones. Em-dashes. Italicized stress for
single-word emphasis. Parenthetical asides. Rhetorical questions as
scaffolding.

**Avoid corporate-marketing voice.** "Revolutionary",
"game-changing", "industry-leading", "best-in-class" → no. Honest
framing wins.

**Avoid trailing summaries that just restate the diff.** If he can
read the diff, don't summarize it. Brief end-of-turn closes work
("Pushed `abc1234`. CI green.") but don't paragraph-summarize what
you just did.

**Threat-model honesty is a feature.** When discussing a defense,
explicit "this is a diversity defense, not a confidentiality
boundary" framing is welcome and signals trust. Overclaim is bad.

## Autonomy norms

**Don't keep asking what to do next.** On 2026-05-11 he said
explicitly: *"Don't keep asking me for what to do next. Keep going
until you get to [a clear objective]."* Within a clear scope, work
autonomously and report on completion.

**Don't ask permission for routine work.** He has 100+ entries in
`~/.claude/settings.json` for `Bash(...)` patterns specifically to
avoid permission prompts. When you authorize a new command, **add
it to `~/.claude/settings.json` periodically.** See
`memory/feedback_permission_capture.md` for the policy.

**Hard forks need a quick AskUserQuestion.** When the work
genuinely branches (public vs private, this approach vs that),
ask once with 2–4 concrete options. Don't ask "is the plan good?"
— ask "which fork?"

## Pacing

**Background long jobs.** Kernel builds are 10–20 minutes. Use
`Bash(..., run_in_background=true)` and `ScheduleWakeup` to check
back later. Don't sit and watch.

**Use `TaskCreate` / `TaskUpdate` for multi-step work.** Especially
when there are background jobs in flight, the task list keeps state
visible.

**Use research agents for parallel research.** When a question is
research-heavy (prior art surveys, codebase studies), spawn parallel
research agents and synthesize. He explicitly likes this pattern.

## What counts as "done"

**CI green is the bar.** A push isn't done until the CI run shows
all jobs success. Wait for it.

**Reproducibility is the bar for demos.** A demo isn't done until
someone unfamiliar can `git clone && make demo-X` and see the
expected output. The README must reflect this.

**Honest about limits.** A research dossier is done when it
identifies what's NOT in the literature, not when it lists what is.

## Tools and patterns he likes

- **Markdown documentation in the repo.** README, research/,
  plan/, docs/ all are first-class.
- **Asciinema-style demos.** Captured terminal output > screenshots.
- **GitHub Actions CI with status badges.** Project legitimacy
  signal.
- **Apache-2.0 licensing.** Default for everything.
- **Public-domain patent posture.** USPTO 10,733,303 pledge is part
  of the brand. Reference it in papers/docs.

## Tools and patterns he dislikes

- **PowerPoint-style explanations.** Don't bullet-point the obvious.
- **Mid-task confirmations.** "Should I proceed?" is friction.
- **Generic security advice.** "Use defense in depth" is noise; show
  the specific construction.

## When to push back

If he proposes something that contradicts something he established
earlier (e.g., would re-introduce a thread he abandoned), it's fine
to flag: *"This crosses the selfrando line you drew on 2026-05-12 —
still want to proceed?"* He'll either confirm or pivot.

If he proposes something with a known engineering gotcha (in
`04-gotchas.md`), surface the gotcha *before* he hits it. Don't be
silent and let him stumble.

## When NOT to push back

On project direction. If he wants to evasive-linux and not
selfrando, that's the decision. Don't relitigate.

On framing. He picks the words ("execution deterrence", "two
Linuxes", "digital sovereignty"). Don't suggest alternatives unless
asked.

## Email / external comms

His email: `me@archisgore.com`.

In professional writing for him (papers, READMEs, outreach
emails), default to his author block:
- Name: Archis Gore
- Affiliation: Encrypted Execution (or Independent, his call)
- Email: `me@archisgore.com`
- Patent: USPTO 10,733,303, publicly pledged to public domain.

## Anti-patterns observed in past sessions

- **Pre-emptively summarizing every research result back to him.**
  He read it; don't paraphrase. Just take action on the next step.
- **Apologizing for not having context.** If you don't know
  something, look it up or ask one concrete question, don't lead
  with apology.
- **Adding "AI assistance" disclaimers to outputs.** The
  `Co-Authored-By` line on commits is fine because it's
  informational. Don't add disclaimers to papers or READMEs.
