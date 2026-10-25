# Module Diagrams

A portable agent skill for generating evidence-backed, Greptile-style architecture diagrams from a codebase's actual source. It creates local Mermaid source files and, when possible, renders SVG diagrams. It does **not** post PR comments or change application code.

## What it generates

- **Architecture diagrams** for components, responsibilities, dependencies, and subsystem boundaries.
- **Sequence diagrams** for important runtime paths.
- **Data-flow diagrams** for data transformations, calls, and persistence.
- **Class or ER diagrams** when those relationships are useful and supported by the source.
- **Evidence notes** that cite repository-relative source files and line numbers for important relationships.

The skill instructs the agent to trace implementations and call sites, keep diagrams readable, and label uncertainty rather than inventing connections.

## Install in a repository

From the repository root:

```bash
mkdir -p .agents/skills
git clone https://github.com/TraiNguyenVan/module-diagrams.git .agents/skills/module-diagrams
```

## Install for your user

```bash
mkdir -p ~/.agents/skills
git clone https://github.com/TraiNguyenVan/module-diagrams.git ~/.agents/skills/module-diagrams
```

Compatible agents need to support the Agent Skills `SKILL.md` format and discover the corresponding skills directory.

## Use it

Ask your coding agent something like:

> Generate a Greptile-style architecture diagram for `src/economy/` and explain the database interaction flow. Save the editable Mermaid source and local SVG under `docs/diagrams/`.

By default, outputs go under `docs/diagrams/` in your project. The editable `.mmd` files are the source of truth; `.svg` renderings are produced when Mermaid CLI is available. If rendering is unavailable, the agent should still save the Mermaid files and report the exact blocker.

## Scope and safety

The skill is a code-reading and documentation workflow, not a dedicated static-analysis engine. Diagram accuracy depends on the agent tracing the relevant implementation correctly. By default, it works locally and does not commit, push, open PRs, or post to GitHub.
