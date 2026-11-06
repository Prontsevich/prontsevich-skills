---
name: project-docs
description: Set up, audit, migrate, or maintain project documentation using one authoritative home per fact, living versus historical documents, concise agent routing, and plan lifecycle. Use when bootstrapping or adapting a documentation system, deciding where a new fact or decision belongs, reconciling living docs after a verified change, activating or closing an implementation plan, or auditing for stale or duplicate docs; ordinary single-document edits need not activate this skill.
---

# Project Documentation

Make project knowledge easy to find, accurate, and cheap to maintain. Apply the
documentation model while adapting its layout to the project at hand.

Communicate in the user's language. Write repository artifacts, filenames,
commands, and agent instructions in English unless the repository has chosen
another language. Keep historical source material and existing documents in
their original language; translate only on request, not to normalize the layout.

## Choose the requested scope

- **Bootstrap:** establish documentation for a new or sparsely documented project.
- **Adapt:** bring an existing documentation system into the model, preserving
  useful conventions and history.
- **Audit:** report gaps and a concrete proposed mapping without changing files.
- **Maintain:** route new knowledge, reconcile current docs, close a specific
  documentation gap after a change, or activate or close an implementation plan.

An explicit request to set up or adapt documentation authorizes scoped local
documentation changes; restructuring existing files follows the confirmation
rule below. A request to inspect or discuss a possible approach is an
audit. Ask only about consequential choices not resolved by the project or user.

Read [the documentation model](references/documentation-model.md) for bootstrap
or adaptation, and the relevant sections for maintenance. For existing documents
that will move, merge, or change role, also read
[migration guidance](references/migration.md).

## Discover before prescribing

Inspect repository instructions, Git status, root README, documentation indexes,
placement rules, plan indexes, and relevant agent entrypoints. Inventory actual
documents, including feature-local notes and references outside `docs/`. Determine:

- What knowledge each document owns, its audience, and whether it is living or
  point-in-time. Filenames alone do not establish its role.
- Which repository, product document surface, and tracker are already canonical.
  Inspect relevant connected context only when needed; do not provision services.
- Which current docs contradict each other or completed evidence; which apparent
  duplicates are historical snapshots rather than competing current copies.
- What reads the paths: links, agent rules, templates, scripts, skills, or other
  repositories. Preserve those contracts during a migration.

Documentation inspection does not require a full application audit. If a current
implementation claim needs verification, inspect the relevant code and checks;
follow the repository's code exploration rules. Restate an old claim as
current only after evidence confirms it.

When adapting or restructuring, add new documents and update existing ones
directly within the authorized scope. Before moving, merging, renaming, or
deleting existing documents, show a compact finding and the intended mapping,
and wait for the user's confirmation. Plan state moves under the activation and
closure rules need no separate confirmation. Keep unresolved factual conflicts
visible, with both versions kept until evidence or the user decides between
them.

## Establish the smallest useful system

Use the model's defaults only where no useful convention exists. A typical
bootstrap establishes a root quickstart, `docs/README.md` navigation,
`docs/contributing.md` placement and maintenance rules, and concise documentation
routing in `AGENTS.md` or the existing agent entrypoint. Create a `CLAUDE.md`
only when the project needs Claude-specific rules; an existing one imports
`AGENTS.md` instead of copying it.
[Examples](references/examples/) show minimal shapes for these files; adapt
them to the project rather than copying them.

Create topic folders, templates, registers, and plan indexes only when actual
content or a recurring workflow needs them. A small project can keep living
contracts as a few flat files. Every document starts with real content; a tree
of empty architecture, capability, decision, standards, or product documents
adds no knowledge.

For each fact, choose one authoritative home and link from other surfaces:

- Version implementation-bound knowledge with its code.
- Keep current contracts separate from dated designs, plans, and evidence.
- Keep active work in the configured tracker; local backlog is canonical only
  without an available configured tracker, or temporary pending synchronization.
