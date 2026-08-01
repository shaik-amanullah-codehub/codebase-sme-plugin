---
description: Build a function/method call-traceability map for a given class, method, or endpoint — who calls it (callers/upstream) and what it calls (callees/downstream) — for precise impact analysis before a change.
argument-hint: [the symbol to trace, e.g. "PaymentService.Charge" or "OrderRepository"]
---

Act as the codebase-sme agent. Build a call-traceability map centered on: **$ARGUMENTS**

This is an impact-analysis tool. Its value depends entirely on completeness and honesty about what was and wasn't verified. A missed caller can cause a production incident, so be explicit about the limits of the trace.

## Steps
1. Read `.sme/codebase-knowledge.json` (if missing, tell the user to run `/sme-scan`).
2. Locate the target symbol's definition. Read it.
3. **Downstream (callees):** trace what the target calls — services, repositories, external clients, DB access — to a sensible depth (2–3 levels, or until you hit a boundary like the DB or an external API).
4. **Upstream (callers):** search the codebase for everything that calls the target. Be thorough — check controllers, other services, jobs, tests, and any DI registration or reflection/dynamic invocation that might hide a caller.

## Output contract

**Target** — the symbol, its file, and a one-line description of what it does.

**Upstream — who calls this (callers):**
A tree or table, deepest-first, of what invokes the target, with file locations:
```
POST /api/orders (OrderController.SubmitOrder)
  └─ OrderService.Checkout
       └─ PaymentService.Charge   ← target
```

**Downstream — what this calls (callees):**
```
PaymentService.Charge  (target)
  ├─ StripeClient.CreateCharge        [external: Stripe]
  ├─ PaymentRepository.Save           [db: Payments]
  └─ AuditLogger.Record               [cross-cutting: logging]
```

**Combined map (render to PNG):**
Draft as Mermaid `flowchart LR` with the target node visually distinguished (e.g. a
different shape/label). Upstream on the left, downstream on the right. Dashed edges for
any call you inferred rather than confirmed. Then follow the rendering procedure in
`${CLAUDE_PLUGIN_ROOT}/shared/diagram-rendering.md` to produce a PNG at
`.sme/diagrams/<symbol-slug>-trace.png`. The image is the deliverable.

**Impact summary:**
- **Blast radius:** how many distinct callers/entry points ultimately reach this. High fan-in = risky to change.
- **Data & external effects:** which tables and external systems a change here could affect.
- **Test exposure:** which tests (if any) cover paths through the target — note if none were found.

**⚠️ Trace completeness caveat:**
State plainly how confident the caller list is. Static tracing can miss: dependency-injection-resolved calls, reflection, dynamic dispatch, event/message handlers, and calls from code not scanned. List the specific mechanisms in *this* codebase that could hide additional callers, so the reader knows where to double-check manually. Never present the caller list as guaranteed complete.

**Deliverable:** `.sme/diagrams/<symbol-slug>-trace.png` (plus the `.mmd` source alongside it).
