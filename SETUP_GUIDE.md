# Setup & Distribution Guide

## Prerequisites
- Each user needs Claude Code with an account/plan that includes it (individual Pro/Max, Team Premium seat, or Enterprise). Verify current plan details at https://support.claude.com — plans change.
- Git, for sharing the knowledge file across the team.
- **Node.js** (for rendering diagrams to PNG/JPG via Mermaid CLI). Most machines with Claude Code already have this. The plugin runs Mermaid CLI on demand via `npx`, so no separate install step is required — the first diagram render may take a little longer as `npx` fetches it.

### If diagram rendering fails on first use
Mermaid CLI needs a headless Chrome to render images. If you see an error like
`Could not find Chrome`, run this once:
```bash
npx -y puppeteer browsers install chrome
```
Then re-run the command that produces the diagram. This is a one-time, per-machine setup step — it does not need to be repeated by every teammate if they hit it once each, but it's worth mentioning in your team rollout notes so nobody is surprised by it.

## A. First-time project setup (done once, by one person)

1. **Install the plugin**
   ```bash
   cd your-project
   claude
   /plugin marketplace add <your-org>/<this-repo>
   /plugin install codebase-sme@our-team-marketplace
   ```

2. **Scan the codebase** — the one deliberately thorough step.
   ```bash
   /sme-scan
   ```
   Produces `.sme/codebase-knowledge.json`. Review the printed summary and the open-questions list.

3. **Add human context**
   ```bash
   cp CLAUDE.md.template CLAUDE.md
   # fill in history, intent, tribal knowledge, glossary
   ```

4. **Commit both**
   ```bash
   git add .sme/codebase-knowledge.json CLAUDE.md
   git commit -m "Add codebase SME knowledge base"
   git push
   ```

## B. Each teammate (2 commands, once)
```bash
cd your-project
claude
/plugin marketplace add <your-org>/<this-repo>
/plugin install codebase-sme@our-team-marketplace
```
They pull the repo, so the knowledge file and CLAUDE.md are already there. Nothing else to configure.

## C. Keeping it current
- After routine changes: `/sme-refresh [what changed]` and commit.
- After large structural changes: `/sme-scan` again and commit.

## Distribution options
- **Internal Git repo (recommended):** push this folder; teammates `add` the marketplace by repo path; updates flow via `git pull` + `/plugin update`.
- **Zip (demo only):** share the archive; teammates `add` the marketplace by local path. No auto-updates.

## Cost & efficiency notes
- `/sme-scan` is the expensive call — it reads broadly. Run it once; refresh incrementally after that.
- Every other command reads the compact knowledge file first and then opens only the few source files it needs, instead of re-exploring the whole codebase. That's what keeps day-to-day usage cheap.
- For hard usage caps: prefer `/sme-refresh` over `/sme-scan`, keep the knowledge file committed so no one re-scans unnecessarily, and encourage specific questions (a scoped `/sme-explain checkout` costs far less than an open-ended exploration).
- Exact per-query cost depends on model, codebase size, and plan. Treat any specific dollar figure as something to measure on your own project rather than assume.

## A note on client code / confidentiality
If this is a client's proprietary codebase, confirm what your agreement allows before running it through any AI tool, and check where your organization's Claude plan sits on data handling. The agent is instructed never to emit secrets or connection strings into the knowledge file, but the surrounding policy decision is yours to confirm.
