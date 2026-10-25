# Module Diagrams

A portable agent skill for generating evidence-backed, Greptile-style architecture diagrams from a codebase's actual source. It creates local Mermaid source files and, when possible, renders SVG diagrams. It does **not** post PR comments or change application code.

## What it generates

- **Architecture diagrams** for components, responsibilities, dependencies, and subsystem boundaries.
- **Sequence diagrams** for important runtime paths.
- **Data-flow diagrams** for data transformations, calls, and persistence.
- **Class or ER diagrams** when those relationships are useful and supported by the source.
- **Evidence notes** that cite repository-relative source files and line numbers for important relationships.

The skill instructs the agent to trace implementations and call sites, keep diagrams readable, and label uncertainty rather than inventing connections.

## Install with `npx skills` (recommended)

You need Node.js 22.20 or later. From the root of the project where you want to use the skill, run:

```bash
npx skills add TraiNguyenVan/module-diagrams
```

The CLI will prompt you to choose the skill installation target(s). Skills are installed to the current project by default.

### Install for specific agents

For example, install it for OpenCode and Codex in the current project:

```bash
npx skills add TraiNguyenVan/module-diagrams -a opencode -a codex
```

Use the agent names recognized by the CLI for your setup. To install globally so the skill is available across projects, add `-g`:

```bash
npx skills add TraiNguyenVan/module-diagrams -g
```

You can also combine global scope with explicit agents:

```bash
npx skills add TraiNguyenVan/module-diagrams -g -a opencode -a codex
```

### Manage the installation

```bash
# List skills installed in the current scope
npx skills list

# Check for available updates
npx skills check

# Update installed skills
npx skills update
```

See the [Skills CLI documentation](https://github.com/vercel-labs/skills/blob/main/docs/commands.md) for all options and supported agents.

## Manual install

If you prefer not to use the CLI, install the repository in a skills directory. From your project root:

```bash
mkdir -p .agents/skills
git clone https://github.com/TraiNguyenVan/module-diagrams.git .agents/skills/module-diagrams
```

For a user-level install:

```bash
mkdir -p ~/.agents/skills
git clone https://github.com/TraiNguyenVan/module-diagrams.git ~/.agents/skills/module-diagrams
```

Use the directory convention supported by your chosen coding agent. The CLI is recommended because it can configure the skill for selected agents.

## Use it

Ask your coding agent something like:

> Generate a Greptile-style architecture diagram for `src/economy/` and explain the database interaction flow. Save the editable Mermaid source and local SVG under `docs/diagrams/`.

By default, outputs go under `docs/diagrams/` in your project. The editable `.mmd` files are the source of truth; `.svg` renderings are produced when Mermaid CLI is available. If rendering is unavailable, the agent should still save the Mermaid files and report the exact blocker.

## Scope and safety

The skill is a code-reading and documentation workflow, not a dedicated static-analysis engine. Diagram accuracy depends on the agent tracing the relevant implementation correctly. By default, it works locally and does not commit, push, open PRs, or post to GitHub.
