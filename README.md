# Codebase SME Plugin

A senior-architect-level Subject Matter Expert for your codebase, delivered as a Claude Code plugin. It scans the project once into a compact knowledge file, then answers questions and produces documentation-grade artifacts grounded in the real code.

Language-agnostic (works for .NET, Java, Node, Python, etc.) — the scan adapts to the detected stack.

## What it can do

| Command | Purpose |
|---|---|
| `/sme-scan` | **Run once first.** Deep-scans the codebase into `.sme/codebase-knowledge.json`. |
| `/sme-refresh` | Cheap incremental update after code changes. |
| `/sme-explain` | Explain how a feature/flow/module works today, end to end. |
| `/sme-user-story` | Industry-standard Jira user story (epic, Gherkin AC, estimate, impacted components). |
| `/sme-architecture` | System/module architecture diagram (rendered as a PNG) + narrative. |
| `/sme-workflow` | Runtime workflow of one operation as a sequence diagram (PNG). |
| `/sme-trace` | Function call-traceability map (callers + callees) as a PNG, for impact analysis. |
| `/sme-impact` | Pre-change risk assessment: what breaks, what to test, how risky. |
| `/sme-onboard` | Full onboarding report for a newcomer. |

## The two files that make it accurate

1. `.sme/codebase-knowledge.json` — machine-derived map, produced by `/sme-scan`. Commit it.
2. `CLAUDE.md` — human-authored context (history, intent, tribal knowledge). Copy from `CLAUDE.md.template`. Commit it.

The scan gives structure; the briefing gives intent. Together they let the SME reason like someone who's been on the project for years.

## Diagrams are real image files

Every diagram command (`/sme-architecture`, `/sme-workflow`, `/sme-trace`, and the diagram
inside `/sme-onboard`) renders an actual PNG to `.sme/diagrams/`, using a shared professional
theme (`shared/mermaid-theme.json`) — not just Mermaid code you'd have to render yourself.
The `.mmd` source is kept alongside each PNG so you can hand-edit or re-render if needed.
These are convenient to drop straight into a Confluence page, a Jira ticket, or a slide.

## Design principle: honest grounding

This agent is built to distinguish **confirmed facts** (read from code) from **inferences** (deduced). Diagrams mark unverified edges as dashed; traces state what could hide additional callers; the knowledge file carries `confidence` fields. A tool that produces a confident-looking but wrong diagram is dangerous — this one is built to say "I'm not sure" where that's the truth.

## Quick start

```bash
cd your-project
claude

# install (once per person)
/plugin marketplace add <your-org>/<this-repo>
/plugin install codebase-sme@our-team-marketplace

# set up (once per project)
/sme-scan
cp CLAUDE.md.template CLAUDE.md   # then fill it in
git add .sme/codebase-knowledge.json CLAUDE.md
git commit -m "Add codebase SME knowledge base"

# use (anyone, anytime)
/sme-explain checkout
/sme-user-story let customers apply a discount code at checkout
/sme-architecture
/sme-trace PaymentService.Charge
```

See `SETUP_GUIDE.md` for the full walkthrough and cost notes.
