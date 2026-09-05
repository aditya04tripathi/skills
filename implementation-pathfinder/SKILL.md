---
name: implementation-pathfinder
description: >-
  Maps a feature requirement to where it belongs in the repository, which
  abstractions and patterns to reuse, what not to duplicate, and which files to
  inspect first. Use when adding a feature, deciding where code should live,
  avoiding duplication, or when the user asks where a capability belongs.
---

# Implementation Pathfinder

Given a requirement such as "add notifications to mobile", tell the agent where and how to implement it so it does not wander the repo like a tourist with no map.

## Hard rules

- Read `.agents/MEMORY.md` and `AGENTS.md` first. If missing/stale, invoke `codebase-intelligence` intelligence workflow.
- Infer from actual code, not directory names alone.
- Prefer existing abstractions over new ones.
- Do not invent conventions. Do not start implementing unless asked.

## Answer these

- where this feature belongs
- what abstractions already exist
- which patterns to reuse
- what not to duplicate
- which files to inspect first

## Workflow

1. Parse the requirement into domain + surface (API, UI, mobile, worker, CLI, data).
2. Locate the owning app/package/service from memory and workspace layout.
3. Find existing similar features (search names, routes, services, hooks, schemas).
4. Trace their entry points, data flow, and tests.
5. Name the extension point: new module vs extend existing.
6. List anti-patterns for this repo (duplicate clients, bypassing design system, env access outside config, editing generated files).

## Output

```markdown
# Implementation Path

## Belongs in
Package/app/layer and why.

## Inspect first
Ordered file paths.

## Reuse
Existing abstractions and the pattern to copy.

## Do not duplicate
Nearby lookalikes that must not be forked.

## Suggested shape
New files vs edits. Keep this a map, not a full design doc.

## Tests
Where similar behaviour is tested and what to add.
```
