---
description: Generate a comprehensive onboarding report for a newcomer — a guided tour of the whole system, how to run it, where things live, and what to be careful about.
argument-hint: [optional audience, e.g. "backend developer" or "QA tester"]
---

Act as the codebase-sme agent writing the onboarding document you wish you'd had on day one. Tailor the emphasis to the audience if given: **$ARGUMENTS** (default: general developer).

## Steps
1. Read `.sme/codebase-knowledge.json` (if missing, tell the user to run `/sme-scan`).
2. Read the project's `CLAUDE.md` if present, plus any README, to incorporate human-authored context.

## Output contract
Produce a well-structured report (this one is meant to be saved and shared). Use these sections:

**1. What this system does** — the business purpose, main users, and the value it delivers, in plain language.

**2. The 5-minute mental model** — the smallest set of concepts someone must hold in their head to reason about this codebase. Name the core modules and how they relate.

**3. Architecture overview** — a compact diagram of the main modules/layers (reuse the architecture command's logic and follow `${CLAUDE_PLUGIN_ROOT}/shared/diagram-rendering.md` to render it as a PNG at `.sme/diagrams/onboarding-architecture.png`), plus a short narrative. Embed/reference the PNG rather than raw Mermaid code.

**4. Where things live** — a map from "I want to work on X" to "look in folder/file Y". Cover the common tasks for the audience.

**5. How a request flows** — one representative end-to-end workflow (pick the most central one), briefly, so the newcomer sees the whole path once.

**6. Data model orientation** — the handful of core tables/entities and how they relate.

**7. Conventions to follow** — naming, layering, DI, testing — from the knowledge file's `conventions`.

**8. Here be dragons** — the high-risk / fragile / poorly-tested areas to approach carefully, from the `risk` data. Be candid; this is the most valuable section.

**9. Glossary** — domain terms and what they mean (from `glossary_candidates`, clearly marking any that are inferred and need confirmation).

**10. Open questions for the team** — from the knowledge file's `open_questions`, so the newcomer knows what's genuinely unclear (vs. what they're just missing).

Write it so a competent engineer new to *this* project — but not new to software — could become productive quickly. Prefer clarity and honesty over completeness for its own sake.
