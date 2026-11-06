---
name: setup-tracker
description: "Configure the work tracker for this repository: Linear, Plane, ClickUp, local Markdown, or another tracker. Writes the agent adapter docs/agents/tracker.md. Use only when the user explicitly asks to set up, switch, or repair the tracker; never invoke it on your own, since choosing the tracker is the user's decision."
---

# Setup Tracker

Write the per-repository adapter that lets agent skills operate the project's
work tracker without knowing which tracker it is. Skills speak in **roles**
(where a work item is in its lifecycle) and **verbs** (what to do with it).
`docs/agents/tracker.md` maps both to this project's statuses and operations.

This is a prompt-driven setup, not a script. Explore, present findings and a
recommendation, show drafts, and write only after the user confirms them.

## The contract

The adapter is assembled from [the adapter template](templates/adapter.md), which
defines the roles, verbs, and common rules, plus one tracker section:

- [Linear](templates/linear.md)
- [Plane](templates/plane.md)
- [ClickUp](templates/clickup.md)
- [Local Markdown](templates/local.md): `docs/backlog.md` is the tracker.
- **Other** tracker: write the tracker section from the user's description,
  covering every verb and role in the template. Ask for what is missing.

Keep the template's role and verb names unchanged; consuming skills depend on
them. Adapt everything else to the project.

## 1. Explore

Read what exists; assume nothing:

- Repository root, Git remote, and visibility. A public repository must not
  receive private tracker IDs, URLs, workspace slugs, or client names.
- `AGENTS.md` and `CLAUDE.md`: decide which one gets the tracker pointer. Use
  the file that holds the rules (a `CLAUDE.md` that only imports `AGENTS.md`
  does not), and note any existing work-tracking section.
- `docs/contributing.md`, `docs/backlog.md`, and `docs/plans/`: the documented
  tracker and fallback contract, plan layout, and backlog format, including
  any convention for keeping finished task links.
- `docs/agents/tracker.md`: a previous run. Re-runs update it in place.
- Existing tracker tooling: a project tracker skill, a CLI wrapper, tooling docs,
  ignored local configuration. The adapter routes to these
  for access mechanics instead of duplicating them.
- Connected tracker MCP servers and tools available in this session.
- `docs/agents/issue-tracker.md` or another adapter from a different skill
  collection. One repository needs one tracker contract: ask whether the new
  adapter replaces it or should link to it, and do not leave two competing ones.

## 2. Recommend and confirm the tracker

Lead with a recommendation the user can accept in one word: the tracker the
repository already documents, otherwise the one with a connected integration,
otherwise local Markdown. Changing the canonical tracker of a project that
already has one is a migration, not setup; confirm it explicitly.

For a remote tracker, perform one harmless read before promising anything:
identify the workspace and the team or project for this repository, then read
its actual statuses (with their category), labels, and enabled features. If
access fails, say which part of setup is blocked; local Markdown is not a silent
substitute.

## 3. Map roles and check automations

Propose a mapping from each role in the template to the project's real statuses
or labels. Start from the status categories the tracker exposes; ask only about
genuinely ambiguous roles, usually `in-review` and `intake`. Do not create or
rename statuses or labels without explicit approval.

- Each role must be distinguishable. When two roles share a status, typically
  `intake` and `backlog` without a triage feature, the adapter names a label
  that tells them apart.
- `in-review` must exist in some form. Without a dedicated status, map it to a
  label on the `in-progress` status, and record that choice. It never falls
  back to `done`.
- Any other role with no suitable status may map to a label, or stay unmapped
  with the fallback written down.

Check the tracker's source-control automations. An automation that moves an item
to a `done` status on merge bypasses user acceptance: recommend mapping it to the
`in-review` status, and record the decision in the adapter.

## 4. Draft and write

Show the user drafts of:

- `docs/agents/tracker.md`.
- A short `## Work tracking` pointer in the rules file chosen in step 1. If that
  section exists, update its tracker pointer in place and keep the rest; never
  append a duplicate.
  If neither `AGENTS.md` nor `CLAUDE.md` exists, create `AGENTS.md`, which
  Claude Code and other agents read. Never create a second rules file here.
- When `docs/contributing.md` describes tracker operations, reduce that part to
  the policy and a link to the adapter, so the operations have one home.

For a remote tracker, leave `docs/backlog.md` as it is. The adapter's fallback
section adds `## Pending sync` to it during an outage, within the existing
backlog format, or creates the file then.

Suggested pointer:

```markdown
## Work tracking

<Tracker> owns active work. Agent operations, role mapping, and fallback rules:
[docs/agents/tracker.md](docs/agents/tracker.md).
```

Write after confirmation. Keep private identifiers out of tracked files: resolve
them at run time, or keep them in ignored local configuration that the adapter
names. Do not create remote statuses, labels, or work items as part of setup
unless the user asks.

## 5. Report

State the tracker, the role mapping, roles left unmapped, automations that need
attention, and files written. `change` and `execute-tasks` now read the adapter
for intake, status transitions, and acceptance. The user can edit `docs/agents/tracker.md`
directly later; re-run this skill to switch trackers or rebuild the mapping.

The setup process and the role-mapping table are adapted from
[mattpocock/skills](https://github.com/mattpocock/skills) (MIT).