- Keep agent entrypoints short by applicability, using pointers for narrow detail.
- Preserve the chosen home for product/client material. Do not require a separate
  documentation repository or a particular tracker for every project.

Record the project's actual choices in `docs/contributing.md`: placement table,
living/historical distinction, language, naming, tracker/fallback contract, plan
states, indexing, and closure. Link it from documentation and agent entrypoints.
This repository rule is the ongoing operating contract; this skill establishes
and maintains it.

### Agent adapters

`docs/agents/` holds one adapter for each skill that needs this project's
choices. Bootstrap and adapt create the missing adapters and repair drifted
ones, without waiting to be asked:

- `execute-tasks.md`: write it from what the repository settles. It holds the
  verification baseline (the actual test, build and check scripts and how to
  scope them), review tooling, worker routing per harness, and any closure
  steps the project adds to a plan's close. Write only the parts the repository
  actually settles.
- `change.md`: when the project has a proposal or plan template, a capability
  registry, or steps that record a shipped change, write where each lives and
  what a proposal must carry. Skip it when planning uses only the defaults.
- `release-notes.md`: when the project ships versions (release tags, version
  bumps, a deploy entry point), invoke `release-notes` to set up its adapter.
  Skip a project that never releases.
- `tracker.md`: `setup-tracker` runs only at the user's request, because
  choosing the canonical tracker is the user's decision. When the adapter is
  missing, say so in the report and ask the user to run `/setup-tracker`. Never
  write a tracker adapter yourself.

Link the adapters from the documentation navigation, and list in the report
which adapters were created or repaired.

## Handle existing conventions deliberately

`docs/specs/` can be a living capability registry or a set of dated design
records. Preserve the semantic role and choose another explicit home
if both are needed. Similarly, `docs/plan.md` can be a living architecture
document, and `docs/tasks.md` can be completed history. Classify by content,
not by name.

Preserve equivalent plan state names: `backlog/planned`, `active`, and
`archive/done/completed`. New systems default to `planned/active/done`.
Folder state follows the rules in
[Plan lifecycle and closure](references/documentation-model.md#plan-lifecycle-and-closure).

## Maintain, activate, and close

Update affected living documents alongside verified changes. Keep proposed
behavior in its design/plan until implementation is established.

This skill owns the definitions of plan activation and closure. To activate a
plan, apply [Activating a plan](references/documentation-model.md#activating-a-plan);
to close one, apply [Closing a plan](references/documentation-model.md#closing-a-plan);
both under the repository's `docs/contributing.md` rules. For an activation or
closure, read only the plan, its index, `docs/contributing.md`, the living docs
it affects, and documents that link to the plan, and report the state change and checks rather than the whole
documentation structure. Technical closure can
coexist with pending QA, deployment, or user acceptance in the tracker. Never
manufacture completion or accept the work on the user's behalf.

If available and relevant, `change` authors design and execution artifacts and
`execute-tasks` delivers them and decides when a plan starts and when it is
ready to close; this skill owns documentation organization, reconciliation, and
the activation and closure rules, not their entire workflows. It remains usable
without those skills.

## Verify and deliver

Check changed indexes and local links, including anchors where applicable;
affected inbound references; preservation of unique content and history;
current-versus-proposed statements; plan state consistency against actual
evidence; and that agent entrypoints route to one shared contract. For external
homes, report access or portability limits rather than claiming they were
checked. Documentation-only changes need documentation checks, not an unrelated
application build.

Review the diff for accidental loss, duplicate authority, private tracker details
in public material, and unrelated edits. A repeated run should repair real drift,
not create duplicate indexes or rename a working layout again. Do not commit,
push, publish, create remotes, or mutate an external tracker merely to finish setup.

Report the chosen structure and authoritative homes, changed files, checks
performed, and any unresolved decisions. Provide a short example of where the
next architecture fact, design proposal, task, and completed plan belong.
