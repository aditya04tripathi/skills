# Skills

A library of [Cursor Agent Skills](https://cursor.com/docs) for repository intelligence, planning, debugging, review, and delivery.

Each skill is a directory with a `SKILL.md` file. Cursor injects the skill when the request matches its description. Skills invoke each other instead of duplicating workflows.

This repo has no application UI. The diagram below is the visual map of how the skills compose.

```text
codebase-intelligence
├── understand
│   ├── implementation-pathfinder
│   └── change-impact
├── build
│   └── task-bootstrap
├── debug
│   ├── bug-triage
│   ├── root-cause-analysis
│   ├── flaky-test-hunter
│   └── test-impact
├── review          (internal specialists; not separate skills)
├── maintain
│   ├── monorepo-health
│   └── dependency-upgrader
├── environment
│   └── dev-environment-doctor
└── delivery
    └── atomic-commits
```

## Setup

Skills are discovered from a personal skills directory or a project `.cursor/skills/` folder.

### Personal (all projects)

```bash
git clone https://github.com/aditya04tripathi/skills.git
mkdir -p ~/.cursor/skills
for skill in \
  atomic-commits bug-triage change-impact codebase-intelligence \
  dependency-upgrader dev-environment-doctor flaky-test-hunter \
  implementation-pathfinder monorepo-health root-cause-analysis \
  task-bootstrap test-impact
do
  ln -sfn "$(pwd)/skills/$skill" ~/.cursor/skills/$skill
done
```

Codex-compatible agents also look under `~/.agents/skills/`. Use the same loop with that path if needed.

### Project (this repo only)

```bash
git clone https://github.com/aditya04tripathi/skills.git
mkdir -p your-app/.cursor/skills
for skill in atomic-commits bug-triage change-impact codebase-intelligence \
  dependency-upgrader dev-environment-doctor flaky-test-hunter \
  implementation-pathfinder monorepo-health root-cause-analysis \
  task-bootstrap test-impact
do
  ln -sfn "$(pwd)/skills/$skill" your-app/.cursor/skills/$skill
done
```

Copy instead of symlink if you want a snapshot that does not track this repo.

Restart Cursor or open a new agent chat after installing. Ask for a skill by name, or describe the task using the trigger terms in each description.

## Skills

| Skill | When to use |
| --- | --- |
| [codebase-intelligence](codebase-intelligence/SKILL.md) | PR review, merge-readiness, architecture briefing, optional remediation. Default mode is review-only. |
| [task-bootstrap](task-bootstrap/SKILL.md) | Turn a ticket or feature request into an implementation plan before coding. |
| [implementation-pathfinder](implementation-pathfinder/SKILL.md) | Decide where a feature belongs and which abstractions to reuse. |
| [change-impact](change-impact/SKILL.md) | Blast radius of a proposed change: callers, APIs, schemas, downstream packages. |
| [bug-triage](bug-triage/SKILL.md) | Rank hypotheses from a report, logs, stack trace, or failing test. |
| [root-cause-analysis](root-cause-analysis/SKILL.md) | Non-trivial bugs: first incorrect state transition, not just symptoms. |
| [flaky-test-hunter](flaky-test-hunter/SKILL.md) | Intermittent, order-dependent, or unstable tests. |
| [test-impact](test-impact/SKILL.md) | Which unit, integration, and E2E tests to run first after a change. |
| [monorepo-health](monorepo-health/SKILL.md) | Package boundaries, cycles, workspace drift, build graph waste. |
| [dependency-upgrader](dependency-upgrader/SKILL.md) | Controlled patch, minor, major, or security-only upgrades. |
| [dev-environment-doctor](dev-environment-doctor/SKILL.md) | Local setup failures: tools, PATH, Docker, env files, ports, local DB. |
| [atomic-commits](atomic-commits/SKILL.md) | Produce a copy-pasteable script of small conventional commits. Does not run git write commands. |

### `codebase-intelligence` extras

| File | Role |
| --- | --- |
| [reference.md](codebase-intelligence/reference.md) | Review templates, specialist output contract, merge-ready status |
| [specialists.md](codebase-intelligence/specialists.md) | Domain checklists for spawned reviewers |
| [thresholds.md](codebase-intelligence/thresholds.md) | Hard refactoring thresholds (file/function size, complexity) |

Modes: `review-only` (default), `fix-critical`, `fix-all`, `merge-ready`.

## Conventions

- **Read first.** Skills read `AGENTS.md` and `.agents/MEMORY.md` in the target repo before inventing architecture or commands.
- **Default is report.** Planning, triage, impact, and health skills do not modify code unless asked.
- **Commits go through `atomic-commits`.** Other skills do not invent commit grouping or message format. That skill emits a script; it does not run `git add` or `git commit`.
- **No secrets.** Skills must not print or persist credential values from `.env`, logs, or config.
- **No fabricated evidence.** Unknowns stay unknown. Passing tests are only claimed when they actually ran.

## Authoring

```text
skill-name/
├── SKILL.md          # required: frontmatter + instructions
├── reference.md      # optional: templates, contracts
└── …
```

Frontmatter requires `name` (lowercase, hyphens) and `description` (what + when, third person, trigger terms). Keep `SKILL.md` concise; put long templates in sibling files one level deep.

## License

[MIT](LICENSE) © 2026 Aditya Tripathi
