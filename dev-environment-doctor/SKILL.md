---
name: dev-environment-doctor
description: >-
  Diagnoses local developer environment problems (missing tools, runtime
  versions, PATH, package manager mismatch, Docker, env files, ports,
  certificates, local DB) and gives exact fix steps. Maintains
  .agents/DEV_ENVIRONMENT.md. Use when setup fails, the app will not start, or
  the local environment is broken.
---

# Dev Environment Doctor

Diagnose friction in the local environment. Give exact fix steps. Do not "helpfully" rewrite the user's machine config without saying what to run.

## Diagnose

- missing tools
- wrong runtime versions
- broken PATH
- package manager mismatch
- Docker issues
- env file problems
- port conflicts
- certificates
- local DB issues

## Workflow

1. Read AGENTS.md, MEMORY.md, README, `.nvmrc`/`.node-version`/`mise.toml`/`package.json` engines, `.env.example`, compose files, setup scripts.
2. Compare expected vs actual (runtime, package manager, required CLIs). Cite the file that defined the expectation.
3. Check `.env` exists vs `.env.example` **keys only**. Never print secret values.
4. Check Docker/compose, ports, certs, local DB only if this repo uses them.
5. Write or update `.agents/DEV_ENVIRONMENT.md` (machine-oriented: expected tools, versions, commands, common failures). No secrets.
6. Output exact commands to run, in order, for this OS when known.

Do not install tools or mutate Docker/DB unless the user asks. Do not invent version requirements.

## Output

```markdown
# Environment Doctor

## Status
healthy | degraded | blocked
## Findings
Each: check, expected, actual, fix command.
## Exact fix steps
Ordered.
## DEV_ENVIRONMENT.md
created | refreshed | unchanged
```

Also maintain:

```text
.agents/DEV_ENVIRONMENT.md
```
