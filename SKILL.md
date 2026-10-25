---
name: module-diagrams
description: Generate source-grounded, Greptile-style architecture and flow diagrams for local code modules. Use when asked to visualize or understand a module's components, dependencies, call paths, data flow, or runtime behavior. Produces local Mermaid source and validated SVG renderings; does not post to GitHub.
---

# Module Diagrams

Generate useful, accurate diagrams of an existing codebase for local viewing and documentation. The goal is to help a developer understand how a module actually works—not merely to visualize its folder structure.

## Default behavior

- Work locally in the current repository.
- Treat the requested module, directory, class, feature, or execution path as the scope. If the user names no scope, choose one clearly bounded module from the current context. Record the selected repository root, module boundary, and excluded dependencies in the evidence file and final response.
- Read source code before drawing conclusions. Trace important symbols to their definitions and call sites; inspect tests, configuration, schemas, and documentation when they establish behavior.
- Create diagrams and supporting evidence under `docs/diagrams/` by default. If the repository already has an established documentation/diagram location, use it instead.
- Do not edit application code, build configuration, dependencies, or package manifests. Do not commit, push, open a PR, or post anything to GitHub. This skill's default output is local files only.
- Do not invent components, relationships, runtime behavior, APIs, or data flow. Label uncertainty explicitly.
- Use source identifiers and short summaries in labels. Do not copy secret values, tokens, credentials, private keys, connection strings, or sensitive payloads into diagrams or evidence; cite the configuration key and line instead.

## Workflow

### 1. Establish the module boundary

1. Inspect repository instructions and the relevant package/module's entry points.
2. Identify the main responsibilities, externally visible interfaces, and significant collaborating components.
3. Trace important relationships to actual code: function or method calls, event handlers, dependency injection, registration, persistence, network boundaries, and configuration-driven wiring as applicable.
4. Inspect tests for intended behavior, but distinguish tested behavior from what production code demonstrably does.
5. Keep the scope focused. Do not scan or diagram the entire repository when a small module is requested, except where following a dependency requires it.

### 2. Choose the right diagram(s)

Create the smallest set that clearly explains the module. Common choices:

- **Architecture/component diagram**: major components, responsibilities, dependencies, and subsystem boundaries.
- **Sequence diagram**: one meaningful runtime path, with participants and call order supported by the implementation.
- **Data-flow diagram**: where important data originates, is transformed, and is persisted or returned.
- **Class diagram**: only when class/interface relationships are important to understanding the design.
- **ER diagram**: only when actual schema/model relationships are present and relevant.

Prefer one readable overview plus a focused flow diagram over one oversized diagram. Usually target roughly 8–25 nodes per diagram. Split diagrams if the result becomes crowded. Avoid diagrams that merely reproduce imports or directory structure without explaining behavior.

### 3. Write Mermaid source and evidence

Create files with stable, descriptive names, for example:

- `docs/diagrams/<module>-architecture.mmd`
- `docs/diagrams/<module>-flow.mmd` (only if a distinct runtime flow adds value)
- `docs/diagrams/<module>-evidence.md`

Use Mermaid syntax compatible with Mermaid CLI and common Markdown renderers. Give the diagram a clear title. Use concise labels and meaningful subgraphs. Label edges when that clarifies the relationship. Make dependency direction and runtime call/data-flow direction unambiguous; do not mix them without saying which relationship is shown.

For every diagram node and edge, record a row in the evidence file with: element or relationship, repository-relative source path, line number when available, evidence type (`direct`, `event/configuration`, `inferred`, or `unverified`), and a short explanation. Explain what the diagram shows, how its boundaries were chosen, and any unresolved relationships. Cite definitions and call sites rather than relying on filenames alone.

### 4. Check accuracy and readability

Before finishing:

