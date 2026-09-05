---
name: root-cause-analysis
description: >-
  Performs root-cause analysis for non-trivial bugs by tracing symptoms
  backward, distinguishing cause from side effects, inspecting recent changes,
  logs, and tests, and identifying the first incorrect state transition. Use
  for RCA, incident writeups, or when triage is insufficient.
---

# Root Cause Analysis

For non-trivial bugs. Do not skip `bug-triage` when the issue is still shapeless.

## Method

- trace symptoms backward
- distinguish cause from side effects
- inspect recent changes
- inspect logs/tests
- identify first incorrect state transition
- produce RCA

## Workflow

1. Start from the observable failure (test, log, user report).
2. Walk backward through state: last known good → first incorrect value/event.
3. Separate root cause from cascading side effects.
4. Inspect recent git history and related diffs for the window (`bisect-regression` only if the window is unclear and tests can decide).
5. Inspect tests: why they missed it.
6. Propose a permanent fix (not a symptom bandage) and a regression test.

Do not modify code unless the user asks to implement the fix. If committing, invoke `atomic-commits`. Never invent a cause. If inconclusive, say so and list remaining experiments.

## Output

```markdown
# RCA

## Root cause
## Contributing factors
## First incorrect state transition
Where, when, and what should have been true.
## Why existing tests missed it
## Permanent fix
## Regression test
## Evidence
## Confidence
high | medium | low
```
