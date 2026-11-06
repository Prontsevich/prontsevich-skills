# Delegated Execution

Read this before the first worker launch. Read the worker/reviewer contracts
yourself before assigning their work; send each agent its absolute contract
path and all necessary source context.

## Runtime and routing

Use the actual tools and schema exposed by this runtime. Do not assume Claude,
Codex, or another harness has identical roles, model names, or fork parameters.

Choose a narrow implementation role when available, otherwise a fresh worker.
Use the parent's model by default. If explicit routing is authorized and the
runtime permits overrides, choose by ambiguity and risk:

- Fast: mechanical, bounded transformations.
- Balanced: routine multi-file work with moderate judgment.
- Strongest suitable reasoning: contracts, concurrency, migrations, security,
  or costly silent regressions.

Do not infer context capacity or reasoning support from a model nickname.
When uncertain, prefer the stronger suitable tier or re-bound the unit.
Honor runtime restrictions on model overrides.

Use a fresh, minimal agent context with explicit inputs. For the current
multi_agent_v1 API, use `fork_context: false`, with `agent_type: "task_worker"`
or `"task_reviewer"` when those roles exist. Other runtimes may use different
parameters; inspect them rather than copying this example blindly.

## Dispatch and evidence

Before launch, show a short dispatch card:

- Unit index/name and role.
- Requested model/reasoning, or inherited configuration.
- Routing rationale and allowed paths.
- Expected worker checks and next boundary.

After launch, record the returned agent id and actual configuration when
exposed. Record any fallback explicitly. Never infer actual model from intent.

The worker starts with:

- Absolute worker-contract path, repository root, and branch.
- Selected source and full unit scope/acceptance.
- Allowed paths, dependencies, non-goals, and unresolved decisions.
- Applicable guidance/adapter paths and pre-existing dirty paths.
- Worker-scoped checks and parent verification baseline.
- Explicit prohibition on commits, staging, pushes, plan-state/tracker changes,
  or starting the next unit.

The worker's local implementation plan refines steps within this boundary;
it does not reopen product decisions or substitute for the parent plan.

In a shared checkout, after launch wait for the worker. No parent writes,
mutating commands, or second worker until it finishes. Read-only inspection
and progress updates are allowed. This serial-write rule overrides generic
encouragement to fill concurrency slots.

## Detect a stalled worker

A worker can wait forever on a permission prompt nobody is there to answer: an
auto-mode classifier denies a command, the worker retries, and one call never
returns. A wall-clock deadline is the wrong guard, because it kills a worker that
is only thinking, and a large unit can legitimately run longer than any deadline
worth setting. Judge progress instead, with what the runtime exposes:

- **Completion is delivered and a job hub exists** (omp `hub`, or similar): write
  no watcher. Wait in five-minute windows, and on each window read the job's state
  and the tail of its history. One quiet window during a build or a test run means
  nothing. Two consecutive windows with no new tool call is a stall.
- **The launch returns a transcript file** (Claude Code's `output_file`): its
  modification time is a free progress signal, since every tool call and message
  appends a line. Run one background probe per dispatch on the exact path the
  launch returned. Never discover the file by listing the newest one, because the
  newest file there is the probe's own log. Stop the probe when the completion
  notice arrives, since a finished worker stops writing too.

  ```bash
  F=<transcript path from the launch result>
  IDLE_LIMIT=240   # four minutes without a new line is a stall, not thinking
  while true; do
    now=$(date +%s)
    # BSD/macOS stat; on Linux use `stat -c %Y`. A failed stat counts as a stall.
    m=$(stat -L -f %m "$F" 2>/dev/null) || { echo "WATCHER: cannot stat $F"; break; }
    [ $(( now - m )) -gt "$IDLE_LIMIT" ] && { echo "STALLED: no output for $(( now - m ))s"; break; }
    sleep 20
  done
  ```

- **Supervised workers with their own protocol** (`check --wait` and similar): the
  wait is the protocol's, and absence of news is a checkpoint, never proof of death.

Read the transcript file only through its timestamp. Opening it floods the
parent's context.

On a stall, diagnose before stopping: the agent's age and status, and whether the
command it claims to run exists in the process list. An empty process list next
to a "running" agent means it is blocked on permission, not working. Stop it and
take the unit over yourself. Its edits up to that point are usually intact on
disk, so never re-dispatch on top of half-finished edits.

## Review and bounded fixes

Surface the worker report's outcome, file purposes, checks/results, deviations,
and manual risks before review. Do not collapse it to "worker finished."

For an independent reviewer, supply the reviewer contract, acceptance, complete
unit diff/scope, worker report, applicable guidance, and existing dirty paths.
Remain read-only. Publish the launch card and record its id/verdict.

A criteria-based reviewer and a defect-first pass are complementary lenses.
Use an available review skill when useful; do not claim an absent skill ran.
The parent still owns the combined verdict and independent command results.

If changes are required, prefer a follow-up to the same worker when its context
is intact. Pass the bounded findings and wait again. Re-review after changes.
A very small parent fix is acceptable only while no worker is active.

## Ledger

Keep one row per unit with worker launch, reviewer launch/verdict, parent
verification, commit status, and tracker state. Preserve routing at launch
instead of reconstructing it from conversation after compaction.

When a launch field is unavailable, write `Not exposed`. When an agent was
not used, write `Parent-only` with the reason. Command failures and unmet
manual criteria remain visible. Add detailed evidence for units with findings,
deviations, repeat rounds, or residual risk.
