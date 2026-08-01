---
description: Update the knowledge file after code changes — re-scan only what changed instead of the whole project, keeping .sme/codebase-knowledge.json current cheaply.
argument-hint: [optional: the area that changed, e.g. "the payment module" or a path]
---

Act as the codebase-sme agent. Update the existing knowledge file to reflect recent changes. Changed area: **$ARGUMENTS** (if empty, ask what changed, or infer from recent git activity if available).

## Steps
1. Read the current `.sme/codebase-knowledge.json`. If it does not exist, tell the user to run `/sme-scan` instead (there's nothing to refresh).
2. Determine what changed — from the argument, or by checking recent git history (`git log`, `git diff`) if the tool is available.
3. Re-analyze only the affected modules/files with the same rigor as `/sme-scan`.
4. Merge the updates into the existing knowledge file: update changed modules, interactions, data model, and risk entries; add new modules; remove entries for deleted code. Preserve unaffected parts as-is.
5. Refresh `open_questions` and `glossary_candidates` for the touched areas.
6. Update the `project.scanned_at` timestamp.

## Output contract
- Write the merged result back to `.sme/codebase-knowledge.json` (same schema — see `shared/knowledge-schema.md`).
- Print a concise changelog: what modules/entries were added, updated, or removed, and any new open questions.
- Remind the user to commit the updated file so the team stays in sync.

This is the cheap, incremental counterpart to `/sme-scan`. Prefer it for routine updates; use a full `/sme-scan` only after large structural changes.
