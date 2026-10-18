---
type: guide
title: sdlc.yaml reference
---

# `sdlc.yaml` reference

> **Generated** from `SdlcConfigSchema` and its key meta by the `docs`
> skill (`sdlc docs generate sdlc-yaml`). **Do not hand-edit** — edit
> the section module under `lib/config/sections/` (or `packages/ts/cli-tool-config/meta.ts`) and regenerate.

Every top-level `sdlc.yaml` key, generated from `SdlcConfigSchema`
(`lib/config/sections/` in the sdlc source) and the `.meta()` each key carries
(`packages/ts/cli-tool-config/meta.ts`). The prose guide to what each section is for is
`conventions/sdlc-yaml.md` in the sdlc source. From a project, `sdlc config list`
shows the effective value of every key and `sdlc config explain <key>` shows one.

Meta columns: **layers** are the files that may set the key (`project` =
`sdlc.yaml`, `local` = `sdlc.local.yaml`, `machine` = the machine config,
`subtree` = a nested `sdlc.yaml`); **applies via** is `read` when consumers read
the key at run time, or `apply:<applier>` when `sdlc apply` must run before a
change is live. A key whose consumers are all `planned:` is reserved: it is
accepted but nothing reads it yet.

## Deprecation

Within a major version a key marked deprecated keeps working and is removed
only in a later release. No key is deprecated today; a deprecated key would add
a **Deprecated** row to its meta table below.

| Surface | Marked by | What happens |
|---|---|---|
| `sdlc.yaml` key | `deprecation: { since, removeIn, use? }` in its section meta (`lib/config/sections/`) | The key loads normally; one stderr warning per project and key names the file that set it and `use`. |
| Command | `deprecated: { since, removeIn, use? }` on its op descriptor (`lib/registry.ts`) | The command runs; one stderr warning names `use`. `--output json` stdout is unchanged. |
| Unknown or invalid key | not in the schema | Still refuses to run (pre-1.0 hard cutover). |

`sdlc project doctor` lists, under `deprecations`, each deprecated key the
project sets and each deprecated command its `hooks:` or `workflows:` call.

## Keys

