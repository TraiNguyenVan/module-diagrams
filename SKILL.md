---
name: module-diagrams
description: Generate source-grounded, Greptile-style architecture and flow diagrams for local code modules. Use when asked to visualize or understand a module's components, dependencies, call paths, data flow, or runtime behavior. Produces local Mermaid source and, when possible, SVG renderings; does not post to GitHub.
---

# Module Diagrams

Generate useful, accurate diagrams of an existing codebase for local viewing and documentation. The goal is to help a developer understand how a module actually works—not merely to visualize its folder structure.

## Default behavior

- Work locally in the current repository.
- Treat the requested module, directory, class, feature, or execution path as the scope. If the user names no scope, use the immediately relevant context; otherwise inspect the repository and choose a clearly bounded module, stating the choice in the final response.
- Read source code before drawing conclusions. Trace important symbols to their definitions and call sites; inspect tests, configuration, schemas, and documentation when they establish behavior.
- Create diagrams and supporting evidence under `docs/diagrams/` by default. If the repository already has an established documentation/diagram location, use it instead.
- Do not edit application code, build configuration, dependencies, or package manifests. Do not commit, push, open a PR, or post anything to GitHub. This skill's default output is local files only.
- Do not invent components, relationships, runtime behavior, APIs, or data flow. Label uncertainty explicitly.

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

For each significant component or relationship, record source evidence in the evidence file with repository-relative file paths and line numbers where practical. Explain what the diagram shows, how its boundaries were chosen, and any unresolved or inferred relationships. Cite definitions and call sites rather than relying on filenames alone.

### 4. Check accuracy and readability

Before finishing:

- Verify that names and edges match the code inspected.
- Verify that sequence order follows the actual call path, not an assumed ideal flow.
- Distinguish direct calls from indirect/event/configuration-driven relationships.
- Remove speculative or low-value nodes and edges. If a relationship is uncertain, omit it or mark it as uncertain in the evidence notes.
- Check for duplicate nodes, ambiguous arrows, unreadable labels, syntax errors, and unnecessarily long edge crossings.
- Do not claim the diagram is exhaustive unless you actually checked all relevant paths.

### 5. Render locally when possible

Prefer rendering an SVG alongside each `.mmd` file. If `mmdc` (Mermaid CLI) is already available, use it:

```bash
mmdc -i docs/diagrams/<module>-architecture.mmd -o docs/diagrams/<module>-architecture.svg
```

If it is not installed, do not modify project dependencies or manifests. When environment policy and network access permit, a one-off invocation can be used:

```bash
npx --yes --package @mermaid-js/mermaid-cli mmdc \
  -i docs/diagrams/<module>-architecture.mmd \
  -o docs/diagrams/<module>-architecture.svg
```

Use the same approach for additional diagrams. If rendering cannot be done because the CLI, browser runtime, network, or system libraries are unavailable, keep the valid `.mmd` file, report the blocker, and give the user the exact render command. Never report successful rendering unless the output file exists and was checked.

If practical, inspect the rendered SVG or PNG to catch clipping and legibility issues. Keep the Mermaid source as the editable source of truth.

## Output conventions

- Use lowercase kebab-case slugs for filenames.
- Keep source diagrams in `.mmd` and rendered diagrams in `.svg`.
- Keep evidence notes in Markdown, with source file paths and line references.
- Do not overwrite existing diagrams without checking them first. Update only the requested diagram(s), preserving unrelated work.
- Do not add generated images or diagrams to application code or runtime directories.

## Final response

Summarize what was created and link to repository-relative file paths. State which diagram formats exist (`.mmd`, `.svg`) and which important paths/components they cover. Mention any rendering failure or meaningful uncertainty. Keep the report concise and do not claim more verification than was performed.

## Example requests

- “Generate a Greptile-style architecture diagram for `src/economy/`.”
- “Show how deposits move from the command handler into the database.”
- “Map the classes and call flow inside `VillagerDatabase`.”
- “Create local diagrams for these three modules and save them under `docs/diagrams/`.”
