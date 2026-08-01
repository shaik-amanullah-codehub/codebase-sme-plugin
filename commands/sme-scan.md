---
description: One-time (per project) deep scan of the codebase into .sme/codebase-knowledge.json. Every other SME command reads this file, so run this first. Re-run only after major structural changes.
argument-hint: [optional: a subfolder to limit the scan to, e.g. src/Ordering]
---

Act as the codebase-sme agent. Your job is to build the project's knowledge file with the rigor of a senior architect doing a proper codebase audit. Accuracy matters more than speed here — this file is the foundation every later query depends on.

**Scope:** $ARGUMENTS (if empty, scan the whole project from the current root)

## Process (follow in order)

**1. Orient before reading everything.**
- Identify the tech stack from project/manifest files (`*.csproj`, `*.sln`, `package.json`, `pom.xml`, `requirements.txt`, etc.).
- Identify the top-level layout: where source lives, where tests live, what to ignore (generated code, `bin/`, `obj/`, `node_modules/`, vendored libs, DB migrations unless relevant).
- Form an initial hypothesis of the module boundaries from the folder/namespace structure.

**2. Map modules systematically.**
- For each logical module, identify its key files and their roles, its entry points (HTTP endpoints, jobs, CLI, event handlers), its public surface, and which other modules it depends on.
- Determine database access per module (tables/entities, operations, access path).
- Identify external dependencies (payment, email, queues, caches, third-party APIs).
- Note design patterns actually present (not aspirational ones).

**3. Trace the important interactions.**
- Record module-to-module interactions with the concrete method path that proves the link (e.g. `OrderService.Checkout -> PaymentService.Charge`).
- Mark each as `confirmed` (you saw the call) or `inferred` (you deduced it).

**4. Capture cross-cutting concerns.**
- Auth, logging, error handling, configuration source, validation approach.
- If you find secrets/connection strings, record only *that they exist and where* — never the values.

**5. Assess risk honestly.**
- Per module: tests present? high coupling? raw SQL? apparent age/neglect? Rate low/medium/high with concrete reasons.

**6. Record what you could not determine.**
- Populate `open_questions` with genuine ambiguities. This array must not be empty — flag anything you had to guess.
- Populate `glossary_candidates` with domain terms whose meaning you inferred, so a human can confirm them.

## Output contract

- Write the result to `.sme/codebase-knowledge.json` (create the `.sme/` directory if needed).
- Follow the schema exactly as defined in the plugin's `shared/knowledge-schema.md`. Read that file first if you have not.
- Set every `confidence` field honestly. Default to `inferred` unless the code directly proves the fact.
- After writing, print a short human-readable summary to the terminal:
  - Stack detected
  - Number of modules found
  - Highest-risk modules and why
  - The top 3–5 open questions the team should answer
- End with: "Knowledge file written to .sme/codebase-knowledge.json. Commit it to git so the whole team shares one accurate map. Re-run /sme-scan after major structural changes."

## Cost/quality guidance

This scan is the one deliberately expensive operation. Do it thoroughly once. Explore in a structured order (stack → layout → module by module) rather than reading files at random, so you cover the codebase without re-reading. For a very large codebase, it is acceptable to summarize a module from its key files and public surface rather than reading every private helper — but never invent what you did not read, and lower the `confidence` accordingly.