| Key | Category | Shape | Applies via | Layers |
|---|---|---|---|---|
| [`workflows`](#workflows) | lifecycle | `map<string, list<object> \| object>` | `read` | project, local |
| [`verify`](#verify) | lifecycle | `object` | `read` | project, local, subtree |
| [`lib`](#lib) | lifecycle | `object` | `read` | project, local |
| [`chain`](#chain) | lifecycle | `object` | `read` | project, local |
| [`hooks`](#hooks) | lifecycle | `map<'pre-commit' \| 'pre-merge-commit' \| 'prepare-commit-msg' \| 'commit-msg' \| 'post-commit' \| 'pre-rebase' \| 'post-checkout' \| 'post-merge' \| 'pre-push' \| 'post-rewrite', list<object> \| object>` | `apply:hooks` | project, local |
| [`orchestrator`](#orchestrator) | automation | `object` | `read` | project, local |
| [`pr_check`](#pr_check) | automation | `object` | `read` | project, local |
| [`pr_update`](#pr_update) | automation | `object` | `read` | project, local |
| [`pr_review`](#pr_review) | automation | `object` | `read` | project, local |
| [`notify`](#notify) | automation | `object` | `read` | project, local |
| [`usage`](#usage) | automation | `object` | `read` | project, local |
| [`schedules`](#schedules) | automation | `list<object>` | `read` | project, local |
| [`runner`](#runner) | automation | `object` | `apply:schedule-runner` | local |
| [`profiles`](#profiles) | integration | `map<string, list<string> \| object>` | `read` | project, local |
| [`routing`](#routing) | integration | `list<object>` | `read` | project, local |
| [`router`](#router) | integration | `object` | `read` | project, local |
| [`tracker`](#tracker) | integration | `object` | `read` | project, local |
| [`code_host`](#code_host) | integration | `object` | `read` | project, local |
| [`host`](#host) | integration | `object` | `read` | project, local |
| [`harness`](#harness) | integration | `object` | `apply:harness` | project, local |
| [`mcp`](#mcp) | integration | `object` | `apply:mcp` | project, local |
| [`issues`](#issues) (reserved) | integration | `object (no fields)` | `read` | project, local |
| [`task`](#task) | planning | `object` | `read` | project, local |
| [`backlog`](#backlog) | planning | `object` | `read` | project, local |
| [`knowledge`](#knowledge) (reserved) | planning | `object (no fields)` | `read` | project, local |
| [`lease_authority`](#lease_authority) | infrastructure | `string` | `read` | project |
| [`docs_site`](#docs_site) | infrastructure | `string` | `read` | project, local |
| [`paths`](#paths) | infrastructure | `map<string /^[A-Za-z][A-Za-z0-9_]*$/, string>` | `read` | project, local, machine |
| [`repos`](#repos) | infrastructure | `list<string>` | `read` | project, local, machine |
| [`checkout`](#checkout) | infrastructure | `object` | `read` | project, local, machine |
| [`log`](#log) | infrastructure | `object` | `read` | project, local |

## Lifecycle

### workflows

Named lifecycle-stage workflows: implement, check, judge, pr-review, pr-respond, pr-update, close-out, define (`lib/config/workflows.ts`'s `BUILTIN_WORKFLOWS`), plus any project-defined name — including the `setup`/`setup-hooks`/`check` baseline names that used to live under their own `verbs:` section, retired: everything lives here now, resolved via `resolveWorkflow(name, projectRoot)`. A step is `{skill}`, `{run}`, `{script}`, `{prompt}`, or a bare string (sugar for `{script: <string>}` — a verb's old shell-command list is spelled identically, just under this key). A `{script}` step may set `process_group: false`: the orchestrator otherwise runs it in its own process group and session, with no controlling terminal, so a `/dev/tty` prompt fails there (`sdlc run` keeps the terminal either way). Every built-in name ships a default step list IN CODE, not here — this map only carries PROJECT overrides. An entry replaces the built-in outright (a bare array, or an object's `steps` key — the two are equivalent), or wraps it with `prepend`/`append` step lists; setting both a full list and `prepend`/`append` on one entry fails validation. A `{run: <name>}` step splices in ANOTHER workflow's already-resolved steps, recursively (cycle-guarded) — not a verb lookup. An entry may also set `profile` or `tool` (exactly one) and `host` with no `steps` at all — the one place a workflow's binding and host live, read by `resolveWorkflow` and dispatched by the orchestrate loop and `sdlc session launch` alike (see `WorkflowEntryObjectSchema`'s own doc). `profile` names a `profiles:` entry; `tool` is a reference such as `codex` or `claude/opus@high`, or a wildcard such as `*/max` (quoted in YAML), which expands through `router.chains.order` or the built-in order. An unattended dispatch (`sdlc orchestrate`, `sdlc run <workflow>`) always launches headless: a profile skips an entry sdlc cannot launch headless, and a `tool` that cannot is rejected before any step runs. `engine` was removed in favour of these two keys. A name absent from this map, and not one of the eight built-ins, resolves to an empty step list. This section is data only: it carries no runner logic, so `sdlc session launch` and a later flowline runner can execute the same resolved list.

| Meta | Value |
|---|---|
| Shape | `map<string, list<object> \| object>` |
| Default | `{}` |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `lib/config/workflows.ts#resolveWorkflow`<br>`lib/config/hooks.ts#resolveWorkflow`<br>`lib/services/config/config-file.ts#resolveWorkflow`<br>`lib/services/workflow/ops/show.ts#resolveWorkflow`<br>`lib/services/workflow/ops/run.ts#resolveWorkflow`<br>`lib/services/orchestrator/step-runner.ts#resolveWorkflow`<br>`lib/services/session/ops/launch.ts#resolveWorkflow`<br>`lib/services/git/arm-worktree.ts#resolveWorkflowCommands`<br>`lib/services/pr/ops/review.ts#resolveWorkflowCommands`<br>`lib/services/pr/ops/update.ts#resolveWorkflowCommands`<br>`lib/services/quality/ops/run.ts#resolveWorkflowCommands`<br>`lib/services/quality/baseline.ts#resolveWorkflowCommands`<br>`lib/services/project/ops/doctor.ts#resolveWorkflowCommands`<br>`lib/model/entities/task/ops/preflight-permissions.ts#resolveWorkflowCommands` |

### verify

Settings for `sdlc verify changes`, which checks a changeset against every instruction file (`CLAUDE.md`/`AGENTS.md`) and standard whose scope encloses a changed file. A directory beneath the project root may carry its own `sdlc.yaml` whose `verify:` block shallow-merges over this one for rule sources scoped inside it. Only `profile`, `tool`, and `timeout_ms` nest, and `profile`/`tool` are one binding: a block that sets either replaces both of the parent's. `concurrency` and `exclude` govern the whole run, and `blocking`/`severity_floor` are the project defaults for one process exit code (a standard may override them for its own findings with `enforcement`/`severity_floor`), so a subtree cannot opt itself out of a blocking run.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `enforced` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local, subtree |
| Runs in | cli, loop |
| Consumers | `lib/services/verify/ops/changes.ts#verify`<br>`lib/services/verify/binding.ts#verify`<br>`lib/services/verify/nested-config.ts#verify` |

Default:

```yaml
verify:
  concurrency: 32
  blocking: false
  severity_floor: violation
  exclude: []
  max_prompt_bytes: 400000
  max_files_per_job: 12
  timeout_ms: 600000
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `profile` | `string` | — | Name of the `profiles:` entry whose headless agent `sdlc verify changes` dispatches each judgement to, translated through `@sksizer/agent-call`. Set this or `tool`, not both; with neither set, verify uses `profiles.default` (built in as `["*/standard"]`). The tool must be one agent-call has an adapter for (claude, codex, antigravity, opencode) and its CLI must be on PATH; one that is not installed fails every job rather than silently passing. |
| `tool` | `string` | — | Tool reference (`tool[/model][@effort]`, e.g. `claude/haiku@low` or `codex`) whose headless agent `sdlc verify changes` dispatches each judgement to, or a wildcard (`*/max`, quoted in YAML) that walks every tool in `router.chains.order` or the built-in order. Set this or `profile`, not both. A bare tool name uses the tool's own default model. Verification fans out one call per governing rule source, so a small fast model is usually the right trade. The tool must be one agent-call has an adapter for (claude, codex, antigravity, opencode) and its CLI must be on PATH. |
| `concurrency` | `integer >= 1` | `32` | Ceiling on judgement calls in flight, not a target — a run never opens more than it planned. Purely a rate limit: verification is read-only and no job mutates the working tree, so jobs cannot conflict. Coupled to `max_files_per_job`, and the coupling decides whether sharding helps: a judgement costs about the same wall clock whatever its prompt size, so a run costs roughly `ceil(jobs / concurrency)` of them. Measured on a 63-file branch: 12 jobs at a cap of 6 took 190s, 30 shards at a cap of 12 took 253s, and those same 30 at a cap of 32 took 155s. Lower it if the engine rate-limits, but lowering it below the planned job count is what makes sharding cost more than it saves. |
| `blocking` | `boolean` | `false` | Whether findings at or above `severity_floor` make `sdlc verify changes` exit non-zero. Default false: the run reports and gets out of the way, which is the right posture until a project trusts its rule corpus enough to gate on it. This is the project default only: a standard that sets `enforcement: block` still blocks its own findings when this is false, and one that sets `enforcement: advise` never blocks when this is true. |
| `severity_floor` | `'violation' \| 'tension' \| 'drift'` | `"violation"` | Least serious finding that can fail a blocking run. `violation` (the change contradicts a rule) only; `tension` also admits defensible-but-contrary changes; `drift` admits everything. Applies to findings that follow the project, so it has no effect on them when `blocking` is false; a standard with `enforcement: block` uses its own `severity_floor` (this one when it sets none), whether or not `blocking` is on. |
| `exclude` | `list<string>` | `[]` | Glob patterns removed from the changeset before rule sources are selected. Use for generated or vendored paths that no rule meaningfully governs. A rule source left with no files is dropped and costs nothing. |
| `max_prompt_bytes` | `integer >= 0` | `400000` | Prompt size, in bytes, past which a judgement is reported as oversized. Advisory: nothing is truncated and no job is skipped, because dropping files silently is a worse failure than a degraded judgement. It exists because a judge asked about two hundred files reasons worse about each one than a judge asked about five, and nothing else surfaces that — you get vaguer findings and no signal that scope caused it. With `max_files_per_job` sharding a wide source into slices, this is now a backstop rather than the primary control: it trips on a job whose few files are individually enormous, which sharding by file count cannot prevent. Set 0 to disable. |
| `max_files_per_job` | `integer >= 0` | `12` | Largest number of changed files one judgement may carry. A rule source governing more is split into that-many-file shards, each judged against the same rules and dispatched concurrently. The binding constraint is attention, not context: a judge handed sixty diffs reasons worse about each one than a judge handed a dozen, well before any window fills, and the sixty-file job also sets the run's wall clock because everything else finishes while it is still reading. Findings are attributed to the rule source, not the shard, so a split is invisible in the report. Set 0 to disable sharding. |
| `timeout_ms` | `integer >= 1000` | `600000` | Per-judgement wall clock in milliseconds. Applied by `@sksizer/agent-call`, which no agent CLI provides itself. Generous because a job that times out contributes no findings at all, which reads as a clean pass from the rule source that produced it. Applies per judgement, so a sharded source gets this budget for each of its slices rather than sharing one. A timeout degrades the run rather than failing it: an unreachable agent is not evidence of a violation. |

### lib

Settings for the `sdlc lib` commands, which inspect a library package before it is published.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | unset |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli |
| Consumers | `lib/services/library/ops/history.ts#lib` |

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `boilerplate` | `list<string>` | `[]` | More file names (`*` globs, matched against the base name) that `sdlc lib history` treats as boilerplate, on top of its built-in list of licence, ignore and formatter files. Name the project's own per-package tool files here (a build or lint config every package carries), so a move that carries one is not followed into another package's history. `--boilerplate` adds more for one run. |

### chain

The `sdlc` chain (capture → triage → define → ready → implement → check → judge → review → respond → merge → close-out): a fixed, ordered 11-stage pipeline (`lib/config/chain.ts`'s `CHAIN_STAGE_NAMES`), each bound to a built-in workflow or explicitly `null` for a checkpoint stage — see [[D-0019-sdlc-chain-stages-and-labels]] for the full stage table and rationale. `resolveChain(projectRoot)` returns the effective 11-entry binding list, each stage tagged with the layer (`project`/`local`/`default`) that produced it. `stages` lets a project override one stage's bound workflow; it can never change the stage list itself.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | unset |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | loop, cli |
| Consumers | `lib/config/chain.ts#resolveChain`<br>`lib/services/orchestrator/ticks/issues.ts#runIssuesTick` |

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `stages` | `map<'capture' \| 'triage' \| 'define' \| 'ready' \| 'implement' \| 'check' \| 'judge' \| 'review' \| 'respond' \| 'merge' \| 'close-out', object>` | — | Per-stage override of `lib/config/chain.ts`'s `DEFAULT_CHAIN_BINDINGS`, keyed by one of the 11 fixed stage names (`capture`, `triage`, `define`, `ready`, `implement`, `check`, `judge`, `review`, `respond`, `merge`, `close-out`). An unknown key fails validation — a project may change a stage's bound workflow, never add, remove, reorder, or rename a stage. |

### hooks

Git hooks sdlc runs, keyed by client-side hook name (`pre-commit`, `commit-msg`, `post-checkout`, `pre-push`, …), each resolved by `resolveHook` (`lib/config/hooks.ts`) and run by `sdlc hooks run <hook>`. An entry is a bare array (full replacement) or `{steps, prepend, append}` (replace vs. wrap the resolved list — `sdlc.local.yaml` wraps or replaces whatever `sdlc.yaml` resolved to, each step tagged with its own `{layer, mode}` provenance; hooks have no `env` layer and no built-in default). A hook step is a workflow `run`/`script` step (a git hook cannot drive a session, so `skill`/`prompt` are rejected — including one spliced in from a `run:` target) plus `when` (`always` | `deps-missing`), `on_fail` (`warn` | `block` | `park`), `process_group` (default `true`; `false` keeps the terminal for a step that prompts) and an optional `name`. A Ctrl-C or other signal to sdlc stops the hook: no later step runs, and nothing is parked or notified. Steps come from the repository, so a hook with steps to run needs the repo trusted (`sdlc repo trust`) and exits 21 otherwise; a hook with no steps exits 0 without a trust check. Editing a step list takes effect on the next run; adding or removing a hook name takes effect only after `sdlc apply` rewrites the hook shims.

| Meta | Value |
|---|---|
| Shape | `map<'pre-commit' \| 'pre-merge-commit' \| 'prepare-commit-msg' \| 'commit-msg' \| 'post-commit' \| 'pre-rebase' \| 'post-checkout' \| 'post-merge' \| 'pre-push' \| 'post-rewrite', list<object> \| object>` |
| Default | unset |
| Enforcement | `applied` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `apply:hooks` |
| Layers | project, local |
| Runs in | hook, cli |
| Consumers | `lib/config/hooks.ts#resolveHook`<br>`lib/services/hooks/run.ts#runHooks`<br>`lib/services/apply/hooks.ts#hooksApplier` |

## Automation

### orchestrator

Configures the categorized in-flight limits enforced by `/sdlc:orchestrate` (via `sdlc task inflight`) and the deterministic `sdlc orchestrate run --loop work,prs,merges` tick loop. Missing block or missing keys default to `limits.executor.implement: 5` (and the rest of `limits`' own defaults), `max_awaiting_review: 20`, `review.max_rounds: 3`, `base_health.enabled: true`. An empty `orchestrator: {}` block is treated the same as missing. `.strict()` at this level and on `limits`/`review`/`pr_filters`/`base_health` below rejects unknown keys — including a leftover `orchestrator.notify` block from before notification config moved to the top-level `notify:` section, and the retired top-level `orchestrator.max_implementations`.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `enforced` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | loop, cli, session |
| Consumers | `lib/services/orchestrator/ops/run.ts#interval_seconds`<br>`lib/services/orchestrator/ticks/prs.ts#pr_filters`<br>`lib/services/orchestrator/ticks/merges.ts#pr_filters`<br>`lib/services/orchestrator/ticks/issues.ts#pr_filters`<br>`lib/services/orchestrator/ticks/_pr_action.ts#max_rounds`<br>`lib/model/entities/task/ops/inflight.ts#implement`<br>`lib/services/orchestrator/ticks/_limits.ts#limits`<br>`lib/services/orchestrator/ticks/work.ts#goal`<br>`lib/services/orchestrator/ops/watch.ts#goal`<br>`lib/services/dispatch/router.ts#router`<br>`lib/services/orchestrator/ticks/prs.ts#router`<br>`lib/services/orchestrator/ticks/prs.ts#polling`<br>`lib/services/orchestrator/ticks/merges.ts#router`<br>`lib/services/orchestrator/target-branch.ts#target_branch`<br>`lib/services/orchestrator/ticks/merges.ts#merge`<br>`lib/services/orchestrator/base-health.ts#base_health`<br>`lib/services/orchestrator/ticks/work.ts#base_health`<br>`lib/services/pr/ops/merge.ts#merge` |

Default:

```yaml
orchestrator:
  limits:
    executor:
      implement: 5
      pr_review: 2
      pr_respond: 3
      pr_update: 5
      pr_merge: 3
      close_out: 5
      issues: 2
    schedule:
      total: 1
    session_ttl_minutes: 180
  merge:
    allowed_targets: []
    required_evidence: []
    method: squash
  max_awaiting_review: 20
  loops:
    work: true
    prs: true
    merges: true
    issues: true
    schedule: true
  interval_seconds: 300
  review:
    max_rounds: 3
  pr_filters:
    exclude_labels: []
    exclude_authors: []
    include_labels: []
    exclude_drafts: true
  router:
    enabled: true
    routes:
      - live
      - resume
      - fresh
    live_enabled: false
    max_deliveries_per_pr: 20
    quiet_period_secs: 120
    receipt_timeout_ms: 15000
  goal:
    milestones: []
    until_version: null
  polling:
    full_refresh_secs: 900
  base_health:
    enabled: true
    commands: []
    timeout_seconds: 900
    infra_retry_seconds: 120
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `limits` | `object` | `{"executor":{"implement":5,"pr_review":2,"pr_respond":3,"pr_update":5,"pr_merge":3,"close_out":5,"issues":2},"schedule":{"total":1},"session_ttl_minutes":180}` | Concurrency caps for the orchestrate loop, in two pools (`executor` for task execution, `schedule` for scheduled runs) under an optional overall `total`. A start is allowed only when its own cap, its pool's total and the overall total all have room. A limit bounds sessions already running from earlier passes plus sessions started this pass, not a per-pass count alone (`ticks/_limits.ts`, the one shared gate/count helper every tick reads through). An item blocked by a limit is not dispatched and stays eligible for the next pass: it never advances a cursor, records a review round, or takes a lease. |
| `target_branch` | `string` | — | The branch task work builds against: `/sdlc:task-work` cuts the task branch from `origin/<target_branch>`, opens its PR with that as the base, and takes its quality baseline against its tip. Unset means the repository default branch (the remote's `origin/HEAD`, else `main`), so a repository that sets nothing sees no change. The branch must exist on the remote: a missing target stops the run with an error, never falls back. Set it to an integration branch (e.g. `sdlc-1.0`) to run a body of work as one omnibus PR; `sdlc orchestrate target` prints the resolved value. |
| `merge` | `object` | `{"allowed_targets":[],"required_evidence":[],"method":"squash"}` | The `merges` tick's merge step: it merges a reviewed task PR into an allowed target once the required ledger evidence for the PR head, review threads, CI and mergeability all hold (`sdlc pr merge`, `lib/services/pr/ops/merge.ts`). Missing block means no merging. |
| `max_awaiting_review` | `integer >= 0` | `20` | Informational ceiling on the count of tasks in the `awaiting-review` category (status `in-progress` AND an open PR exists for `task/<basename>`). When the count is at or above this limit, the orchestrator's digest line records `caps-reached=max_awaiting_review` as a warning so the human knows the review queue is saturated. Does NOT block new implementations — awaiting-review tasks have already handed control back to the human; they don't consume implementation slots. |
| `loops` | `object` | `{"work":true,"prs":true,"merges":true,"issues":true,"schedule":true}` | Per-loop enable flags for `sdlc orchestrate run --loop <names>`. A name not passed to --loop never runs regardless of this flag; a name that IS passed but whose flag here is false is dropped from the run (absent from the `loop` field of the op output) rather than run. |
| `interval_seconds` | `integer >= 1` | `300` | Default --interval (seconds) for `sdlc orchestrate run` when the flag is omitted. Ignored under --once. |
| `review` | `object` | `{"max_rounds":3}` | Configures how the `prs`/`merges` ticks dispatch the built-in `pr-review` workflow. Missing block or missing keys default to `max_rounds: 3`, no `rubric_path` (generic inlined rubric only). The model that dispatch runs `pr-review` under is set at `workflows.pr-review.profile` or `.tool`, not here — a static per-project value, deliberately separate from `pr_review.profile`/`pr_review.tool` (the interactive `sdlc pr review` binding) so an unattended review can run a different model from the one the PR author used; the loop does NOT infer a model from the PR author (the author is recorded in the lease and the PR footer, but this dispatch does not read it yet). (An earlier `command` field here backed the OLD `/sdlc:orchestrate` Step 2a opt-in review phase; that phase and its `get-review-policy` op were retired when the orchestrate skill was rewritten as a thin `orchestrate run` wrapper.) |
| `pr_filters` | `object` | `{"exclude_labels":[],"exclude_authors":[],"include_labels":[],"exclude_drafts":true}` | A PR matching an `exclude_*` filter, or excluded by `include_labels` being set and not matched, is skipped by the `prs`/`merges` ticks entirely — not even classified — for excluding e.g. dependabot or a human-owned exploratory PR from autonomous handling, or scoping the orchestrator to only opted-in PRs. |
| `router` | `object` | `{"enabled":true,"routes":["live","resume","fresh"],"live_enabled":false,"max_deliveries_per_pr":20,"quiet_period_secs":120,"receipt_timeout_ms":15000}` | Configures the Router: the delivery fallback chain, whether the optional live-send route may run, and whether the Router is wired into the `prs`/`merges` ticks at all. Missing block or missing keys default to enabled, `[live, resume, fresh]`, `live_enabled: false`, `max_deliveries_per_pr: 20`, `quiet_period_secs: 120`, `receipt_timeout_ms: 15000`. |
| `goal` | `object` | `{"milestones":[],"until_version":null}` | The goal scope the `work` tick cuts its frontier to, so a loop runs toward one release rather than the whole backlog. Dispatch order is the roadmap order either way (`task next` ranks by milestone release order first); this only drops what lies outside the goal. Missing block or missing keys mean no scope. |
| `polling` | `object` | `{"full_refresh_secs":900}` | Configures how the `prs` tick polls open PRs. Missing block or missing keys default to `full_refresh_secs: 900`. |
| `base_health` | `object` | `{"enabled":true,"commands":[],"timeout_seconds":900,"infra_retry_seconds":120}` | The `work` tick's pre-dispatch check of the branch tasks are cut from. When the tip of `origin/<target>` fails a configured command, the tick dispatches nothing new (runs already going continue) and says so once per (sha, command) rather than starting runs that all fail for the same reason. The verdict is cached per sha; only a real non-zero exit is cached red. A failure of the check itself (a spawn error, a timeout, a failed install or fetch) is never cached as red: it is retried after a short TTL and reported separately, and the tick dispatches nothing until a verdict exists. `sdlc orchestrate base-health skip` overrides a red verdict for the current sha. Missing block or keys default to `enabled: true` with no commands, which makes the check a no-op until a repo lists some. |

##### `orchestrator.limits`

Concurrency caps for the orchestrate loop, in two pools (`executor` for task execution, `schedule` for scheduled runs) under an optional overall `total`. A start is allowed only when its own cap, its pool's total and the overall total all have room. A limit bounds sessions already running from earlier passes plus sessions started this pass, not a per-pass count alone (`ticks/_limits.ts`, the one shared gate/count helper every tick reads through). An item blocked by a limit is not dispatched and stays eligible for the next pass: it never advances a cursor, records a review round, or takes a lease.

| Field | Shape | Default | Description |
|---|---|---|---|
| `executor` | `object` | `{"implement":5,"pr_review":2,"pr_respond":3,"pr_update":5,"pr_merge":3,"close_out":5,"issues":2}` | The task-execution pool: every session-starting or PR-handling dispatch the orchestrate loop makes (`implement`, `pr_review`, `pr_respond`, `pr_update`, `pr_merge`, `close_out`, `issues`). A start is allowed only when its own cap, this pool's `total` and the overall `limits.total` all have room. |
| `schedule` | `object` | `{"total":1}` | The schedule pool: concurrency for scheduled runs, separate from task execution so a busy hour of PR work cannot starve a schedule and a schedule cannot starve task work. |
| `total` | `integer >= 0` | — | Optional machine-wide cap across BOTH pools: every executor operation plus every running schedule entry, checked in addition to each one's own cap and its pool total. Unset (the default) means no overall cap. `0` disables every operation and schedule outright. |
| `session_ttl_minutes` | `integer >= 1` | `180` | How long, in minutes, a session record (see `log:`) with no `pid`, no running tagged process and no recorded end still counts as a running session toward the session-counted caps (`pr_review`, `pr_respond`, `close_out`). A record WITH a recorded `pid` counts only while that process is alive, and one without is checked against the running tagged processes, so an orphan (its launcher killed before it wrote `endedAt`) stops counting once the process is gone. This age bound is the fallback for a record with no pid and no other signal (written before the pid was stamped, or caught before the spawn, on a host that cannot be asked): it counts only while younger than this, so it cannot count forever. A session that declared itself `done` never counts. Default 180 (three hours): longer than any single headless review/respond/close-out is expected to run. |

###### `orchestrator.limits.executor`

The task-execution pool: every session-starting or PR-handling dispatch the orchestrate loop makes (`implement`, `pr_review`, `pr_respond`, `pr_update`, `pr_merge`, `close_out`, `issues`). A start is allowed only when its own cap, this pool's `total` and the overall `limits.total` all have room.

| Field | Shape | Default | Description |
|---|---|---|---|
| `total` | `integer >= 0` | — | Optional cap across ALL of the executor operations below combined (`implement`, `pr_review`, `pr_respond`, `pr_update`, `pr_merge`, `close_out`, `issues`), checked in addition to each one's own limit. Unset (the default) means no executor-pool cap — each operation is bounded only by its own key. Schedules are NOT counted here; they have their own pool (`limits.schedule`), so a full executor pool never blocks a schedule. `0` disables every executor operation outright. |
| `implement` | `integer >= 0` | `5` | Hard cap on concurrent `implement` dispatches — tasks currently in the `implementing` category (status `in-progress`, no open PR), read via `sdlc task inflight`. Counts runs already going from earlier passes plus this pass's own: the `work` tick starts each task's define/implement/check/judge sequence as a detached run and returns without waiting, so up to this many tasks work at once and the other ticks are not held up. A live detached run counts even before `task inflight` can see its worktree. At or above this limit, the `work` tick starts nothing this pass, and each remaining ready task stays eligible for the next one. `0` disables `implement` outright: the `work` tick dispatches nothing. |
| `pr_review` | `integer >= 0` | `2` | Cap on concurrent `pr-review` dispatches (the `prs` tick's new-PR path). Counted from live session records (see `log:`) tagged `operation: "pr_review"` plus this pass's own dispatches (`ticks/_limits.ts`). Default 2: a review is a single autonomous pass over a diff, kept conservative relative to `implement`'s 5 since a stuck reviewer session is easy to overlook. `0` disables `pr-review` outright. |
| `pr_respond` | `integer >= 0` | `3` | Cap on concurrent `pr-respond` dispatches (the `prs` tick's NEEDS-RESPONSE/CI-FAILED path, `merges.ts`'s CONFLICTS path, and a `respond` chain-stage advance from `issues.ts`). Counted across passes for the Router's `fresh` route (and the direct non-Router dispatch), which both go through `session launch` and leave a durable session record `ticks/_limits.ts`'s `countLiveSessions` can see on a later pass — but a Router `resume` delivery bypasses `session launch` entirely, so a `resume` still running from an earlier pass is invisible to that count (an accepted partial signal, `ticks/_limits.ts`'s module doc); a `live` delivery into an already-running session starts no new session and never counts at all. Default 3, one above `pr_review`'s: responding to feedback is typically faster per item than a full review. `0` disables `pr-respond` outright. |
| `pr_update` | `integer >= 0` | `5` | Cap on concurrent `pr-update` dispatches (the `merges` tick's stale-branch fan-out). Counted per pass only: it is a deterministic `{script}` step, not an agent session, so no `session launch` record ever exists to scan for it on a later pass (`ticks/_limits.ts`'s module doc). The built-in `pr-update` workflow runs `sdlc pr update --pr {pr} --apply`, so each dispatch can push a rebased or merged head: this cap bounds that rebase/merge work. Default 5, matching `implement`'s. `0` disables the fan-out outright. |
| `pr_merge` | `integer >= 0` | `3` | Cap on PR merges the `merges` tick performs in one pass (`orchestrator.merge`, `ticks/merges.ts`). Counted per pass only: a merge is a deterministic op call, not an agent session, so no session record exists to scan on a later pass. Default 3: each merge moves the integration branch, and the PRs queued behind it then need their own base re-checked. `0` disables the merge step outright. |
| `close_out` | `integer >= 0` | `5` | Cap on concurrent `close-out` dispatches (the `merges` tick's MERGED-verdict path, and a `close-out` chain-stage advance from `issues.ts`). Counted from live session records (see `log:`) tagged `operation: "close_out"` plus this pass's own dispatches. Default 5: a close-out is a short, mostly mechanical wrap-up step. `0` disables close-out outright (merged PRs are held for a later pass, not dropped). |
| `issues` | `integer >= 0` | `2` | Cap on concurrent chain-stage advances dispatched by the `issues` tick, enforced IN ADDITION to whichever of `implement`/`pr_review`/`pr_respond`/`close_out` the advanced stage's own workflow counts against (`ticks/issues.ts`'s `limitedOpForStage`). Counted per pass only. Default 2, matching `pr_review`'s: the issues loop drives the same underlying workflows as `work`/`prs`/`merges`, so this is a secondary throttle layered on top of theirs, not a substitute for them. `0` disables the `issues` loop's stage advances outright. |

###### `orchestrator.limits.schedule`

The schedule pool: concurrency for scheduled runs, separate from task execution so a busy hour of PR work cannot starve a schedule and a schedule cannot starve task work.

| Field | Shape | Default | Description |
|---|---|---|---|
| `total` | `integer >= 0` | `1` | Cap on schedule entries running at once (`sdlc schedule`), enforced by the `schedule` loop (`ticks/schedule.ts` via `_limits.ts`'s `scheduleLimitReason`). Counted from the schedule run lock and state file (`services/schedule/state.ts`'s `runningEntries`), not from session records, so a script-only run counts too. Default 1. There is no per-entry cap because an entry never overlaps itself. The `schedule` loop is not in the default `--loop`, so the cap applies only when that loop runs. A running schedule counts toward the overall `limits.total` that executor operations see. `0` disables schedules outright. |

##### `orchestrator.merge`

The `merges` tick's merge step: it merges a reviewed task PR into an allowed target once the required ledger evidence for the PR head, review threads, CI and mergeability all hold (`sdlc pr merge`, `lib/services/pr/ops/merge.ts`). Missing block means no merging.

| Field | Shape | Default | Description |
|---|---|---|---|
| `allowed_targets` | `list<string>` | `[]` | Branches the `merges` tick may merge task PRs INTO. A PR whose base is not listed is never merged, however green. The default is empty: the orchestrator merges nothing until a project names an integration branch here. `main` is merged into only when it is listed explicitly. |
| `required_evidence` | `list<string>` | `[]` | Evidence-ledger step ids (`.sdlc/pr-evidence/<head_sha>.jsonl`, `lib/services/pr/evidence.ts`) that must hold a passing entry for the PR head before it merges. Empty by default, and an empty list is only valid while `allowed_targets` is empty: setting an allow-list without naming required evidence makes the config refuse to load. The ledger is the single source. The configured gates are required on top of this list without being named in it: every step of the `check` workflow (ids `check#<index>`) and, when `verify.blocking` is on, `verify-changes` and `pr-review`; a recorded failing `verify-changes` verdict from a `block` standard also stops the merge. `pr-review` is the review verdict, recorded by `sdlc pr review-verdict` (reasons `verdict-missing`, `verdict-blocking`); any other id is a gate (`gate-not-run: <step>`, `gate-failed: <step>: <detail>`). |
| `method` | `'squash' \| 'merge' \| 'rebase'` | `"squash"` | How a task PR is merged into its target. |

##### `orchestrator.loops`

Per-loop enable flags for `sdlc orchestrate run --loop <names>`. A name not passed to --loop never runs regardless of this flag; a name that IS passed but whose flag here is false is dropped from the run (absent from the `loop` field of the op output) rather than run.

| Field | Shape | Default | Description |
|---|---|---|---|
| `work` | `boolean` | `true` | Whether the `work` loop (drive ready tasks through implement/check/judge) may run. |
| `prs` | `boolean` | `true` | Whether the `prs` loop (classify open PRs, dispatch review/respond) may run. |
| `merges` | `boolean` | `true` | Whether the `merges` loop (close out merged PRs, update stale ones, respond to conflicts) may run. |
| `issues` | `boolean` | `true` | Whether the `issues` loop (walk open, chain-labeled GitHub issues one stage at a time up to their autonomy ceiling) may run. |
| `schedule` | `boolean` | `true` | Whether the `schedule` loop (start the `schedules:` entries that are due, each as a detached run) may run. |

##### `orchestrator.review`

Configures how the `prs`/`merges` ticks dispatch the built-in `pr-review` workflow. Missing block or missing keys default to `max_rounds: 3`, no `rubric_path` (generic inlined rubric only). The model that dispatch runs `pr-review` under is set at `workflows.pr-review.profile` or `.tool`, not here — a static per-project value, deliberately separate from `pr_review.profile`/`pr_review.tool` (the interactive `sdlc pr review` binding) so an unattended review can run a different model from the one the PR author used; the loop does NOT infer a model from the PR author (the author is recorded in the lease and the PR footer, but this dispatch does not read it yet). (An earlier `command` field here backed the OLD `/sdlc:orchestrate` Step 2a opt-in review phase; that phase and its `get-review-policy` op were retired when the orchestrate skill was rewritten as a thin `orchestrate run` wrapper.)

| Field | Shape | Default | Description |
|---|---|---|---|
| `max_rounds` | `integer >= 1` | `3` | Cap on `prs`/`merges`-tick review/respond round-trips per PR (tracked in the `review_rounds` field of the per-PR cursor) before the loop stops dispatching `pr-review`/`pr-respond` for that PR and fires a `pr.ci_failed` or `pr.needs_response` notification instead of looping forever — repeating every tick thereafter for as long as the PR stays stuck (see the `notify:` section's own event descriptions). |
| `rubric_path` | `string` | — | Project-root-relative path to a code-review rubric doc that `/sdlc:pr-review` reads in full and judges the diff against in its Step 2. Unset (the default for a project that has not configured one) falls back to the eight dimensions and severity scale inlined directly in the skill's own prose, with no project-specific "How to apply" guidance beyond that. This repository sets it to `docs/planning/standards/S-0017-code-review-rubric.md`. |
| `response_policy` | `string` | — | Path (repo-relative) to a standard document (e.g. `docs/planning/standards/S-0018-review-response-policy.md`) the `pr-respond` skill follows when triaging review comments — fix / decline-with-a-reply / verify-before-acting / minimize-churn / loop-guard-at-`max_rounds`. Unset falls back to a short built-in policy inlined in the skill. Read via `sdlc config get orchestrator.review.response_policy`; also surfaced in the Router feedback bundle (`lib/services/dispatch/feedback.ts`) as a path plus one-line summary, so a resumed or live session applies it too. |

##### `orchestrator.pr_filters`

A PR matching an `exclude_*` filter, or excluded by `include_labels` being set and not matched, is skipped by the `prs`/`merges` ticks entirely — not even classified — for excluding e.g. dependabot or a human-owned exploratory PR from autonomous handling, or scoping the orchestrator to only opted-in PRs.

| Field | Shape | Default | Description |
|---|---|---|---|
| `exclude_labels` | `list<string>` | `[]` | PR labels whose PRs the ticks skip. |
| `exclude_authors` | `list<string>` | `[]` | PR author logins whose PRs the ticks skip. |
| `include_labels` | `list<string>` | `[]` | Opt-in scoping: when non-empty, a PR is considered only if it carries at least one of these labels — every other open PR is skipped by the `prs`/`merges` ticks entirely, the same as an `exclude_*` match. Empty (the default) means "all PRs", i.e. this filter is inactive. Turning a busy repo's orchestrator on for the first time commonly sets this to a single opt-in label (e.g. `sdlc:orchestrate`) rather than trusting `exclude_*` to keep up with everything NOT meant for autonomous handling. The one exception is the loop's own work: a PR whose head is `task/<basename>` of a task with a work-run record (a run a `work` tick started or a person dispatched with `sdlc task dispatch`; records are pruned after 14 days) is in scope without the label (nothing labels the PR `/sdlc:task-work` opens), and every `exclude_*` and draft rule still applies to it. |
| `exclude_drafts` | `boolean` | `true` | Skip draft PRs entirely — not even classified — in the `prs`/`merges` ticks, and skip a draft PR at a `review`/`respond` chain stage in the `issues` tick. Default true: a draft is a work-in-progress the author is not yet asking for autonomous review/response on. `false` restores pre-this-flag behavior (drafts treated like any other open PR). One exception: a failed-check draft (its last recorded local check failed) stays in scope of the `prs` tick so reviewer comments on it get a `pr-respond`; it is never reviewed. |

##### `orchestrator.router`

Configures the Router: the delivery fallback chain, whether the optional live-send route may run, and whether the Router is wired into the `prs`/`merges` ticks at all. Missing block or missing keys default to enabled, `[live, resume, fresh]`, `live_enabled: false`, `max_deliveries_per_pr: 20`, `quiet_period_secs: 120`, `receipt_timeout_ms: 15000`.

| Field | Shape | Default | Description |
|---|---|---|---|
| `enabled` | `boolean` | `true` | Whether the `prs`/`merges` ticks route NEEDS-RESPONSE, CI-FAILED, and conflict feedback through the Router (`lib/services/dispatch/`) into a PR's producing session before falling back to a fresh `pr-respond` dispatch. `false` restores the old cold-dispatch-only behavior for every tick, though `sdlc pr route`/`sdlc task dispatch` can still be invoked manually regardless of this flag. |
| `routes` | `list<'live' \| 'resume' \| 'fresh'>` | `["live","resume","fresh"]` | Ordered delivery fallback the Router tries for a subject with a resolvable session, first list entry that succeeds wins: `live` (send into a running session via whichever host opened it — `orca`/`tmux` today, any host with a `send` part), `resume` (headless native resume of the originating harness session with the feedback as the follow-up prompt — `claude -p --resume=<id> -- <message>`, replaying the launch's plugin and prompt-pin flags / `codex exec resume <id> -- <message>`), `fresh` (today's cold dispatch: open a new session with the feedback as the prompt via the existing `pr-respond` workflow). A route missing from this list is never attempted. |
| `live_enabled` | `boolean` | `false` | Whether the `live` route may be attempted by the `prs`/`merges` orchestrate loop. Defaults off: it is optional in the MVP and not yet exercised end-to-end against a real hosted session. `false` skips straight to `resume` for the loop even when `routes` lists `live` first — an explicit `sdlc pr route <n> --route live` still attempts it regardless, since that is a single manual op confirming the route works, not the loop-wide default turning on. |
| `max_deliveries_per_pr` | `integer >= 1` | `20` | Cap on entries kept in one subject's DeliveryRecord `attempts` log (oldest dropped past this). Does not cap how many distinct feedback items can be delivered, and does not cap the `delivered` signature map, which is pruned by PR state and head SHA instead — only how much attempt history is retained. |
| `quiet_period_secs` | `integer >= 0` | `120` | Seconds the Router waits after the newest undelivered comment or review before delivering, so a reviewer who leaves several comments in a row gets one batch. Failing-check and conflict items never start or extend the wait. `sdlc pr route --force` and `--route` skip it. `0` turns the wait off. |
| `receipt_timeout_ms` | `integer >= 0` | `15000` | Milliseconds the `live` route waits, after typing a message into a running session, for a delivery receipt: the host confirming it, a harness hook reporting the prompt (armed only when `log.capture.activity` is on and the session is not mid-turn), or weaker signs the agent started a turn. A receipt that never arrives while a confirming signal was expected sends the delivery on to `resume`/`fresh` (or holds while the session runs); with none expected, the send counts as unconfirmed. `0` checks once and does not wait. |

##### `orchestrator.goal`

The goal scope the `work` tick cuts its frontier to, so a loop runs toward one release rather than the whole backlog. Dispatch order is the roadmap order either way (`task next` ranks by milestone release order first); this only drops what lies outside the goal. Missing block or missing keys mean no scope.

| Field | Shape | Default | Description |
|---|---|---|---|
| `milestones` | `list<string>` | `[]` | Milestone ids or basenames (e.g. `M-XKFC`). When non-empty, the `work` tick dispatches only tasks that belong to one of these milestones (listed in its `tasks:`; failing that named in the task's `related:`; failing that through its `parent_key` task); a task in no milestone is skipped, unless it blocks (through `depends_on`) a task that is in scope, in which case it is pulled in. Empty (the default) means no milestone cut. The same cut as `sdlc task next --milestone`. A name that is not an existing milestone file makes the config refuse to load. |
| `until_version` | `string \| null` | `null` | Release-version ceiling (e.g. `1.0.0`). When set, the `work` tick dispatches only tasks whose milestone ships at or before this version on the roadmap (`## vX.Y.Z` sections of an open roadmap, else the milestone's own `version:`); a task in no milestone, or in a milestone with no version, is skipped (unless it blocks an in-scope task, which pulls it in). Null (the default) means no ceiling. The same cut as `sdlc task next --until-version`. Combined with `milestones`, a task must satisfy both. |

##### `orchestrator.polling`

Configures how the `prs` tick polls open PRs. Missing block or missing keys default to `full_refresh_secs: 900`.

| Field | Shape | Default | Description |
|---|---|---|---|
| `full_refresh_secs` | `integer >= 0` | `900` | Seconds the `prs` tick may skip the full fetch of an open PR whose listed `updatedAt` has not changed since its last full fetch. A PR whose last verdict was `CI-BLOCKED`, `NEEDS-RESPONSE` or `CI-FAILED`, or whose checks were still pending, is always fetched, since GitHub does not bump `updatedAt` for check runs or base-branch moves. `0` fetches every PR every tick. |

##### `orchestrator.base_health`

The `work` tick's pre-dispatch check of the branch tasks are cut from. When the tip of `origin/<target>` fails a configured command, the tick dispatches nothing new (runs already going continue) and says so once per (sha, command) rather than starting runs that all fail for the same reason. The verdict is cached per sha; only a real non-zero exit is cached red. A failure of the check itself (a spawn error, a timeout, a failed install or fetch) is never cached as red: it is retried after a short TTL and reported separately, and the tick dispatches nothing until a verdict exists. `sdlc orchestrate base-health skip` overrides a red verdict for the current sha. Missing block or keys default to `enabled: true` with no commands, which makes the check a no-op until a repo lists some.

| Field | Shape | Default | Description |
|---|---|---|---|
| `enabled` | `boolean` | `true` | Whether the `work` tick checks the health of the target branch before it dispatches. `false` makes the check a no-op: the tick dispatches as it did before the check existed. |
| `commands` | `list<string>` | `[]` | Shell commands the base-health check runs, in order, in a worktree at the tip of `origin/<target>` (`.sdlc/orchestrate/base-health`); the first to exit non-zero makes the base red. An explicit list, not a pointer into `workflows.check`. Default: none, and with none the check is a no-op that reports `unconfigured` and never holds dispatch. Editing the list re-verifies the current sha: a verdict is keyed by sha and by these commands. |
| `timeout_seconds` | `integer >= 1` | `900` | Seconds one whole check (the dependency install and every command together, not each) may run before it gives up; the commands share what the install leaves. A timeout is a failure of the check, not a red base: it is never cached as red, and is retried after `infra_retry_seconds`. The tick blocks for at most this long per check. |
| `infra_retry_seconds` | `integer >= 0` | `120` | Seconds to wait before running the check again for a sha whose last attempt failed for a reason other than a command exiting non-zero (a spawn error, a timeout, a failed install or fetch). Each consecutive failure doubles the wait, up to one hour; a verdict resets it. Until a verdict exists the `work` tick dispatches nothing. `0` retries on every tick. |

### pr_check

Configures how `/sdlc:pr-check` (the `pr classify` op) and the `orchestrate watch` pending-work gate treat review comments and submitted reviews: which authors are ignored as automation noise (`ignored_authors`), and how the PR author's own comments are treated (`author_comments`). The defaults favour solo-developer workflows where the PR author and the reviewer are the same human; teams should set `author_comments: self-notes` to filter the author's own comments out of the actionable-feedback signal. Missing block, missing key, or an unrecognised value all default to `actionable` (and an empty `ignored_authors`).

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop, session |
| Consumers | `lib/services/pr/ops/classify.ts#author_comments`<br>`lib/services/pr/ops/classify.ts#ignored_authors`<br>`lib/services/orchestrator/ops/watch.ts#ignored_authors`<br>`lib/services/dashboard/server.ts#ignored_authors` |

Default:

```yaml
pr_check:
  author_comments: actionable
  ignored_authors: []
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `author_comments` | `'actionable' \| 'self-notes'` | `"actionable"` | `actionable` (default, solo) — author's review comments and submitted reviews count as actionable feedback and can flip the verdict to NEEDS-RESPONSE. `self-notes` (team) — author's comments on their own PR are filtered out as notes-to-self. Automation authors (see `ignored_authors`) are always excluded regardless of this setting. |
| `ignored_authors` | `list<string>` | `[]` | Project-specific automation logins whose comments and reviews never count as actionable feedback (and never wake the orchestrator). The built-in set `github-actions` and `cloudflare-workers-and-pages` is always applied; this list ADDS to it (e.g. `vercel`, `netlify`, `dependabot`) so a new project's CI/deploy bots need config, not a plugin release. Matching is shape-normalized: a trailing `[bot]` suffix is stripped before comparison, so an entry written as `dependabot` or `dependabot[bot]` matches both the suffix-less GraphQL login (`gh pr view`) and the suffixed REST login (`gh api`). Missing key or a non-array value means 'no extra authors' — the built-in set still applies. |

### pr_update

Configures the `survey` and `update` verbs: which PRs are in scope (`author`, `include_drafts`), how an out-of-date branch is brought up to date (`strategy`, `overrides`), the cases that automatically avoid a history rewrite (`guards`), and how conflicts are resolved without a human (`lockfile_install`, `resolvers`, `verify`, `repair`), and when a failed update is retried (`max_failed_attempts`). Every key has a default and the whole block is optional, so both verbs work with no config file at all.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `enforced` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `src/survey.ts#runSurvey`<br>`src/update.ts#runUpdate` |

Default:

```yaml
pr_update:
  author: null
  include_drafts: true
  strategy: rebase
  overrides: []
  guards:
    unpushed_local: skip
    reviewed: merge
    max_rebase_commits: 20
  lockfile_install: null
  resolvers: []
  verify: none
  repair:
    command: null
    on:
      - conflict
  max_failed_attempts: 1
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `author` | `string \| null` | `null` | Only survey/update PRs by this author. `@me` is gh's own token for the authenticated user. Null (the default) means every author — appropriate for a solo repository, where filtering by author would hide nothing. |
| `include_drafts` | `boolean` | `true` | Whether draft PRs are surveyed and updated. Drafts drift like any other branch and a draft left behind for months is exactly the case this exists to surface, so they are included by default. |
| `strategy` | `'rebase' \| 'merge'` | `"rebase"` | How an out-of-date branch is brought up to date. `rebase` (default) keeps each PR's history linear and its diff exactly its own commits, which matters most in a stack — a merge leaves every descendant carrying a merge commit per ancestor update. `merge` never rewrites history and so never needs a force-push. See `guards` for the cases that fall back to merge automatically. |
| `overrides` | `list<object>` | `[]` | Per-PR strategy overrides, first match wins. A rule matches when every field it declares matches. Use it to pin a protected base branch to `merge`, or to let a label opt one PR out. An override beats the `reviewed` and `max_rebase_commits` guards, which are preferences — it does NOT beat `unpushed_local`, which exists to stop a force-push destroying work and is not negotiable. |
| `guards` | `object` | `{"unpushed_local":"skip","reviewed":"merge","max_rebase_commits":20}` | Automatic falls-back to `merge` (or `skip`) for the cases where rewriting history costs more than the tidier tree is worth. `unpushed_local` is checked first and cannot be overridden; the other two are preferences an `overrides` rule may beat. Guards only ever step down from `rebase` — none of them turns a `merge` into a rebase. |
| `lockfile_install` | `string \| null` | `null` | Shell verb that rebuilds lockfiles from their manifests (`bun install`, `cargo generate-lockfile`, …), run in the update worktree. A lockfile conflict is resolved by taking the base side and re-running this, because a lockfile is derived and the merged manifest is what it must be derived from. Null (the default) means lockfile conflicts are left for a human: taking a side without re-deriving would push a lockfile that disagrees with its own manifest, which is worse than not updating. |
| `resolvers` | `list<object>` | `[]` | Project-specific conflict resolvers for generated artifacts, first match wins. A conflict in a file a generator owns is not a judgement call — it is `re-run the generator`, and the run's output is the resolution. Lockfiles are handled without configuration; this is for a project's own codegen (`docs/index.md` rebuilt by `make docs`, say). Every conflicted path in a PR must be claimed by some rung or the update is left for a human. |
| `verify` | `'none' \| 'check' \| 'test'` | `"none"` | What must pass before an updated branch is pushed. `none` (default) because the deterministic tier only ever produces a merge git itself resolved or an artifact a generator rebuilt, and running the project's whole gate once per PR is not affordable across a 50-PR run — CI on the PR is the real gate. `check` runs the host's check commands anyway, for a project where a bad push is expensive. `test` runs the project's explicitly configured test commands (a standalone run reads `test_command`): it never falls back to detection, and when it resolves to no command the update fails before touching any PR. Both are trust-gated like every other project-sourced command: they need the repository trusted or a one-run trust flag. |
| `repair` | `object` | `{"command":null,"on":["conflict"]}` | The last rung of the conflict ladder, called once per kind only when nothing deterministic resolved the problem. Never called in a rehearsal (no `--apply`). |
| `max_failed_attempts` | `integer >= 0` | `1` | Skip a PR once its update has failed this many times at the same head and base sha, until either moves. Failures from `--apply` runs are recorded in `.pr-update/attempts.json`; a successful update clears the PR's entries. 0 disables the check. The retry-failed flag of `update` ignores it for one run. |

##### `pr_update.guards`

Automatic falls-back to `merge` (or `skip`) for the cases where rewriting history costs more than the tidier tree is worth. `unpushed_local` is checked first and cannot be overridden; the other two are preferences an `overrides` rule may beat. Guards only ever step down from `rebase` — none of them turns a `merge` into a rebase.

| Field | Shape | Default | Description |
|---|---|---|---|
| `unpushed_local` | `'skip' \| 'merge'` | `"skip"` | What to do when the head branch is checked out in another worktree that holds commits the remote does not. `--force-with-lease` does NOT protect against this — it compares against the remote, which is unchanged — so a rebase here silently orphans work in progress. `skip` (default) leaves the PR alone; `merge` updates it without rewriting history. |
| `reviewed` | `'merge' \| 'rebase'` | `"merge"` | What to do when a PR carries at least one submitted review. Rebasing rewrites the commits review comments are anchored to, so GitHub marks those threads outdated. `merge` (default) keeps them anchored. |
| `max_rebase_commits` | `integer >= 0` | `20` | Above this many commits ahead of base, fall back to `merge`. A rebase replays every commit and can hit the same conflict once per commit, while a merge resolves it once; past some length that trade stops paying. 0 disables the guard. |

##### `pr_update.repair`

The last rung of the conflict ladder, called once per kind only when nothing deterministic resolved the problem. Never called in a rehearsal (no `--apply`).

| Field | Shape | Default | Description |
|---|---|---|---|
| `command` | `string \| null` | `null` | Shell command run in the update worktree when no resolver fixes a problem — typically an LLM call. Gets the request as JSON on stdin and in `PR_UPDATE_*` variables; exit 0 claims success. It must not commit, move HEAD or create refs: pr-update checks the tree, then commits and pushes itself. Trust-gated like `verify: check`/`test`: needs the repository trusted or a one-run trust flag. Null (the default) means no repair. |
| `on` | `list<'conflict' \| 'verify-failed'>` | `["conflict"]` | Which problems the repair command is asked to fix: an unclaimed `conflict`, a failed `verify` step, or both. |

### pr_review

Settings for `sdlc pr review`, which opens a pull request in a local worktree and starts an interactive review session there. Missing block uses `profiles.default` (built in as `*/standard`) in the current terminal with the built-in brief.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli |
| Consumers | `lib/services/pr/ops/review.ts#pr_review` |

Default:

```yaml
pr_review:
  untrusted: skip-init
  setup: true
  refresh: update
  related_prs: true
  prompt:
    mode: extend
    text: ''
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `profile` | `string` | — | Name of the `profiles:` entry whose coding agent `sdlc pr review` starts in the PR worktree. Set this or `tool`, not both; with neither set, `sdlc pr review` uses `profiles.default` (built in as `["*/standard"]`). The chosen CLI must be on PATH. `--tool` overrides per run. |
| `tool` | `string` | — | Tool reference (`tool[/model][@effort]`, e.g. `codex`) whose coding agent `sdlc pr review` starts in the PR worktree, or a wildcard (`"*/max"`) that walks every tool in `router.chains.order` or the built-in order. Set this or `profile`, not both. The chosen CLI must be on PATH. `--tool` overrides per run. |
| `not_the_author` | `boolean` | — | The author rule: when true, the review never runs on the tool that wrote the PR (the author in the PR's lease and footer). With the author unknown (no trusted footer), every tool may be the author's, so `if_only_author_left` decides. Applies to whatever the review walks (the binding, a `routing:` rule's profile, `profiles.default`) but not to `--tool`: a `--tool` that lands on the author's tool runs, with a warning naming both. Default false. |
| `if_only_author_left` | `'different-model' \| 'wait' \| 'person'` | — | What `not_the_author` does when only the author's tool is left: `different-model` (default) takes an author-tool entry whose model differs from the author's, else the next model of its tier, else the entry flagged that the model may match; `wait` stops so the run can be retried later; `person` stops and hands the review to a person. |
| `host` | `'terminal' \| 'orca' \| 'cmux' \| 'tmux' \| 'herdr' \| 'otty' \| 'wezterm' \| 'vscode' \| 'jetbrains'` | — | Where the review session opens, when it should differ from `host.default`: terminal \| orca \| cmux \| tmux \| herdr \| otty \| wezterm \| vscode \| jetbrains. A host whose binary is missing fails the run rather than falling back. |
| `untrusted` | `'skip-init' \| 'full' \| 'refuse'` | `"skip-init"` | What `sdlc pr review` does with a PR whose head branch lives in another repository (a fork), whose tree is a stranger's code. `skip-init` (default): create the worktree but run no `setup` verbs there — an install would execute that PR's own lifecycle scripts before anyone has read it — and warn that the tree's instruction files and hooks are the PR author's. `full`: treat it like a same-repo PR. `refuse`: exit with an error. `--trust` runs one PR as `full` whatever this says. Same-repo PRs are never affected. |
| `setup` | `boolean` | `true` | Whether `sdlc pr review` runs the `setup` verb in the PR worktree on every launch — created or reused — before the session starts. Idempotency is the verb's own contract, so running it again on a reused worktree is cheap by design. `--no-setup` skips it for one run. |
| `refresh` | `'update' \| 'pull' \| 'off'` | `"update"` | How `sdlc pr review` brings a reused worktree up to date before the session starts. `update` (default): fetch, run `sdlc pr update --apply` on a same-repo PR that is behind its base (this may push to the PR), then fast-forward the worktree to the PR head. `pull`: fetch and fast-forward only. `off`: leave the worktree as it is. A worktree with uncommitted changes, or with commits the PR head lacks, is never touched. `--no-refresh` acts as `off` for one run. |
| `related_prs` | `boolean` | `true` | Whether `sdlc pr review` lists other open PRs that are stacked with the reviewed one or change a shared path, in the prompt's `{related_prs}` placeholder. The lookup fetches `origin` and diffs each candidate PR. `false` skips it and the placeholder reads `none found`. |
| `prompt` | `object` | `{"mode":"extend","text":""}` | How the review brief handed to the session is composed. See `mode` and `text`. |

##### `pr_review.prompt`

How the review brief handed to the session is composed. See `mode` and `text`.

| Field | Shape | Default | Description |
|---|---|---|---|
| `mode` | `'extend' \| 'replace'` | `"extend"` | `extend` appends `text` after the built-in review brief; `replace` uses `text` as the whole prompt. In either mode the placeholders `{pr_number}`, `{pr_url}`, `{pr_title}`, `{pr_author}`, `{base}`, `{head}`, `{repo}`, `{worktree}`, `{related_prs}` and `{default_prompt}` resolve (unknown ones render empty), so a replacement can embed the built-in brief wherever it likes. |
| `text` | `string` | `""` | Project-specific review instructions. Empty (the default) leaves the built-in brief unchanged in `extend` mode; in `replace` mode an empty text is an error. |

### notify

Notification channels and the event catalog that routes each event to zero or more of them. `notify(event, payload, ctx)` (`lib/services/notify/dispatch.ts`) resolves `events[event]` to channel names, looks each one up in `channels`, and dispatches best-effort per channel — one channel failing never suppresses another or fails the caller. `sdlc notify send|test` fires a named channel directly, bypassing event routing. This cross-field check assumes a fully-merged config; validating `sdlc.yaml` or `sdlc.local.yaml` alone (where the split may legitimately span both files) uses the un-refined `NotifyObjectSchema` (the `fileSchema` of this section) instead. A webhook channel `url` is a secret: the loader refuses it in the committed `sdlc.yaml`.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | unset |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | loop, cli, hook |
| Consumers | `lib/services/notify/dispatch.ts#notify`<br>`lib/services/notify/ops/send.ts#runNotifySend`<br>`lib/services/notify/ops/test.ts#runNotifyTest` |

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `channels` | `map<string, object>` | `{}` | Named notification channels, each an object discriminated by `type`: `{type: "desktop"}` (no other fields — raises a notification on the machine running the loop); `{type: "ntfy", topic_url}` (POSTs to an ntfy HTTP topic URL); or `{type: "webhook", url, format?}` (POSTs a JSON body to an arbitrary HTTP endpoint — `format` is `slack`, `discord`, or `raw`, default `raw`). Each name is referenced by one or more `events.*` lists below; a channel not referenced by any event is valid but inert. |
| `events` | `object` | `{"pr.needs_response":[],"pr.ci_failed":[],"task.parked":[],"stage.ceiling_reached":[],"phase.ready_for_review":[],"lease.expired":[],"session.ended":[],"session.waiting":[],"session.done":[],"hook.blocked":[],"orchestrator.dead_stop":[],"usage.budget_exceeded":[],"orchestrator.base_red":[],"orchestrator.base_unverified":[],"orchestrator.base_green":[]}` | Exactly the 15 canonical event names, each an ordered list of channel names (from `channels` above) to fire when that event happens. An event key that is absent, or set to `[]`, is a silent no-op — no channel call, no error. An unknown event key fails validation. |

##### `notify.events`

Exactly the 15 canonical event names, each an ordered list of channel names (from `channels` above) to fire when that event happens. An event key that is absent, or set to `[]`, is a silent no-op — no channel call, no error. An unknown event key fails validation.

| Field | Shape | Default | Description |
|---|---|---|---|
| `pr.needs_response` | `list<string>` | `[]` | A PR classified NEEDS-RESPONSE has exhausted `orchestrator.review.max_rounds` review/respond round-trips (`ticks/_pr_action.ts`). Fires on every orchestrator tick thereafter for as long as the PR stays stuck in that state — there is no dedup or one-shot suppression, so a configured channel repeats this notification, not just once. |
| `pr.ci_failed` | `list<string>` | `[]` | A PR classified CI-FAILED has exhausted `orchestrator.review.max_rounds` review/respond round-trips (`ticks/_pr_action.ts`). Fires on every orchestrator tick thereafter for as long as the PR stays stuck in that state — there is no dedup or one-shot suppression, so a configured channel repeats this notification, not just once. |
| `task.parked` | `list<string>` | `[]` | A task's dispatch is stood down, whether by a blocking hook (`sdlc hooks run` consumes a park marker for the task's branch) or by the `work` tick's own step-failure handling when a dispatched workflow step fails outright (`ticks/work.ts`). The payload carries a `focus` field, the `sdlc session focus …` command for the task. |
| `stage.ceiling_reached` | `list<string>` | `[]` | A chain-labeled issue is already at its `sdlc:auto/<x>` autonomy ceiling (the `issues` tick). |
| `phase.ready_for_review` | `list<string>` | `[]` | A phase reached ready-for-review. No detector wired yet. |
| `lease.expired` | `list<string>` | `[]` | A lease expired (`sdlc lease reconcile`'s `expired-task-leases` detector). `notifyExpiredLeaseAnomalies` (`reconcile.ts`) collapses every anomaly found in ONE reconcile run into a single notify, but that dedup does not persist across runs: the same still-expired lease fires again on every subsequent `sdlc lease reconcile` run for as long as it stays expired — there is no cross-run dedup or one-shot suppression, the same unbounded-repeat shape as `pr.needs_response`/`pr.ci_failed` above. |
| `session.ended` | `list<string>` | `[]` | An agent session ended: fires whenever a headless session record is finalized. The orchestrate step-runner launching a headless skill/prompt step for a workflow tick is the common case in practice; a direct `sdlc session launch --headless` invocation is the other. |
| `session.waiting` | `list<string>` | `[]` | An interactive Claude session entered `waiting_for_input` (a permission or elicitation prompt, or an `AskUserQuestion`): fires once per entry from the hook reporter (`sdlc session hook`), not on each repeated hook while it stays waiting. The payload carries a `focus` field, the `sdlc session focus …` command that brings the session forward. |
| `session.done` | `list<string>` | `[]` | An agent declared its session done with `sdlc session done` (`outcome` is `ok` or `failed`). The session stays open for a next prompt; this is not `session.ended`. The payload carries a `focus` field, the `sdlc session focus …` command that brings the session forward. |
| `hook.blocked` | `list<string>` | `[]` | An `on_fail: block` hook step failed (`sdlc hooks run`). |
| `orchestrator.dead_stop` | `list<string>` | `[]` | The orchestrate loop stopped dead. No detector wired yet. |
| `usage.budget_exceeded` | `list<string>` | `[]` | A usage.budget ceiling (tokens or cost_usd) was exceeded within its rolling window AND the work tick actually had a ready candidate it would otherwise have dispatched this tick (ticks/work.ts) — never fires while already `orchestrate pause`d (that gate is the more informative status) or when there was nothing ready to dispatch anyway. Still repeats every tick that meets both conditions for as long as usage stays over the ceiling — no one-shot-per-crossing suppression, but scoped to ticks where the event's claim ("dispatch was suppressed") is actually true. |
| `orchestrator.base_red` | `list<string>` | `[]` | The `work` tick found the tip of `origin/<target>` failing a `orchestrator.base_health.commands` command and stopped dispatching new work (`services/orchestrator/base-health.ts`). Fires once per (sha, command), not once per tick, and not while the `work` loop is paused or on a dry run. Runs already going continue. |
| `orchestrator.base_unverified` | `list<string>` | `[]` | The base-health check could not answer for a sha: a spawn error, a timeout, or a failed fetch, worktree reset or install. Distinct from `orchestrator.base_red`: no command failed, so nothing is cached red; the check is retried after `orchestrator.base_health.infra_retry_seconds` and the `work` tick dispatches nothing until it has a verdict. Fires once per sha, not once per retry, and not while paused or on a dry run. |
| `orchestrator.base_green` | `list<string>` | `[]` | The base turned green after a red one was announced (a new sha passed): the `work` tick dispatches again. Fires once per recovery, and not while paused or on a dry run. |

### usage

Token/cost usage reporting, the budget gate and the rate-limit cooldown cap. `budget` sets a rolling-window token/cost ceiling that pauses the work tick's dispatch and fires `usage.budget_exceeded` when exceeded. Absent, or present with `budget` absent (or both `budget.tokens`/`budget.cost_usd` unset), means no cap — inert, matching `notify.channels`' 'valid but inert' precedent.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | unset |
| Enforcement | `enforced` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | loop |
| Consumers | `lib/services/usage/budget.ts#checkUsageBudget`<br>`lib/services/usage/cooldown.ts#cooldownMaxMs`<br>`lib/services/orchestrator/ticks/work.ts#runWorkTick` |

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `budget` | `object` | — | Token/cost ceiling that pauses the orchestrator work tick's dispatch and fires usage.budget_exceeded when exceeded. Absent, or present with both `tokens`/`cost_usd` unset, means no cap — inert, matching notify.channels' 'valid but inert' precedent. |
| `cooldown_max_hours` | `number > 0` | — | The longest a rate-limit cooldown (`.sdlc/cooldowns.json`) keeps a tool or model out of routing, in hours. A reset time the tool reports further out, or a stale entry further out, counts only this long; the tool is then tried again and re-cooled if still limited. The writer clamps to it and every reader applies it. Unset: 24. |

##### `usage.budget`

Token/cost ceiling that pauses the orchestrator work tick's dispatch and fires usage.budget_exceeded when exceeded. Absent, or present with both `tokens`/`cost_usd` unset, means no cap — inert, matching notify.channels' 'valid but inert' precedent.

| Field | Shape | Default | Description |
|---|---|---|---|
| `window_hours` | `number > 0` | `24` | Rolling lookback window (hours, ending now) that budget totals are summed over. |
| `tokens` | `integer > 0` | — | Total token ceiling, compared against a BILLABLE per-window sum: UsageRow.inputTokens + outputTokens + cacheWriteTokens, nulls counting as 0 — deliberately EXCLUDING cacheReadTokens (see lib/services/usage/record.ts#sumBillableTokens). Cache reads dominate raw usage and are far cheaper than fresh input, so a ceiling compared against the cache-inclusive ledger total (UsageRow.totalTokens) would trip within a single session. Unset: no token ceiling. |
| `cost_usd` | `number > 0` | — | Total USD cost ceiling (sum of UsageRow.costUsd, nulls counting as 0) across rows inside the window. Only a headless Claude run ever populates a non-null costUsd (claude -p --output-format json's own total_cost_usd) — every interactive row and every codex row (headless or interactive) is always costUsd: null, since codex reports no dollar figure and there is no local price table. This ceiling therefore only ever caps headless-Claude spend, not total spend. Unset: no cost ceiling. |

### schedules

Named schedules: each runs a workflow on a five-field cron expression, with a per-run time limit. Entries merge by `name` across `sdlc.yaml` and `sdlc.local.yaml`. A bad cron expression or time zone, an unknown workflow, a duplicate name or a missing `limits.minutes` refuses to load. Runs only while the orchestrator loop is on.

| Meta | Value |
|---|---|
| Shape | `list<object>` |
| Default | unset |
| Enforcement | `enforced` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | loop |
| Consumers | `lib/services/schedule/due.ts#due`<br>`lib/services/orchestrator/ticks/schedule.ts#runScheduleTick` |

### runner

Per-machine choice to keep the orchestrator running without a terminal: a launchd agent (macOS) or systemd user unit (Linux) that runs `sdlc orchestrate run` with restart on failure. Lives in `sdlc.local.yaml` only; unset installs nothing, and schedules then run only while `sdlc orchestrate run` is up. `sdlc apply` installs and removes the unit; `sdlc project doctor` flags a missing unit or one whose project moved.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | unset |
| Enforcement | `applied` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `apply:schedule-runner` |
| Layers | local |
| Runs in | cli |
| Consumers | `lib/services/apply/schedule-runner.ts#scheduleRunnerApplier` |

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `kind` | `'launchd' \| 'systemd' \| 'none'` | — | Which OS service manager runs `sdlc orchestrate run` for this project: `launchd` (macOS user agent), `systemd` (Linux user unit) or `none` (no runner; removes one `sdlc apply` installed earlier). A kind that does not match this machine is reported and changes nothing. |
| `parent` | `string` | — | Root of this project's parent project, when it has one. Recorded with the project in the machine's runner list; the runner itself does not use it. |

## Integration

### profiles

Named, ordered tool preferences. An entry is a bare list of references (`[claude/opus, codex/max]`, tried in order) or the long form `{order, host, context}`; the launching command, not the profile, picks headless or interactive. A reference is `tool[/model][[qualifiers]][@effort]`: the tool is a model-catalogue tool id (claude | codex | cursor | antigravity | pi | opencode, or one `router.tools` declares), or `*` with a tier for every tool (`*/fast`, in a profile entry, a rule or a binding’s `tool:`); the model a tier (`fast` | `standard` | `max`) or a name; the qualifiers `ctx=<size>` (at least that much context: `1m`, `200k`, or `max`) and traits that filter a tier or wildcard (`local`, `agent`, `reasoning`, `long-context`); the effort a level the tool accepts (`codex/max@high`). Write `[ctx=1m]`, not a tool bracket such as `opus[1m]` or `[context=1m]`. `context:` asks every entry for at least that size, and an entry whose model cannot reach it is skipped. YAML quoting: a reference starting with `*` must be quoted (`"*/fast"`), and one containing `[` must be quoted inside a flow list (`["claude/opus[ctx=1m]", codex/max]`); block lists (`- claude/opus[ctx=1m]`) need no quotes. A workflow, `pr_review`, `verify` or a `routing:` rule names an entry with `profile:`. Two profiles are built in: `default` (`["*/standard"]`), what a launch uses when nothing else names a binding, and `small` (`["*/fast"]`); an entry of either name replaces the built-in whole. A wildcard expands to its category chain: tools in `router.chains.order`, and within a tool the models the classifier places in the tier, newest first, then larger context first. An unknown tool, a reference that does not parse, an effort the tool and model lack, a `[ctx=]` larger than the model offers anywhere, and a reference the model catalogue knows is impossible (`codex/sonnet`, with the nearest valid one) fail the load and name the entry; a model the catalogue does not know only warns, unless `router.strict`.

| Meta | Value |
|---|---|
| Shape | `map<string, list<string> \| object>` |
| Default | unset |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `lib/services/route/route.ts#routeFor` |

### routing

Rules that choose a profile from what a launch is for, tried in order; the first whose `when` holds wins. Each rule is `{when, profile}`. `when` keys: `workflow` (the workflow name), `stage` (the chain stage, set when the `issues` loop advances one: capture | triage | define | ready | implement | check | judge | review | respond | merge | close-out), `complexity` (small | medium | large), `impact` (high | medium | low), `autonomy` (human-only | supervised | autonomous/pr) and `tags` (a list; holds when the task carries any of them). Every key a rule sets must hold, and a launch with no task (a bare `sdlc session launch --workflow`) holds only `workflow`. `profile` names a `profiles:` entry or a built-in one (`default`, `small`). A rule sits between the `--tool` flag and the workflow's own binding: the flag beats every rule, and a matching rule beats `workflows.<name>.profile|tool`, `pr_review` and `profiles.default`. The launch's trace names the rule (`rule[<index>]`) and its `route.chose` event records `via: rule:<index>`. A rule naming an undefined profile, an unknown `when` key, or a value outside its set fails the load and names the rule index; `sdlc project doctor` warns on a rule an earlier rule with the same or broader `when` always beats. Example: `routing: [{when: {workflow: implement, complexity: small}, profile: small}]`.

| Meta | Value |
|---|---|
| Shape | `list<object>` |
| Default | unset |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `lib/services/route/route.ts#routeFor` |

### router

Model catalogue facts layered over the snapshot `@sksizer/tool-router` ships and the machine catalogue `sdlc models refresh` writes, and the policy that maps tiers to models: `providers` (`{kind, endpoint, key_env}`; `key_env` names an environment variable, never a key), `tools` (a field replaces the built-in one, except `serves`, which adds; a new tool needs `serves`, `openness` and `modes`, and runs as the command its id names; a tool’s executable is not config, so `binary` fails the load; a declared tool is never run for `--version`, and its headless template runs only in a repository trusted with `sdlc repo trust`, else exits 21, `UNTRUSTED`), `offerings` (rows that add one tool and model combination, or remove one with `remove: true`), `strict` (default false: a model the catalogue does not know is a warning at launch and in `sdlc project doctor`; true makes it an error that refuses the load, while a deprecated model still only warns), and the mapping policy: `classify` (classifier rules tried before the shipped ones, each `{provider?, families?, match?, by_parameters?, require_traits?}`; the first that matches a model places it in `fast`, `standard`, `max` or `unclassified`, and a model no rule places is in no tier and no chain), `chains.order` (the tool order of every category chain; default claude, codex, cursor, antigravity, pi, opencode) and `pins` (`tool/tier: model`, such as `claude/max: claude-opus-5-5`, held whatever the classifier picks; a Claude Code launch also gets the pinned Claude tiers as `ANTHROPIC_DEFAULT_{OPUS,SONNET,HAIKU}_MODEL`). Unknown keys, an incomplete new tool or provider, a row naming an unknown tool, provider or model, an unknown tool in `chains.order`, and a pin its tool cannot run fail the load.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | unset |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `lib/config/models.ts#modelsCatalogue`<br>`lib/config/models.ts#routeCatalogue`<br>`lib/services/route/pins.ts#pinEnv` |

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `providers` | `map<string /^[a-z][a-z0-9-]*$/, object>` | — | Providers to add or change: `{kind, endpoint, key_env}`. |
| `tools` | `map<string /^[a-z][a-z0-9-]*$/, object>` | — | Tools to add or change. A field replaces the built-in one, except `serves`, which adds. A new tool needs `serves`, `openness` and `modes`, and runs as the command its id names, found on PATH: a declared tool is never run for `--version`, and is launched only in a trusted repository. A tool’s executable is not config: a built-in tool runs as the snapshot names it. |
| `offerings` | `list<object>` | — | Rows that add one tool and model combination, or remove one (`remove: true`). |
| `strict` | `boolean` | — | Refuse a model the catalogue does not know, instead of warning. A deprecated model still only warns. Default false; CI should set it. |
| `classify` | `list<object>` | — | Classifier rules tried before the shipped ones; the first that matches a model places it in a tier, or `unclassified`. |
| `chains` | `object` | — | Category chain settings: `order`, the tool order. |
| `pins` | `map<string, string>` | — | `tool/tier` to the model that tier means on that tool (`claude/max: claude-opus-5-5`), held whatever the classifier picks. |

##### `router.chains`

Category chain settings: `order`, the tool order.

| Field | Shape | Default | Description |
|---|---|---|---|
| `order` | `list<string /^[a-z][a-z0-9-]*$/>` | — | Tool order in every category chain; tools left out follow in catalogue order. Default claude, codex, cursor, antigravity, pi, opencode. |

### tracker

Where this project's external work items live. Absent resolves to GitHub Issues, today's behaviour. The code host is named by `code_host`, not here.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `lib/services/issues/tracker/select.ts#selectTracker` |

Default:

```yaml
tracker:
  kind: github
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `kind` | `'github'` | `"github"` | Which tracker adapter `issues sync`, the `issues` tick and the label applier use. Only `github` (GitHub Issues) exists; more kinds join with their adapters. |

### code_host

Which code host the PR commands, the orchestrator `prs` tick, `session focus` and `project doctor` talk to, via `selectCodeHost`. `pr survey`/`pr update` (the standalone `@sksizer/pr-update` package) and the dashboard read GitHub directly and do not consult this key yet. Every key has a default, so this block is optional.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `src/select.ts#selectCodeHost` |

Default:

```yaml
code_host:
  override: null
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `override` | `'github' \| 'forgejo' \| null` | `null` | Force which CodeHost adapter `selectCodeHost`'s callers use, bypassing auto-detection from the `origin` remote's URL. Null (the default) auto-detects: a `github.com` remote uses the GitHub adapter, anything else uses Forgejo. |

### host

The default host, the default session mode, and when sdlc closes host panes — see `host.default`, `host.session` and `host.close_pane`.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `lib/services/host/index.ts#pickHost`<br>`lib/services/session/ops/launch.ts#pickHost`<br>`lib/services/pr/ops/review.ts#pickHost`<br>`lib/services/session/close.ts#closeSessionPane` |

Default:

```yaml
host:
  default: terminal
  close_pane: on-close-out
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `default` | `'terminal' \| 'orca' \| 'cmux' \| 'tmux' \| 'herdr' \| 'otty' \| 'wezterm' \| 'vscode' \| 'jetbrains'` | `"terminal"` | Where sdlc puts work in front of you when a feature does not say otherwise: `terminal` runs it in the current terminal and returns when it ends; `orca` opens a terminal tab in the Orca app; `cmux` opens a cmux workspace; `tmux` opens a detached tmux session named from the sdlc session id; `herdr`, `otty` and `wezterm` open a workspace or tab in that terminal app; `vscode` and `jetbrains` open a terminal in the IDE window that has the project open, through a companion extension or plugin. A host that does not support this OS is unavailable. Every host runs whichever agent CLI (the tool a `profile:` or `tool:` binding resolves to) the feature resolves, in a terminal/workspace it opens — the two are independent, not a fixed pairing. Every host fulfils the host contract in parts (an agent session, a command in a terminal, a message sent into an already-open one); a feature that needs a part the host lacks fails rather than falling back. A feature's own `host` key (e.g. `pr_review.host`) overrides this, and a `--host` flag overrides both. |
| `session` | `'interactive' \| 'headless'` | — | Whether an orchestrated run opens in a host pane you can watch (`interactive`) or runs detached (`headless`), for this machine. Set it in `sdlc.local.yaml` for a person who always wants panes; a team may commit it in `sdlc.yaml` as the repository default, and the local value wins. Unset defers to the built-in `headless`. Precedence, strongest first: the `--interactive` run flag, the task's `session:` field, `workflows.<name>.session`, this setting, headless. |
| `close_pane` | `'on-close-out' \| 'on-done' \| 'never'` | `"on-close-out"` | When sdlc closes the host pane a session ran in (`tmux kill-session`, `orca terminal close`, ...; hosts without a close command leave the pane open). `on-close-out` (default): closing out a merged task (`sdlc task close-commit`, before the worktree is removed) closes the panes of that task branch's sessions. `on-done`: `sdlc session done` closes the session's own pane as soon as the agent declares it done (the harness is killed with a signal, so the wrapper records the outcome from `done`). `never`: panes are left for you to close. Closing a pane ends whatever still runs in it. |

### harness

Which agent harnesses `sdlc apply` installs sdlc into. A change here takes effect only after `sdlc apply` re-installs.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | unset |
| Enforcement | `applied` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `apply:harness` |
| Layers | project, local |
| Runs in | cli |
| Consumers | `lib/services/apply/harness.ts#harnessApplier` |

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `targets` | `list<string>` | — | Agent harnesses to install the sdlc plugin surface for, by exporter name (`sdlc harness export --target` lists them). Absent means the harness applier picks its default. |

### mcp

Which ops the `sdlc mcp` stdio server exposes as tools. Safe default: only ops marked `mutating: false` are exposed; `allow` opts specific mutating ops in, `deny` excludes specific ops. `sdlc apply` registers the server with each harness, so a change to the registration takes effect only after it runs.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | unset |
| Enforcement | `applied` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `apply:mcp` |
| Layers | project, local |
| Runs in | session, cli |
| Consumers | `lib/services/apply/mcp.ts#mcpApplier`<br>`lib/services/mcp/server.ts#buildMcpServer` |

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `allow` | `list<string>` | `[]` | Additional op paths (space-joined, e.g. "task create") to expose as MCP tools even though they are not marked mutating: false. Opt-in for mutating ops. |
| `deny` | `list<string>` | `[]` | Op paths (space-joined) to exclude from the MCP tool set even if they would otherwise qualify (e.g. a read-only op you don't want exposed). |

### issues

Reserved for issue-tracker sync and the issues loop (sdlc 1.0 Phase 7). No field is defined yet.

| Meta | Value |
|---|---|
| Shape | `object (no fields)` |
| Default | unset |
| Enforcement | `recorded` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | loop, cli |
| Consumers | `planned:sdlc issues sync and the issues loop (sdlc 1.0 Phase 7)` |

## Planning

### task

Task-domain configuration. Groups task-lifecycle execution policy under `execution` (see `execution.spawn_from_post_mortem`).

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, session |
| Consumers | `lib/services/config/ops/get-spawn-policy.ts#spawn_from_post_mortem` |

Default:

```yaml
task:
  execution:
    spawn_from_post_mortem:
      enabled: true
      drive_to_ready: true
      fallback_status: planning/draft
      target:
        pr_grouping: per-task
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `execution` | `object` | `{"spawn_from_post_mortem":{"enabled":true,"drive_to_ready":true,"fallback_status":"planning/draft","target":{"pr_grouping":"per-task"}}}` | Configures how this project executes task-lifecycle automation. Today the only sub-block is `spawn_from_post_mortem`. |

##### `task.execution`

Configures how this project executes task-lifecycle automation. Today the only sub-block is `spawn_from_post_mortem`.

| Field | Shape | Default | Description |
|---|---|---|---|
| `spawn_from_post_mortem` | `object` | `{"enabled":true,"drive_to_ready":true,"fallback_status":"planning/draft","target":{"pr_grouping":"per-task"}}` | Policy for converting a `/sdlc:task-work` post-mortem's friction bullets into follow-up tasks. Missing block defaults to enabled, drive-to-ready, landing undriveable follow-ups at `planning/draft`. |

###### `task.execution.spawn_from_post_mortem`

Policy for converting a `/sdlc:task-work` post-mortem's friction bullets into follow-up tasks. Missing block defaults to enabled, drive-to-ready, landing undriveable follow-ups at `planning/draft`.

| Field | Shape | Default | Description |
|---|---|---|---|
| `enabled` | `boolean` | `true` | Whether `/sdlc:task-work` Step 8 spawns follow-up tasks from the post-mortem's 'Friction and automation gaps' bullets (the spawn-from-post-mortem procedure, which hands each bullet to `/sdlc:spawn-task-pr`). `true` (default) runs the spawn sub-step; `false` skips it entirely — the post-mortem is still written and committed, but no follow-up task or PR is created. A missing key defaults to enabled. |
| `drive_to_ready` | `boolean` | `true` | Whether each spawned follow-up is driven toward `open/ready` before its PR is opened. `true` (default) runs `/sdlc:task-auto-define` then `/sdlc:task-ensure-ready` on the scaffolded task: when the spec can be synthesized from the friction bullet plus codebase context AND passes the readiness gate, ensure-ready promotes the task to `open/ready` with `readiness_verified_at:` stamped. When auto-define reports INSUFFICIENT the task lands at `fallback_status:`; when the gate finds a gap it lands at `planning/needs-definition` — the drive is best-effort, never fabricated. `false` skips the drive and always lands the task at `fallback_status:`. |
| `fallback_status` | `'planning/draft' \| 'planning/needs-definition' \| 'planning/proposed' \| 'planning/backlog'` | `"planning/draft"` | The status a spawned follow-up lands at when it is NOT driven to `open/ready` — either because `drive_to_ready:` is false, or because the best-effort drive's auto-define could not synthesize the spec (a spec that fails the readiness gate lands at `planning/needs-definition` instead). Must be a non-ready `planning/*` status; default `planning/draft` (today's behavior). Set `planning/needs-definition` to route undriveable follow-ups straight into the definition backlog — note that with `drive_to_ready: true` this value also skips the drive entirely (`planning/needs-definition` is not a status `/sdlc:task-ensure-ready` accepts as input), landing the follow-up there directly instead of attempting the gate. |
| `target` | `object` | `{"pr_grouping":"per-task"}` | Controls which PR a spawned follow-up lands in and how that PR is titled. See `pr_grouping` and `pr_title_pattern`. |

###### `task.execution.spawn_from_post_mortem.target`

Controls which PR a spawned follow-up lands in and how that PR is titled. See `pr_grouping` and `pr_title_pattern`.

| Field | Shape | Default | Description |
|---|---|---|---|
| `pr_grouping` | `'per-task' \| 'per-execution' \| 'per-project'` | `"per-task"` | How spawned follow-up tasks are packaged into PRs (per target repo — classification routes Local / Upstream-plugin / Cross-project-request follow-ups to different repos, and a PR cannot span repos). `per-task` (default, today's behavior): one PR per spawned task. `per-execution`: one PR per `/sdlc:task-work` run bundling all that run's follow-ups for a repo. `per-project`: a single rolling PR per repo that accumulates follow-ups across runs (append to the open PR, or start a fresh one — the `sdlc backlog create` rolling pattern). |
| `pr_title_pattern` | `string` | — | Optional template for the spawned PR's title. Placeholders resolved at PR-creation time (unknown ones render empty): `{headline}` (spawned task headline), `{slug}`, `{originating_task}`, `{classification}`, `{date}` (UTC), `{repo}` (owner/name). Absent → each grouping mode uses its built-in default: `per-task` = the task headline (today's behavior); `per-execution` = `chore(tasks): follow-ups from {originating_task}`; `per-project` = `chore(tasks): spawned follow-up tasks`. Note `{originating_task}` is meaningful only for `per-task`/`per-execution` — a `per-project` rolling PR spans many runs, so a per-run token there resolves to the run that first opened it. |

### backlog

Backlog-domain configuration. Groups backlog-capture submission policy under `capture` (see `capture.target`).

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, session |
| Consumers | `cli/backlog_cli/create.ts#capture`<br>`lib/services/config/ops/get-backlog-policy.ts#capture` |

Default:

```yaml
backlog:
  capture:
    enabled: true
    target:
      pr_grouping: per-project
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `capture` | `object` | `{"enabled":true,"target":{"pr_grouping":"per-project"}}` | Policy for `sdlc backlog create` — the deterministic tail behind `/sdlc:backlog-capture`. Missing block defaults to enabled, per-project (rolling) grouping. |

##### `backlog.capture`

Policy for `sdlc backlog create` — the deterministic tail behind `/sdlc:backlog-capture`. Missing block defaults to enabled, per-project (rolling) grouping.

| Field | Shape | Default | Description |
|---|---|---|---|
| `enabled` | `boolean` | `true` | Whether `sdlc backlog create` (the `/sdlc:backlog-capture` tail) auto-submits the captured item to a PR. `true` (default) authors the file, commits it in an ephemeral worktree, pushes, and opens-or-updates a PR — today's behavior. `false` authors the file in the working tree and stops (no commit, no push, no PR) — the item is captured locally for the author to submit by hand. |
| `target` | `object` | `{"pr_grouping":"per-project"}` | Controls which PR a captured backlog item lands in and how that PR is titled. See `pr_grouping` and `pr_title_pattern`. |

###### `backlog.capture.target`

Controls which PR a captured backlog item lands in and how that PR is titled. See `pr_grouping` and `pr_title_pattern`.

| Field | Shape | Default | Description |
|---|---|---|---|
| `pr_grouping` | `'per-project' \| 'per-item'` | `"per-project"` | How captured backlog items are packaged into PRs. `per-project` (default, today's behavior): a single rolling `backlog-capture` PR that accumulates every captured item until triaged. `per-item`: one branch/PR per captured item (`backlog/<slug>`), so each idea is reviewed on its own. (No `per-execution` — backlog capture has no run to bundle.) |
| `pr_title_pattern` | `string` | — | Optional template for the backlog PR's title, resolved when the PR is created (placeholders, unknown → empty): `{id}` (B-NNNN), `{slug}`, `{title}` (headline), `{date}` (UTC). Absent → each grouping mode's default: `per-project` = `docs(backlog): rolling capture`; `per-item` = `docs(backlog): {title}`. For a `per-project` rolling PR the title is fixed by the item that first opened it. |

### knowledge

Reserved for knowledge-base settings. No field is defined yet.

| Meta | Value |
|---|---|
| Shape | `object (no fields)` |
| Default | unset |
| Enforcement | `recorded` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, session |
| Consumers | `planned:no reader yet` |

## Infrastructure

### lease_authority

Default ref authority used by the `sdlc lease *` ops when invoked without an `--authority` override. The value is whatever `git fetch` / `git push` accepts: a remote name (`origin`), an SSH URL (`ssh://…`), an HTTPS URL (`https://…`), a `file://` URL, or a filesystem path (absolute or project-root-relative). When unset, the CLI exits 1 with a setup-pointing error — the lease library accepts the authority as an explicit argument, so callers must configure one before any `sdlc lease` command can talk to the ref source. This is a minimal slice-1 surface; the richer `control_plane.ref_authority` block from the ADR (with `type`, `address`, and `namespace`) is deferred to slice 2/3 when control-plane wiring lands.

| Meta | Value |
|---|---|
| Shape | `string` |
| Default | unset |
| Enforcement | `enforced` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project |
| Runs in | cli, session |
| Consumers | `lib/config/load.ts#lowReadLeaseAuthority`<br>`lib/config/sdlc_yaml.ts#resolveAuthority`<br>`lib/services/lease/runtime.ts#lowReadLeaseAuthority`<br>`lib/services/lease/ops/_common.ts#lease_authority` |

### docs_site

Project-root-relative directory of the `sdlc`-generated docs site — the Astro/Starlight app whose content `sdlc docs generate site` produces. Defaults to `site`. Set this to relocate the site, e.g. `sites/docs` when it lives under a monorepo `sites/` tree. Every site path (content root `<dir>/src/content/docs`, the `<dir>/site.yaml` manifest, `<dir>/supplemental/`, the generated sidebar `<dir>/src/generated/sidebar.mjs`, and the `<dir>/scripts/list-ops.ts` helper) is resolved beneath this directory, so consumers can host the site wherever they like.

| Meta | Value |
|---|---|
| Shape | `string` |
| Default | `"site"` |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, hook |
| Consumers | `lib/config/load.ts#docsSiteDir`<br>`lib/services/docs/site.ts#docsSiteDir`<br>`lib/services/docs/site/manifest.ts#docsSiteDir`<br>`lib/services/docs/site/supplemental.ts#docsSiteDir`<br>`lib/services/docs/site/subsites.ts#docsSiteDir` |

### paths

Named directories other path settings refer to as `{name}`: `repos:` entries and `checkout.layout`. `dev: ~/Developer` lets `repos: ['{dev}']` and `layout: '{dev}/{owner}/{repo}'` say it once. `~` expands and a relative path resolves against the project root. The machine config's `paths:` carries the same map and wins per name, since a directory is a fact about a machine. `host`, `owner` and `repo` are the layout's own tokens and cannot be defined here. Entries do not refer to each other.

| Meta | Value |
|---|---|
| Shape | `map<string /^[A-Za-z][A-Za-z0-9_]*$/, string>` |
| Default | `{}` |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local, machine |
| Runs in | cli |
| Consumers | `lib/services/repo/locate.ts#pathTokens`<br>`lib/services/repo/layout.ts#pathTokens` |

### repos

Where this machine's repositories are. An entry that is itself a checkout names that repository; one that is not is searched for the checkouts under it, so `~/Developer` covers `~/Developer/dev` and `~/Developer/acme/api` alike — the search descends through directories that are not checkouts and stops at the ones that are. `sdlc pr review <url>` scans them for the checkout whose `origin` matches the URL's owner/repo, `sdlc pr survey --repo owner/name` for the checkout its drift is measured in, and `sdlc repo checkout` for a clone that is already on disk. Listing a directory here never puts what is under it into a verb's scope on its own: a verb that can fan out (`sdlc project cleanup --all`) does so only when asked, and every other verb stays in the repository you are standing in. An entry may use `{paths}` tokens; `~` expands and a relative path resolves against the project root, as for every other path value. The machine-level config (`<config-home>/sdlc/config.yaml`, key `repos`) carries the same list for paths that are specific to one machine and do not belong in a committed file; its entries are searched first.

| Meta | Value |
|---|---|
| Shape | `list<string>` |
| Default | `[]` |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local, machine |
| Runs in | cli |
| Consumers | `lib/services/repo/locate.ts#repos` |

### checkout

Settings for `sdlc repo checkout`, which clones a repository URL into the directory `layout` names. Missing block: `{dev}/{repo}` over https.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local, machine |
| Runs in | cli |
| Consumers | `lib/services/repo/layout.ts#checkout` |

Default:

```yaml
checkout:
  layout: '{dev}/{repo}'
  protocol: https
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `layout` | `string` | `"{dev}/{repo}"` | Where `sdlc repo checkout <url>` puts a checkout, as a path template. Tokens: `{host}` (`github.com`), `{owner}`, `{repo}`, and every `paths:` entry by name. The default `{dev}/{repo}` needs `paths.dev` and keeps checkouts directly under it; `{dev}/{owner}/{repo}` groups them by owner. Either works with `repos: ['{dev}']`, since the scan descends through directories that are not checkouts. A token nothing defines is an error, not an empty string. `~` expands and a relative result resolves against the project root. The machine config's `checkout.layout` overrides this one. |
| `protocol` | `'https' \| 'ssh'` | `"https"` | Clone URL to build when the input names a repository without saying how to reach it — a bare `owner/repo`, `github:owner/repo`. An explicit `https://…` or `git@…:` input is cloned the way it was given. The machine config's `checkout.protocol` overrides this one. |

### log

The sdlc log root: `sessions/` (one directory per agent session: an append-only `events.jsonl` and the `record.json` it reduces to), `runs/<run-id>/output.log` (a work run's redacted, size-capped output, published when the run ends) and `journal/<writer>/<yyyy-mm>.jsonl` (the project journal). `store.tracked` picks the root: `false` (default) is `.sdlc/log` in the repository's main checkout, shared by every worktree and gitignored; `true` is `sdlc/log` in the writing process's own project root, committed with the branch that wrote it (`sdlc.local.yaml` only). `store.path` names a root outside the project instead (an aggregate store, with `layout: by-project`, `sdlc.local.yaml` only). One root at a time: every reader and writer uses it, and sessions written to an earlier location are not read. Committing is opt-in; sdlc runs git there only when `sdlc log stage` (a pre-commit step) is wired in, and `sdlc log setup` writes the `.gitattributes` union-merge rule for the journal. `journal` (which writers append), `redact`, `capture` and the byte limits are acted on; `retention` is applied by `sdlc log prune`. Missing block: `.sdlc/log`, flat, untracked.

| Meta | Value |
|---|---|
| Shape | `object` |
| Default | see below |
| Enforcement | `read` (see [the enforcement table](sdlc-yaml-enforcement.md)) |
| Applies via | `read` |
| Layers | project, local |
| Runs in | cli, loop |
| Consumers | `lib/services/log/store.ts#logRoot`<br>`lib/services/log/store-git.ts#trackedStoreProblem`<br>`lib/services/session/hook-report.ts#reportHook`<br>`lib/services/log/digest.ts#digestSession`<br>`lib/services/project/ops/doctor.ts#probeLogStore` |

Default:

```yaml
log:
  store:
    tracked: false
    layout: flat
  capture:
    activity: true
    prompts: false
    messages: false
    tool_args: false
    tool_output: false
    originator: true
    raw_transcript: false
  journal:
    ops: true
    sessions: true
    workflow: true
  redact:
    extra_patterns: []
    env_names: []
  retention:
    activity_days: 90
    raw_days: 30
    journal_days: null
    runs_days: 90
  max_inline_bytes: 4096
  max_blob_bytes: 1048576
```

#### Fields

| Field | Shape | Default | Description |
|---|---|---|---|
| `store` | `object` | `{"tracked":false,"layout":"flat"}` | Where the log is stored. |
| `capture` | `object` | `{"activity":true,"prompts":false,"messages":false,"tool_args":false,"tool_output":false,"originator":true,"raw_transcript":false}` | What a session records beyond its lifecycle events. |
| `journal` | `object` | `{"ops":true,"sessions":true,"workflow":true}` | Which writers append to the journal. Setting the environment variable `SDLC_LOG_JOURNAL=off` stops a process writing it at all (the test harness sets it for spawned CLIs). |
| `redact` | `object` | `{"extra_patterns":[],"env_names":[]}` | Redaction applied to everything written to the log. |
| `retention` | `object` | `{"activity_days":90,"raw_days":30,"journal_days":null,"runs_days":90}` | How long the log keeps things. `record.json` and `events.jsonl` are never pruned. |
| `max_inline_bytes` | `integer > 0` | `4096` | Largest content value kept inline in an activity line; larger goes to `<session>/blobs/<sha256>.txt`. |
| `max_blob_bytes` | `integer > 0` | `1048576` | Largest blob written; larger content is cut at this size with a marker and flagged `truncated`. At least `max_inline_bytes`. |

##### `log.store`

Where the log is stored.

| Field | Shape | Default | Description |
|---|---|---|---|
| `tracked` | `boolean` | `false` | Whether the log is a committed part of the repository. `false` (default): the root is `.sdlc/log/` in the repository's main checkout (gitignored), so every git worktree shares one log and deleting a worktree loses nothing. `true`: the root is `sdlc/log/` in the project root of the process that writes (each worktree its own, and a session records in the worktree it works in), so the log travels with the branch that wrote it; it needs `sdlc log stage` as a pre-commit step and the `.gitattributes` union-merge rule (`sdlc log setup`), and `sdlc project doctor` fails when `sdlc/log` is gitignored. Every contributor's clone would commit their hostname, user name and redacted run output, so it is accepted only in `sdlc.local.yaml`. Cannot be combined with `path`. |
| `path` | `string` | — | A log root OUTSIDE the project (e.g. `~/agent-logs`, an absolute path, or a `paths:` `{name}` token), kept untracked. Needs `layout: by-project`, so two projects cannot mix ids in one directory, and is accepted only in `sdlc.local.yaml`. A relative path inside the project is not accepted: `tracked` picks between the two in-project roots. Not allowed with `tracked: true`. |
| `layout` | `'flat' \| 'by-project'` | `"flat"` | `flat`: `<root>/sessions/<id>/`. `by-project`: `<path>/<owner>/<repo>/sessions/<id>/`, keyed by the checkout's `origin` remote and needing an external `path`; a checkout with no readable `origin` is an error, not a guess. An aggregate repo collecting logs across projects uses `by-project`. |

##### `log.capture`

What a session records beyond its lifecycle events.

| Field | Shape | Default | Description |
|---|---|---|---|
| `activity` | `boolean` | `true` | Record per-session activity metadata in `<session>/activity.jsonl`: turns, tool name and target, duration, subagents, compaction, permission waits. Off, no activity is written and the content switches below have nothing to attach to. Lifecycle events are always recorded. |
| `prompts` | `boolean` | `false` | Record prompt text in the activity log (redacted; large text goes to a blob), and keep a launch prompt's text in the session's `launched` event. Off, the launch event stores no prompt text. Needs `activity` for the activity log. |
| `messages` | `boolean` | `false` | Record assistant message text in the activity log: each turn's last message, a subagent's last message and permission notices (redacted; large text goes to a blob). Also lets a headless run keep its output in `log.txt` (with `tool_output`: either one). Needs `activity` for the activity log. |
| `tool_args` | `boolean` | `false` | Record tool-call arguments in the activity log (redacted; large text goes to a blob). Needs `activity`. |
| `tool_output` | `boolean` | `false` | Record tool output, and the text of a tool failure, in the activity log (redacted; large text goes to a blob). Needs `activity`. |
| `originator` | `boolean` | `true` | Record who started each session or run and from where: the git `user.name` in the launch directory (else the OS login) as `startedBy` / `started_by`, and the machine's `os.hostname()` as `hostname`, on the `launched` event, `record.json` and the journal's `run.start`. Real names and hostnames: a team that commits the store publishes them. Off, `startedBy` is `null` and `hostname` is left out; the time zone and the derived machine id (a hash, not the hostname) are still recorded. |
| `raw_transcript` | `boolean` | `false` | When a session ends, write a redacted copy of the harness's own transcript (and Claude's subagent transcripts) to `<session>/raw/`, every string value redacted, up to 64 MiB per file. Transcripts hold every prompt and tool output, so `sdlc project doctor` warns when this is on together with a store path outside the project. |

##### `log.journal`

Which writers append to the journal. Setting the environment variable `SDLC_LOG_JOURNAL=off` stops a process writing it at all (the test harness sets it for spawned CLIs).

| Field | Shape | Default | Description |
|---|---|---|---|
| `ops` | `boolean` | `true` | Journal `op.start`/`op.end` for every top-level `sdlc` command. |
| `sessions` | `boolean` | `true` | Journal `session.started`, `session.state_changed` and `session.ended`. |
| `workflow` | `boolean` | `true` | Journal `run.start`/`run.end`, `step.*`, `dispatch.decision` and `friction.recorded`. (`pr.merged`, the record of an automated merge, is always journaled and has no switch.) |

##### `log.redact`

Redaction applied to everything written to the log.

| Field | Shape | Default | Description |
|---|---|---|---|
| `extra_patterns` | `list<object>` | `[]` | Patterns added to the built-in secret filter, never replacing it. Applied to every string written to the log. |
| `env_names` | `list<string>` | `[]` | More environment variable names whose values are always redacted. Applied to every string written to the log. |

##### `log.retention`

How long the log keeps things. `record.json` and `events.jsonl` are never pruned.

| Field | Shape | Default | Description |
|---|---|---|---|
| `activity_days` | `integer > 0 \| null` | `90` | Age after which activity logs are pruned. Positive days, or null to keep forever. Applied by `sdlc log prune`. |
| `raw_days` | `integer > 0 \| null` | `30` | Age after which raw transcript copies are pruned. Positive days, or null to keep forever. Applied by `sdlc log prune`. |
| `journal_days` | `integer > 0 \| null` | `null` | Age after which journal files are pruned. Positive days, or null to keep forever. Applied by `sdlc log prune`. |
| `runs_days` | `integer > 0 \| null` | `90` | Age after which a work run's published output (`runs/<run-id>/`) is pruned; the journal events stay. Positive days, or null to keep forever. Applied by `sdlc log prune`. |
