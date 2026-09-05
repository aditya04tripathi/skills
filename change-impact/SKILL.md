---
name: change-impact
description: >-
  Analyzes a proposed code change for blast radius: callers and callees,
  affected modules, API and schema impacts, tests, and downstream packages.
  Use when asking what a change will break, assessing impact, planning a
  refactor, or before editing a shared API, schema, or package.
---

# Change Impact

Answer: **what will this break?**

Given a proposed change (paths, symbol, PR, or description), find callers/callees, trace affected modules, check API/schema impacts, check tests, check downstream packages, and highlight hidden blast radius.

## Hard rules

- Read `.agents/MEMORY.md` and `AGENTS.md` for architecture and dependency direction.
- Trace real references in code. Do not guess from names alone.
- Mark unknowns. Do not invent callers.
- Read-only unless the user asks to apply the change.

## Workflow

1. Define the change: files, symbols, types, endpoints, schemas, env, public exports.
2. Find inbound callers and outbound callees (repo-wide, including other packages in a monorepo).
3. Trace affected modules and dependency direction. Flag boundary violations.
4. Check API/schema impacts: REST/GraphQL/RPC, DTOs, OpenAPI, protobuf, generated clients, shared types, migrations.
5. Check tests: unit, integration, E2E that exercise the changed behaviour. Invoke `test-impact` when a run list is needed.
6. Check downstream packages/apps that consume the changed surface.
7. Highlight hidden blast radius: implicit contracts, serialized shapes, feature flags, jobs, mobile clients, docs, generated code.

## Output

```markdown
# Change Impact

## Change
## Direct callers
## Direct callees
## Affected modules
## API / schema
## Downstream packages
## Tests that matter
## Hidden blast radius
## Risk
low | medium | high | critical — with why.
## Safe order of change
```

Do not commit. If the change proceeds and needs commits, invoke `atomic-commits`.
