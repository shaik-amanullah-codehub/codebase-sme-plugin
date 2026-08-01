---
description: Assess the impact and risk of a proposed change before writing code — what it touches, what could break, what to test, and how risky it is.
argument-hint: [the proposed change, e.g. "change Order.Status from string to enum" or "add a nullable DiscountId to Order"]
---

Act as the codebase-sme agent doing a pre-change risk review, the way a senior engineer would in design review. Proposed change: **$ARGUMENTS**

## Steps
1. Read `.sme/codebase-knowledge.json` (if missing, tell the user to run `/sme-scan`).
2. Identify the components the change centers on, then trace outward (reuse the same rigor as `/sme-trace`) to find everything affected.

## Output contract

**Change summary** — restate the proposed change in one or two sentences, and note your interpretation if it was ambiguous.

**Directly affected** — the components that must change, grounded in code (file + reason).

**Indirectly affected (ripple)** — components that don't change but are affected by the change (callers, serializers, DB schema, API contracts, cached shapes, downstream consumers). Mark confirmed vs likely.

**Breaking-change analysis** — will this break: API contracts? database schema (migration needed)? serialized/persisted data? public interfaces other modules rely on? Explicitly yes/no each, with the evidence.

**Test impact** — which existing tests likely need updating, and which new tests should be added. Note if the affected area currently has no tests (a risk in itself).

**Risk rating** — Low / Medium / High, with concrete reasons. Factor in blast radius, presence of tests, and how fragile the touched modules are (from the knowledge file's `risk` data).

**Recommended sequencing** — the safe order to make the change (e.g. add-then-migrate-then-remove for a rename), plus any feature-flag or backward-compat suggestion if warranted.

**Open questions** — anything the person should confirm before proceeding. Be honest about what your analysis could not see (same caveats as `/sme-trace`: DI, reflection, events, unscanned code).
