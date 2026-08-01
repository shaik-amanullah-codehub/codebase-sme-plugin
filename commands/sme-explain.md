---
description: Explain how an existing feature, flow, module, or endpoint works today — end to end, grounded in the real code.
argument-hint: [feature / module / endpoint, e.g. "checkout" or "POST /api/orders"]
---

Act as the codebase-sme agent. Explain how the following works in this codebase: **$ARGUMENTS**

## Steps
1. Read `.sme/codebase-knowledge.json`. If missing, tell the user to run `/sme-scan` first.
2. From the knowledge file, locate the relevant module(s) and the specific `key_files` involved. Open only those files (plus 1–2 more if genuinely needed).
3. Trace the flow from entry point through to data/external boundaries.

## Output contract
Produce, in this order:

**Summary** — 2–4 sentences in plain English. What is this, and what does it accomplish for the user/business?

**Entry point** — where it starts (endpoint/job/handler), with the real file and method name.

**Step-by-step flow** — the runtime path, each step naming the real class/method and what it does. Show the hops: entry → service(s) → data access / external calls → response. Keep each step to one or two lines.

**Data touched** — tables/entities read or written, and the access path.

**External dependencies** — any third-party services, queues, or APIs involved.

**Gotchas & risks** — anything a person should know before changing this: fragile spots, missing tests, surprising coupling, side effects. Be honest; if it's clean, say so.

**Verification note** — one line stating what you actually read vs. what you inferred, so the reader knows how much to trust the trace. If you could not confirm a step, say which one.