- Verify that names and edges match the code inspected.
- Verify that sequence order follows the actual call path, not an assumed ideal flow.
- Distinguish direct calls from indirect/event/configuration-driven relationships.
- Remove speculative or low-value nodes and edges. If a relationship is uncertain, omit it or mark it as uncertain in the evidence notes.
- Check for duplicate nodes, Mermaid-safe identifiers, ambiguous arrows, unreadable labels, syntax errors, and unnecessarily long edge crossings.
- Validate Mermaid syntax with a successful `mmdc` render or the pinned one-off CLI command below. A requested diagram is incomplete until its SVG exists and passes the output checks.
- Do not claim the diagram is exhaustive unless you actually checked all relevant paths.

### 5. Render required SVG output

Render a validated SVG alongside each `.mmd` file. Use a known Mermaid CLI version so results are reproducible. The current tested fallback is `@mermaid-js/mermaid-cli@12.0.0`; update that version only after retesting the workflow.

First run a renderer preflight. Use the installed CLI when available; otherwise use the pinned one-off command without changing the repository:

```bash
render_mmd() {
  if command -v mmdc >/dev/null 2>&1; then
    mmdc "$@"
  else
    npx --yes --package @mermaid-js/mermaid-cli@12.0.0 mmdc "$@"
  fi
}

render_mmd --version
```

Before rendering project files, render a small temporary smoke-test diagram. This catches missing Chromium, missing system libraries, and sandbox restrictions before any project output is touched:

```bash
smoke_dir=$(mktemp -d)
cat > "$smoke_dir/smoke.mmd" <<'EOF'
flowchart LR
  A[Source] --> B[Rendered]
EOF

render_mmd -i "$smoke_dir/smoke.mmd" -o "$smoke_dir/smoke.svg"

test -s "$smoke_dir/smoke.svg" && grep -q '<svg' "$smoke_dir/smoke.svg"
```

If the smoke test fails with a Chromium sandbox or zygote error in a trusted local environment, retry with a temporary Puppeteer configuration. Disabling the browser sandbox reduces isolation; do not use this fallback for untrusted input:

```bash
puppeteer_config=$(mktemp)
cat > "$puppeteer_config" <<'JSON'
{"args":["--no-sandbox","--disable-setuid-sandbox"]}
JSON

render_mmd -p "$puppeteer_config" \
  -i "$smoke_dir/smoke.mmd" \
  -o "$smoke_dir/smoke.svg"
```

Reuse the same `render_mmd` function and, when needed, the same `-p "$puppeteer_config"` option for each project diagram.

If rendering still fails because the browser, network, or system libraries are unavailable, keep the valid `.mmd` file, report the exact error category, state that the diagram task is incomplete, and give the user the exact rerun command. Do not install OS packages or modify project dependencies from this skill. Never report successful rendering unless a non-empty output file exists and contains SVG markup.

Render each project diagram to a temporary output path, validate it, then move it into place. Check for an existing `.mmd` or `.svg` before writing; preserve it unless the user explicitly requested an update. Do not leave a partial output file after a failed render.

If practical, inspect the rendered SVG or PNG to catch clipping and legibility issues. Keep the Mermaid source as the editable source of truth.

## Output conventions

- Use lowercase kebab-case slugs for filenames.
- Keep source diagrams in `.mmd` and rendered diagrams in `.svg`.
- Keep evidence notes in Markdown, with source file paths and line references.
- Do not overwrite existing diagrams without checking them first. Update only the requested diagram(s), preserving unrelated work. Use a temporary output and an existence/content check before replacing an existing SVG.
- Do not add generated images or diagrams to application code or runtime directories.

## Final response

Summarize what was created and link to repository-relative file paths. State which `.mmd` and validated `.svg` files exist and which important paths/components they cover. If any requested SVG is missing, say that the diagram task is incomplete and explain the rendering failure. Mention meaningful source uncertainty. Keep the report concise and do not claim more verification than was performed.

## Example requests

- “Generate a Greptile-style architecture diagram for `src/economy/`.”
- “Show how deposits move from the command handler into the database.”
- “Map the classes and call flow inside `VillagerDatabase`.”
- “Create local diagrams for these three modules and save them under `docs/diagrams/`.”
