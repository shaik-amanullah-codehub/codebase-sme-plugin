# Shared: Rendering Mermaid diagrams to PNG/JPG

Any command that produces a diagram must render it to an actual image file, not just
print Mermaid code. Follow this exact procedure after you have valid Mermaid syntax.

## Procedure

1. **Ensure the output folder exists:**
   ```bash
   mkdir -p .sme/diagrams
   ```

2. **Write the Mermaid source** to `.sme/diagrams/<slug>.mmd`, where `<slug>` is a short
   kebab-case name describing the diagram (e.g. `architecture-overview`,
   `checkout-workflow`, `paymentservice-charge-trace`). Reuse the same slug for reruns
   so the diagram updates in place rather than accumulating stale files.

3. **Render it to a high-resolution PNG** using the plugin's shared theme:
   ```bash
   npx -y @mermaid-js/mermaid-cli -i .sme/diagrams/<slug>.mmd \
     -o .sme/diagrams/<slug>.png \
     -c "${CLAUDE_PLUGIN_ROOT}/shared/mermaid-theme.json" \
     -b white -w 1600 -H 1000 --scale 2
   ```
   - `-b white` gives a clean white background (looks right pasted into a doc or ticket).
   - `-w`/`-H` set a base canvas; `--scale 2` renders at 2x for crisp text — this is what
     makes the difference between a "screenshot-quality" diagram and a professional one.
   - For a JPG instead of PNG, change the `-o` extension to `.jpg`.
   - For very wide/tall diagrams, increase `-w`/`-H` rather than shrinking `--scale`.

4. **If the render fails with a Chrome/Puppeteer error** (common on a fresh machine,
   e.g. `Could not find Chrome`), run this once, then retry step 3:
   ```bash
   npx -y puppeteer browsers install chrome
   ```
   If it still fails in a sandboxed/CI environment, add `--puppeteer-config` pointing to
   a small JSON file with `{"args": ["--no-sandbox"]}` and pass the resolved Chrome path
   via `executablePath` if auto-detection fails.

5. **Confirm the file was written** (`ls -la .sme/diagrams/<slug>.png`) before telling the
   user it's ready — do not claim success without checking.

6. **Present the PNG as the primary artifact.** Keep the `.mmd` source alongside it (useful
   for manual edits or regenerating with a different tool), but the PNG is what the user
   actually wants to view, paste into a doc, or attach to a Jira ticket.

## Style rules for the diagram itself, before rendering

- Group related nodes in subgraphs by layer/domain — this is what makes a diagram look
  architected rather than dumped.
- Keep labels short; put detail in the accompanying narrative text, not on the diagram.
- Use consistent shapes with meaning: rectangles for services/components, cylinders
  `[( )]` for datastores, hexagons `{{ }}` for external systems.
- Use dashed edges (`-.->`) specifically for relationships marked `inferred` rather than
  `confirmed` in the knowledge file, and say so in a one-line legend under the diagram.
- Don't cram more than ~15-20 nodes into one diagram — split into multiple focused
  diagrams (e.g. one per subsystem) if the real picture is bigger, and say so.
