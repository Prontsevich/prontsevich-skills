---
name: execute-tasks
description: Execute an approved implementation plan, issue, or explicit task batch through bounded sequential work, visible worker routing, independent review, parent-owned verification, and documentation closure. Use for multi-step implementation or resuming a selected plan; trivial edits can use the direct repository workflow.
---

# Execute Tasks

Own execution and evidence from ready work through verified delivery. Consume
technical planning from `change` or an equivalent source and execute the
selected work as planned; product design, intake, and new scope belong to the
planning side, and a batch holds only the work that was selected.

## Load inputs and repository guidance

Read the tracker adapter `docs/agents/tracker.md` when it exists: it names the
tracker, maps the roles used here (`intake`, `ready`, `in-progress`,
`in-review`, `done`) to this project's statuses, and defines the verbs
(`fetch`, `transition`, `comment`, `capture`) and the rules for every tracker
write. A connected workspace alone does not configure this repository.

Resolve the source in order:

1. An explicit plan path, issue, ordered task list, or previously selected source
   named in a continuation request.
2. The skill adapter's explicitly declared source.
3. Exactly one active plan, when the user requests continuation of active work.
4. A tracker item in the `ready` role only when the tracker adapter's mapping
   and the selection are unambiguous and the user requested its execution.
5. Otherwise ask which source to execute.

Read applicable `AGENTS.md`, the selected source/linked standards, and
`docs/README.md` and `docs/contributing.md` when present. Read the skill adapter
`docs/agents/execute-tasks.md` if present, or `.codex/execute-tasks.md` in a
repository that still keeps it there. It supplies project-specific inputs: source
arguments, the verification baseline, review tooling, worker routing, and extra
closure steps. It is optional and not a second runner. Use required exploration
tools before broad source reads.

Check accepted scope, dependencies, unresolved decisions, and existing execution
authority. Folder/status alone grants no approval. An explicit request to
execute the selected scope is sufficient; proceed without asking for the same
permission again. If new design decisions are needed, return that bounded question to
`change` or the user while continuing independent authorized work.

## Set scope, checks, and commit policy

Inspect Git status, relevant existing diffs, and untracked paths. Preserve
pre-existing changes. Map the source into coherent ordered units with outcome,
allowed paths, dependencies, acceptance, and verification. A unit groups
related checkboxes; a single checkbox does not warrant its own sub-issue or
commit.

Derive a stable verification baseline from repository guidance, CI, and actual
toolchain scripts. Use it to choose proportional checks per unit and batch:
focused checks while iterating, required broader checks at their stated gate.
A documentation-only unit needs documentation checks, not an unrelated
application build.
Missing categories are `Not applicable` or `Unavailable`, never `PASS`;
if a required check cannot be run, record the gap and do not claim full
verification. Honor stronger repository gates.

Record the tracker from the tracker adapter. If the repository documents a
remote tracker but has no tracker adapter, continue authorized local work and
mention `/setup-tracker` only when a tracker update is due: ask the user to run
it rather than guessing statuses. With no tracker at all, record `No tracker`.

Tracker write authority follows from the request:

- A request to execute a tracker item, or a plan linked to one, authorizes
  three writes on that executed item: `transition` to `in-progress` at actual
  start, `comment` with evidence, and `transition` to `in-review` at handoff.
  When the linked item is a container, this covers the child items actually
  executed, not the container, unless the container is itself the one
  bounded deliverable.
- `done` belongs to the user's acceptance. Move an item to `done` only on the
  user's explicit instruction for that item.
- Any other write, such as `capture` of new items or edits to other items or
  containers, needs its own request or a standing rule in the tracker adapter.
- Stricter rules in the tracker adapter or repository guidance take precedence.

Execution cadence and commit permission are separate:

- Default commit policy: propose only, and obtain explicit approval before
  staging/committing.
- A request to execute the whole batch or use autopilot does not by itself
  authorize commits. An explicit request for automatic batch commits does.
- A no-commit request allows all authorized implementation and validation to
  continue; it is not a blocker at every unit.
- Honor requested checkpoints, pauses, and reorderings. Otherwise continue
  ready units within the approved batch after recording each result.
- Push, PR publication, deploy, destructive actions, or broader external writes
  require their own authority.

An unattended batch with automatic commits exists so the user can walk away.
That makes an unnoticed wrong turn, not a slow run, the expensive failure: each
further commit on top of a bad unit makes it costlier to unpick. Automatic
commits therefore lower the bar for stopping. Stop the batch, leave the unit
uncommitted, and wait for the user when any of these holds:

- A required check fails, and one focused fix attempt did not make it pass.
- Review returns something a person would want to decide: a correctness finding,
  an undocumented behaviour change, a security or data-loss risk.
- The worker reports a blocker, an open question, or work outside its unit.
- The change touches paths the unit does not name.
- Commit checks flag a secret, a sensitive file, or conflict markers.
- The unit needs a product decision. Guessing one unattended is the worst outcome
  here.
- The same unit has already been retried once.

Never park a failure by committing it as work in progress. Report one line per
committed unit as the batch runs, and one closing report. If the batch stopped,
that report names the blocking unit, the reason, and the proposed next step.

When commits are authorized, use repository message/splitting conventions,
or an English Conventional Commit describing the change. Keep ephemeral phase
numbers and private tracker metadata out of the subject. Isolate task changes
from pre-existing changes; if a commit cannot be safely isolated, leave it
pending rather than staging unrelated work.

