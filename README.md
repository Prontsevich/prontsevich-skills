# Prontsevich Skills

Agent skills for planning, executing, and documenting changes in software
projects. They work in Claude Code, Codex, and other harnesses that support the
Agent Skills format.

## Skills

- [change](skills/change/SKILL.md): understand a requested change, investigate
  code and docs, and prepare the smallest implementation-ready approach.
- [execute-tasks](skills/execute-tasks/SKILL.md): execute an approved plan in
  bounded steps with independent review, verification, and documentation closure.
- [project-docs](skills/project-docs/SKILL.md): set up, audit, and maintain
  project documentation with one authoritative home per fact.
- [setup-tracker](skills/setup-tracker/SKILL.md): run once per repository to
  write `docs/agents/tracker.md`, which maps work-tracking roles and operations
  to Linear, Plane, ClickUp, local Markdown, or another tracker. User-invoked
  only.
- [release-notes](skills/release-notes/SKILL.md): list what a deployed version
  changed for its users, as terse bullets that keep the product's own names. A
  project's deploy step can end with it.

The skills form one flow, `change` → `execute-tasks` → `project-docs`, closed by
`release-notes` once a version ships, and each also works on its own.

Per-project choices live in adapters under `docs/agents/`, one per skill that
needs them: `change.md`, `execute-tasks.md` and `release-notes.md`, which
`project-docs` creates during bootstrap, and `tracker.md`, which `setup-tracker`
writes at the user's request.

`change` and `execute-tasks` read the tracker adapter written by
`setup-tracker`. Without one, they keep work local and ask you to run
`/setup-tracker` before writing to a remote tracker.

## Acknowledgements

The `setup-tracker` process and role-mapping table are adapted from
[mattpocock/skills](https://github.com/mattpocock/skills) (MIT, Copyright (c)
2026 Matt Pocock).

## Install

### Claude Code

```sh
claude plugin marketplace add Prontsevich/prontsevich-skills
claude plugin install prontsevich-skills@prontsevich
```

### Codex

```sh
codex plugin marketplace add Prontsevich/prontsevich-skills
codex plugin add prontsevich-skills@prontsevich
```

### omp

```sh
omp plugin marketplace add Prontsevich/prontsevich-skills
omp plugin install prontsevich-skills@prontsevich
```

### Other agents

Copy a folder from `skills/` into the agent's skill directory. The entrypoint
of each skill is its `SKILL.md`.

## Development

Clone the repository and run `scripts/link-skills.sh` to symlink every skill
into `~/.claude/skills` and `~/.codex/skills`. A `git pull` then updates the
linked skills. Set `SKILL_LINK_DESTS` to link into other directories. Do not link into a
harness that also has the plugin installed: both copies load, and every skill
shows up twice. With the plugin in Claude Code, link for Codex only:
`SKILL_LINK_DESTS=$HOME/.codex/skills scripts/link-skills.sh`. The script
leaves an existing real directory in place and reports it.

Maintenance rules for this repository are in [AGENTS.md](AGENTS.md).
