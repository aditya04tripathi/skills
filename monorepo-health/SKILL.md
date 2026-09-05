---
name: monorepo-health
description: >-
  Audits monorepo health: dependency boundary violations, circular
  dependencies, duplicated packages, build graph inefficiencies, shared code
  misuse, workspace drift, and inconsistent scripts. Use when checking
  monorepo structure, package boundaries, workspace drift, or build graph
  problems.
---

# Monorepo Health

For monorepos (and mixed repos with multiple packages). If the repo is a single app with no workspaces, say so and only report what still applies.

## Detect

- dependency boundary violations
- circular dependencies
- duplicated packages
- build graph inefficiencies
- shared code misuse
- workspace drift
- inconsistent scripts

## Workflow

1. Read MEMORY.md / AGENTS.md for intended package graph and architectural rules.
2. Discover workspaces (package.json workspaces, pnpm-workspace, lerna, nx, turbo, cargo workspace, go work).
3. Map package dependencies (declared vs imported). Flag imports that violate allowed direction.
4. Detect cycles.
5. Detect duplicated libraries (same purpose, multiple versions, copy-pasted packages).
6. Inspect pipeline config (turbo/nx/CI) for unnecessary rebuilds / missing dependsOn.
7. Check shared code: apps reaching into other apps; UI bypassing the design-system package; domain logic in the wrong package.
8. Workspace drift: inconsistent Node/package-manager fields, mismatched lint/test scripts, stray packages outside the workspace list.

Default is **report**. Change nothing unless the user asks to fix. Fixes must stay in scope. Commits via `atomic-commits`.

Use `change-impact` before moving packages. New circular dependencies are P1-level; existing ones touched are P2 unless they create runtime risk (align with `codebase-intelligence` thresholds).

## Output

```markdown
# Monorepo Health

## Layout
## Boundary violations
## Cycles
## Duplicated packages
## Build graph
## Shared code misuse
## Workspace drift
## Inconsistent scripts
## Priority fixes
```
