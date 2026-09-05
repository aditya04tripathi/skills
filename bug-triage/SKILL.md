---
name: bug-triage
description: >-
  Triages a bug from a description, logs, stack trace, screenshot, failing
  test, or issue link into ranked hypotheses, affected code paths, reproduction
  steps, minimal instrumentation, and likely fix locations. Optional reproduce-
  and-fix mode. Use when investigating a bug, incident, failing test, or crash.
---

# Bug Triage

Triage first. Fix only in optional mode.

## Input

Accept any of: bug description; logs; stack trace; screenshots; failing test; issue link.

## Output

- probable root causes
- ranked hypotheses
- affected code paths
- reproduction steps
- minimal instrumentation needed
- likely fix locations

## Modes

- `triage` (DEFAULT): diagnose only. Do not modify code.
- `reproduce-and-fix`: reproduce, then apply the smallest correct fix, add a regression test when appropriate, validate. For non-trivial bugs, invoke `root-cause-analysis` before fixing. Commits via `atomic-commits` only if the user asked to commit.

## Workflow

1. Collect evidence. Read MEMORY.md / AGENTS.md for architecture. Use `log-investigator` behaviour when logs are the main signal: correlate timestamps, group repeated failures, trace request IDs, identify the first meaningful error, ignore secondary noise, map errors to source paths.
2. Reproduce if practical (failing test, local steps). If not, state what is missing.
3. Rank hypotheses (most likely first) with evidence for/against.
4. Map each live hypothesis to code paths and likely fix locations.
5. Specify **minimal** instrumentation if still blocked (one log/assertion, not a tracing rewrite).
6. If `reproduce-and-fix` and confidence is high, fix; if non-trivial, run RCA first.

Never claim a root cause without evidence. Distinguish symptoms from causes. Do not discard user work. Do not store secrets from logs in docs.

```markdown
# Bug Triage

## Symptom
## Evidence
## Ranked hypotheses
1. … — evidence — confidence
## Affected paths
## Reproduction
## Instrumentation (if needed)
## Likely fix locations
## Next step
triage complete | needs RCA | ready to fix
```
