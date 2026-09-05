---
name: test-impact
description: >-
  Given changed files, determines which unit, integration, and E2E tests matter
  and runs the relevant subset first. Use when selecting tests after a change,
  speeding up monorepo feedback, or deciding what to run before a full suite.
---

# Test Impact

Given changed files, determine which unit tests matter, which integration tests matter, which E2E tests matter — then run only the relevant subset first.

This speeds feedback loops in monorepos. It is not a substitute for required full CI; it is the first loop.

## Workflow

1. Collect the change set (working tree, PR diff, or named paths).
2. Read test layout from MEMORY.md / AGENTS.md / repo config. Do not guess runner flags.
3. Map production files → colocated/unit tests (name patterns, imports, package graph).
4. Map to integration tests that touch the same modules, APIs, or DB.
5. Map to E2E only when UI routes, user flows, or service contracts changed.
6. Rank: unit subset → integration subset → E2E subset → full suite if required by AGENTS.md or CI.
7. Run the first relevant subset. Report exact commands and results. Never claim a command passed unless it ran successfully.
8. If the subset is empty, say why (no tests, untestable layer, generated-only change).

Do not invent a test runner. Prefer existing scripts (`package.json`, Makefile, turbo/nx, CI). Skip unrelated packages.

## Output

```markdown
# Test Impact

## Change set
## Unit
paths + command
## Integration
## E2E
## Run first
Exact command(s), in order.
## Results
## Still needed for merge
Full suite / CI jobs not run yet.
```

Do not commit. Do not modify tests unless the user asked to add coverage (`test-generator` / `task-bootstrap`).
