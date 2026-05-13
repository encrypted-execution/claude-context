---
name: encrypted-linux-seed-hardening-backlog
description: User wants cryptographic concealment of the seed when other work is done; see plan/06
metadata: 
  node_type: memory
  type: feedback
  originSessionId: e2774676-db4c-4930-b1f8-58c186399a05
---

User request 2026-05-12:
> "Find ways to make the seed undiscoverable. It's not a hard requirement, but the more cryptographic security we add, the better. We'll address this once other things are done."

**Why:** The PoC ships with a plaintext seed and treats per-system uniqueness as the defense. The user wants to layer cryptographic concealment on top — defense in depth.

**How to apply:** When the encrypted-linux project hits a natural pause (or the user explicitly asks for hardening), open `plan/06-seed-hardening.md` and pick the next-cheapest option not yet implemented. The plan ranks five options by cost. Option 1 (per-binary diversification, ~1 day) is the recommended starting point.

**Important:** Don't pursue this proactively. The user explicitly said "once other things are done." Pursue ONLY if either (a) they ask, or (b) we reach a clear "all other work items closed" state with no other obvious next step.
