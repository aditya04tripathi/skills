---
name: flaky-test-hunter
description: >-
  Detects flaky tests by inspecting timing assumptions, global state, test
  order dependence, random data, network calls, race conditions, async cleanup,
  and filesystem dependencies, then fixes them. Use when tests are flaky,
  intermittently failing, order-dependent, or unstable in CI.
---

# Flaky Test Hunter

Detect flake sources, then fix them.

Inspect: timing assumptions; global state; test order dependence; random data; network calls; race conditions; async cleanup; filesystem dependencies.

## Modes

- `report` (DEFAULT unless the user asks to fix): findings only.
- `fix`: make tests deterministic; add the smallest change that removes flake. Do not weaken assertions to hide races. Do not delete coverage to make CI green.

## Workflow

1. Identify suspects: named tests, CI logs, retries, "sometimes fails".
2. Read the test and its immediate helpers/fixtures.
3. Check for: `sleep`/arbitrary timeouts; shared mutable globals; order dependence; unseeded randomness; real network; missing await; incomplete cleanup; temp files/cwd races; timezones/clocks; leaked servers/ports.
4. Confirm with evidence (code, logs). If flake cannot be reproduced, still fix clear nondeterminism.
5. In `fix` mode: isolate state, fake clocks/network, await async, unique temp paths, seed RNG, enforce cleanup. Prefer repo test-utils.
6. Run the affected tests more than once when practical. Use `test-impact` for the command.
7. If committing, invoke `atomic-commits`. Never force push.

## Output

```markdown
# Flaky Tests

## Suspects
## Causes
timing | global state | order | random | network | race | async cleanup | filesystem | other
## Fixes
## Remaining risk
## Commands run
```

Do not "fix" a real product bug by loosening the test. If the test is catching a race in production code, report it and invoke `bug-triage` / `root-cause-analysis`.