Announce the source, unit map, verification baseline, tracker, commit policy,
available delegation/review tooling, and next actual boundary.

## Activate and track

This skill decides when implementation actually starts; `project-docs` defines
how a plan is activated. Immediately before the first implementation unit,
after readiness checks, call the Skill tool with `project-docs` to activate the
plan with its activation rules, keeping the repository's own plan layout. If
`project-docs` is unavailable, activate by `docs/contributing.md`, or move the
plan to the active state and update its index, and state the substitution.
A completed plan is not an executable source; ask for or select new work.

At actual start, `transition` the executed item to `in-progress` under the
tracker write authority above. Check its role and gates first: work starts
from `ready` or from an item the user explicitly selected, never from `intake`.
Update each item for its own work; a container keeps its own state when one
child starts.

Parent-owned plan checkboxes and execution notes are persistent progress.
Use the harness plan tool as a view when available; it is not a second
persistent checklist. Track at most one active implementation unit.

Plan order governs combined sources; match tracker items by stable identity.
A container is not an implementation unit unless its scope is genuinely one
bounded deliverable. Create remote phase tasks only on request.

On continuation, reconstruct state from the selected source, persistent
progress, Git diff, evidence, and tracker. The source stays the one already
selected; recent commits and worker reports are claims to verify, not proof
of completion.

## Implement, review, and verify each unit

Delegate when it adds value and is supported and authorized in this runtime.
Before delegated execution, read
[delegation.md](references/delegation.md) and
[task-worker.md](references/task-worker.md). Read
[task-reviewer.md](references/task-reviewer.md) before reviewer dispatch.
These contracts preserve visible routing and structured evidence.

Run one write-capable worker at a time in a shared checkout. While it runs,
the parent may read and report but must not edit, run mutating commands, or
start another worker. Wait for completion before review or further writes.

For a small unit or unavailable delegation, the parent may implement directly.
State that substitution and use the same scope/evidence boundaries. Do not
invent an agent/model or call parent self-review an independent agent review.
A repository-required independent review remains an outstanding gate if no
authorized replacement exists.

After implementation:

1. Inspect the complete unit diff, affected callers, tests, and docs. Confirm
   that pre-existing changes survived and no unrelated scope was introduced.
2. Review acceptance and concrete introduced defects. For non-trivial code,
   use an independent read-only acceptance reviewer and a defect-first pass
   through available review tooling. A single review may cover both lenses;
   do not require an unavailable branded review skill. Docs-only or truly
   mechanical changes may use parent-only review with its reason recorded.
3. Publish `PASS`, `CHANGES_REQUIRED`, or `BLOCKED`, findings/coverage, and
   outstanding acceptance. Review PASS does not mean all manual checks ran.
4. Resolve findings inside the same unit, then review the changed surface.
   Reproduce failures before fixing; leave unrelated failures explicit.
5. Independently run the applicable baseline/unit checks. Separate worker
   checks, reviewer evidence, parent verification, and manual checks.
6. Tick a task only when its own acceptance and required checks are satisfied.
   Separate implementation and manual acceptance checkboxes when necessary.
   Keep a unit with an unmet required criterion incomplete.
7. Record result and remaining work; handle commits under the selected policy.
   Stage only explicit task-owned paths when authorized, never `git add .`.

Update affected living docs alongside implementation. Repair a known
contradiction in the unit that exposes it, not at the end of the batch. Append execution evidence/deviations
to the plan without rewriting historical design as current architecture.

## Close the plan and hand off acceptance

This skill decides when a plan is ready to close; `project-docs` defines how.
When every implementation unit is complete and the required technical checks
have passed with recorded evidence, call the Skill tool with `project-docs` to
close the plan with its closure checklist. The repository's
`docs/contributing.md` rules take precedence. Route unfinished work found
during closure with `capture` when the request or a standing adapter rule
authorizes it, or with the tracker adapter's fallback during an outage;
otherwise list it in the report as proposed captures. If `project-docs` is unavailable, close the plan by
`docs/contributing.md` and state the substitution. Without either, reconcile the
living docs this work made stale, record verification, move the plan, update
its index, and report that no full closure checklist was available.

Plan closure and issue acceptance are separate facts. A technically verified
implementation plan can be in `done/` while the item is in `in-review` for
separate delivery/user acceptance. Record that boundary explicitly. If the plan
itself includes a required unperformed manual criterion, keep it active.

Under the tracker write authority above, `comment` the actual evidence on the
executed item and `transition` it to `in-review` as the tracker adapter maps it.
Leave `done` to the user's acceptance, and leave a parent or container in its
state when one unit finishes. An outage does not block independent local work:
follow the tracker adapter's fallback without claiming remote transitions or
building a permanent parallel tracker.

## Reporting

For delegated batches, keep a compact ledger with one row per unit:
outcome, requested/actual worker model and reasoning, agent id, review launch
and verdict, parent checks/results, commit proposal/hash or `Not requested`,
and tracker state or `No tracker`. Use `Not exposed` for unavailable routing
metadata. Separate worker checks from parent verification.

Add detail only for findings, deviations, repeat implementation rounds, manual
checks, or residual risk. For direct work, omit agent-only fields rather than
manufacturing launches. Always state remaining work (or `None`), documentation
updates, plan folder/index, acceptance boundary, and pending synchronization.

The final response must stand alone; collapsed commentary is not a substitute
for the result and its verification evidence.
