---
description: Generate an industry-standard, Jira-ready user story (with epic, Gherkin acceptance criteria, estimate, and code-grounded impacted components) for a new feature or change.
argument-hint: [what you want to build, e.g. "let customers apply a discount code at checkout"]
---

Act as the codebase-sme agent working alongside a product owner. Produce a Jira-ready user story for: **$ARGUMENTS**

## Steps
1. Read `.sme/codebase-knowledge.json` (if missing, tell the user to run `/sme-scan`).
2. Explore the code around the affected area so the "Impacted components" and "Technical notes" are grounded in reality, not generic.
3. Produce the story using the exact structure below. This is the format a mature engineering org would accept into a backlog.

## Output contract (use these exact section headings)

**Epic:** the larger initiative this belongs to (one line).

**Title:** a concise, action-oriented story title (imperative mood).

**User Story:**
> As a `<role>`, I want `<capability>`, so that `<benefit>`.

**Description:**
2–4 sentences of context: the current behavior, why the change is needed, and the desired outcome. Reference how the relevant part works today (grounded in the code you read).

**Acceptance Criteria (Gherkin):**
Provide 3–6 scenarios in Given/When/Then form. Cover the happy path, at least one edge case, and at least one failure/validation case.
```gherkin
Scenario: <name>
  Given <precondition>
  When <action>
  Then <expected outcome>
```

**Impacted Components:**
A table grounded in the actual code, from the knowledge file and files you read:
| Component | File | Change type | Notes |
|---|---|---|---|
| e.g. CheckoutController | Controllers/CheckoutController.cs | modify | add discount param to request |

Include likely-affected: controllers/endpoints, services, repositories/data access, models/DTOs, database tables (note if a migration is implied), and any external integrations.

**Technical Notes / Approach:**
A short senior-level implementation sketch: where the logic should live to match existing conventions, what to reuse, what to avoid. Call out anything that conflicts with current patterns.

**Dependencies / Blockers:**
Anything that must happen first (schema change, config, another story). If none found, say so.

**Risks & Open Questions:**
Honest list. Include product questions (ambiguous requirements) and technical risks (fragile modules this touches, missing tests). Anything you assumed goes here explicitly.

**Definition of Done:**
A short checklist (code + tests + docs + acceptance criteria met + reviewed).

**Estimate (relative):**
Give a story-point estimate on the Fibonacci scale (1,2,3,5,8,13) with one sentence of justification. State clearly that this is a rough, code-informed estimate for the team to calibrate, not a commitment.

## Rules
- Ground "Impacted Components" and "Technical Notes" in code you actually examined. If you're inferring an impact, mark it "(likely)".
- If the request is ambiguous, still produce the story, but make the ambiguity explicit under Open Questions rather than silently picking an interpretation.
