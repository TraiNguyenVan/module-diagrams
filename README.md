# Module Diagrams

Generate **source-grounded architecture diagrams for real codebases** with your coding agent. This skill traces implementations and call sites, writes editable Mermaid diagrams, and can render them to local SVG files.

[![skills.sh](https://skills.sh/b/TraiNguyenVan/module-diagrams)](https://skills.sh/TraiNguyenVan/module-diagrams)

> Understand how a module works—not just what its folder structure looks like.

Module Diagrams is inspired by the code-understanding and visual-explanation workflows associated with Greptile-style reviews. It is an independent agent skill, not a Greptile integration: it works in your local repository and does not post PR comments.

## What it creates

| Diagram or artifact | Purpose |
| --- | --- |
| **Architecture overview** | Shows major components, responsibilities, boundaries, and dependencies. |
| **Sequence diagram** | Explains an important runtime path in call order. |
| **Data-flow diagram** | Tracks where data originates, how it changes, and where it is returned or persisted. |
| **Class or ER diagram** | Captures class/interface or data-model relationships when they are useful. |
| **Evidence notes** | Records the source files—and line references where practical—that support key relationships. |

The skill favors a small set of readable diagrams over a single, oversized map. It should omit unsupported connections and call out uncertainty instead of presenting guesses as facts.

## Quick start

### 1. Install the skill

Requires **Node.js 22.20 or later** for the `npx skills` CLI.

Install interactively in the project where you want to use it:

```bash
npx skills add TraiNguyenVan/module-diagrams
```

Install specifically for OpenCode and Codex:

```bash
npx skills add TraiNguyenVan/module-diagrams -a opencode -a codex
```

To install it globally for reuse across projects, add `-g`:

```bash
npx skills add TraiNguyenVan/module-diagrams -g
```

### 2. Ask your coding agent

For example:

> Generate a Greptile-style architecture diagram for `src/economy/`. Trace the important dependencies and database interaction flow from the actual implementation. Save the Mermaid source, SVG rendering when available, and source evidence under `docs/diagrams/`.

That's it. The agent reads the requested code and creates the diagram files in your repository.

## Example output

A typical output directory might look like this:

```text
docs/diagrams/
├── economy-architecture.mmd
├── economy-architecture.svg
├── economy-flow.mmd
├── economy-flow.svg
└── economy-evidence.md
```

The exact files depend on the module and whether additional diagrams clarify its behavior. The `.mmd` files are editable Mermaid source; `.svg` files are renderings; the evidence file explains the important source-backed relationships. These names illustrate the expected structure—no diagrams are generated until you run the skill on your codebase.

## Installation options

### Install for one or more agents

The CLI prompts you to choose installation targets when agent flags are omitted. To select targets explicitly:

```bash
npx skills add TraiNguyenVan/module-diagrams -a opencode -a codex
```

Use agent identifiers supported by your installed version of the CLI. The [Skills CLI documentation](https://github.com/vercel-labs/skills/blob/main/docs/commands.md) lists additional agents and options.

### Install globally

```bash
npx skills add TraiNguyenVan/module-diagrams -g -a opencode -a codex
```

### List, check, update, or remove

```bash
# Preview skills discovered in this repository
npx skills add TraiNguyenVan/module-diagrams --list

# List installed skills
npx skills list

# Check for available updates
npx skills check

# Update installed skills
npx skills update module-diagrams

# Remove the skill when no longer needed
npx skills remove module-diagrams
```

For details about installation scope, non-interactive installs, and other source formats, see the [official CLI reference](https://github.com/vercel-labs/skills/blob/main/docs/commands.md).

## How it works

1. **Scope the module.** Inspect repository guidance, entry points, and the boundaries of the requested component.
2. **Trace real behavior.** Follow symbol definitions and call sites; consult tests, configuration, schemas, and persistence or network boundaries where relevant.
3. **Choose useful views.** Create the smallest set of architecture and runtime/data-flow diagrams that explain the requested behavior.
4. **Record evidence.** Link important components and relationships to repository-relative source paths, adding line numbers where practical.
5. **Validate and render.** Check Mermaid syntax and readability. If Mermaid CLI is available, render SVG files; otherwise keep the Mermaid source and report the blocker.

## Rendering diagrams

Rendering is optional. If [Mermaid CLI](https://github.com/mermaid-js/mermaid-cli) is already installed, run it against a generated source file:

```bash
mmdc \
  -i docs/diagrams/economy-architecture.mmd \
  -o docs/diagrams/economy-architecture.svg
```

Or invoke it without adding a project dependency:

```bash
npx --yes --package @mermaid-js/mermaid-cli mmdc \
  -i docs/diagrams/economy-architecture.mmd \
  -o docs/diagrams/economy-architecture.svg
```

Rendering can depend on your environment and the browser/system libraries required by Mermaid CLI. If it fails, the skill should preserve the editable `.mmd` source and state what prevented rendering. It should not claim an SVG was created unless the file exists.

## Accuracy and limitations

- **Evidence first.** Relationships should be traceable to inspected code, tests, or configuration; uncertainty should be marked or the edge omitted.
- **Readable over exhaustive.** Large modules may need multiple diagrams. A diagram is not a guarantee that every code path has been captured.
- **Agent-dependent.** This is a source-reading workflow, not a dedicated static-analysis engine. Accuracy depends on the coding agent and the evidence it actually inspects.
- **Local by default.** The skill creates documentation files; it does not edit application code, install project dependencies, commit changes, push branches, open pull requests, or post GitHub comments.

## Troubleshooting

**The skill is not discovered.** Confirm the CLI found it with `npx skills add TraiNguyenVan/module-diagrams --list`, then verify that your agent supports Agent Skills and reload it if needed.

**Only Mermaid source was created.** That is expected when Mermaid CLI cannot run. Follow the rendering instructions above or inspect the `.mmd` file in a Mermaid-compatible viewer.

**The diagram contains a questionable relationship.** Ask the agent to trace that specific edge back to its implementation and call sites, then revise the diagram and evidence notes.

## Contributing and feedback

Bug reports, unclear diagrams, and focused improvements are welcome through [GitHub Issues](https://github.com/TraiNguyenVan/module-diagrams/issues). For a diagram-quality report, include the requested module, the questionable relationship, and the source location that demonstrates the expected behavior.

## License

No license file is currently included in this repository. Public visibility alone does not grant additional permissions to reuse or redistribute the project.
