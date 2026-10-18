---
type: guide
title: Contract inventory
---

# Contract inventory

> **Generated** from the code by `sdlc docs generate contract`. **Do not hand-edit.**
> The committed snapshot is [contract-inventory.jsonl](contract-inventory.jsonl).

The surfaces the [compatibility promise](compatibility.md) covers. Everything under a
stable heading is held to semantic versioning within 1.x; experimental items are listed
last and are excluded from the promise. An item is stable unless its key meta or op
descriptor, or an entity type's registry entry, says `stability: experimental`.

## `sdlc.yaml` keys

| Key | Type | Default |
|---|---|---|
| `workflows` | `map<string, list<object> \| object>` | `{}` |
| `profiles` | `map<string, list<string> \| object>` | `null` |
| `routing` | `list<object>` | `null` |
| `orchestrator` | `object` | see [reference](sdlc-yaml-reference.md) |
| `pr_check` | `object` | `{"author_comments":"actionable","ignored_authors":[]}` |
| `pr_update` | `object` | see [reference](sdlc-yaml-reference.md) |
| `tracker` | `object` | `{"kind":"github"}` |
| `code_host` | `object` | `{"override":null}` |
| `task` | `object` | see [reference](sdlc-yaml-reference.md) |
| `backlog` | `object` | see [reference](sdlc-yaml-reference.md) |
| `lease_authority` | `string` | `null` |
| `docs_site` | `string` | `"site"` |
| `paths` | `map<string /^[A-Za-z][A-Za-z0-9_]*$/, string>` | `{}` |
| `repos` | `list<string>` | `[]` |
| `checkout` | `object` | `{"layout":"{dev}/{repo}","protocol":"https"}` |
| `host` | `object` | `{"default":"terminal","close_pane":"on-close-out"}` |
| `log` | `object` | see [reference](sdlc-yaml-reference.md) |
| `pr_review` | `object` | see [reference](sdlc-yaml-reference.md) |
| `verify` | `object` | see [reference](sdlc-yaml-reference.md) |
| `lib` | `object` | `null` |
| `chain` | `object` | `null` |
| `hooks` | `map<'pre-commit' \| 'pre-merge-commit' \| 'prepare-commit-msg' \| 'commit-msg' \| 'post-commit' \| 'pre-rebase' \| 'post-checkout' \| 'post-merge' \| 'pre-push' \| 'post-rewrite', list<object> \| object>` | `null` |
| `notify` | `object` | `null` |
| `mcp` | `object` | `null` |
| `knowledge` | `object (no fields)` | `null` |
| `issues` | `object (no fields)` | `null` |

## Commands

| Command | Summary |
|---|---|
| `sdlc apply run` | Reconcile this repo's state with sdlc.yaml (hook shims, harness install, …). |
| `sdlc backlog create` | Author a new backlog item with a minted B-NNNN identity. |
| `sdlc backlog get` | Get one backlog by id or basename: frontmatter, title, path and body. |
| `sdlc backlog list` | List backlog entities, filtered, sorted and paged. |
| `sdlc backlog preview-id` | Preview the slug + id 'create' would assign for a backlog (read-only; reports collisions). |
| `sdlc backlog related` | Entities of any kind that a backlog links to or that link to it. |
| `sdlc backlog search` | Search backlog entities by words in the id, title, frontmatter and body. |
| `sdlc backlog update` | Apply JSON frontmatter updates to a backlog doc, schema-validated before write. |
| `sdlc capability coverage` | Join capabilities against packages and crates: unanchored units, unanchored capabilities, rot. |
| `sdlc capability create` | Author a new capability with a minted C-NNNN identity. |
| `sdlc capability get` | Get one capability by id or basename: frontmatter, title, path and body. |
| `sdlc capability graph` | The capability corpus as one graph: parent_key containment, related edges, ghosts. |
| `sdlc capability list` | List capability entities, filtered, sorted and paged. |
| `sdlc capability preview-id` | Preview the slug + id 'create' would assign for a capability (read-only; reports collisions). |
| `sdlc capability related` | Entities of any kind that a capability links to or that link to it. |
| `sdlc capability relations` | The milestones that list a capability, their open tasks, and its direct children. |
| `sdlc capability search` | Search capability entities by words in the id, title, frontmatter and body. |
| `sdlc capability update` | Apply JSON frontmatter updates to a capability doc, schema-validated before write. |
| `sdlc commit create` | Commit with a message routed through a tempfile — raw, structured, or kind-rendered. |
| `sdlc commit get-schema` | Emit a commit kind's payload contract as JSON Schema (or list kinds). |
| `sdlc config explain` | Explain one sdlc.yaml key: description, meta, value shape, default, effective source. |
| `sdlc config get` | Print a key’s effective sdlc.yaml value (YAML; --json), or one layer’s with --layer. |
| `sdlc config get-backlog-policy` | Read the resolved backlog.capture policy from sdlc.yaml (JSON). |
| `sdlc config get-spawn-policy` | Read the resolved task.execution.spawn_from_post_mortem policy from sdlc.yaml (JSON). |
| `sdlc config get-verbs` | Read one workflows.<name> list from a project's sdlc.yaml (JSON). |
| `sdlc config init` | Write a starter sdlc.yaml: detected check/setup verbs plus commented sections. |
| `sdlc config list` | List every sdlc.yaml key by category: effective source, value summary, layers. |
| `sdlc config migrate-machine` | Upgrade the machine config file to the current version (keeps a .bak). |
| `sdlc config pin-test` | Detect the project's test command, confirm it, and record it as workflows.test. |
| `sdlc config scaffold` | Print a schema-driven sdlc.yaml, or --local for a commented sdlc.local.yaml template. |
| `sdlc config set` | Set a dotted config key (validated first; comments and layout preserved). |
| `sdlc config set-verbs` | Write a workflows.<name> list to a project's sdlc.yaml (idempotent). |
| `sdlc config unset` | Remove a dotted config key so it falls back to the next layer or its default. |
| `sdlc config validate` | Check sdlc.yaml (or one --file) and print each problem with its key path; exit 14 on any. |
| `sdlc conventions show` | Print the guidance for one agent-pants build convention (assets, excludes, shim). |
| `sdlc decision get` | Get one decision by id or basename: frontmatter, title, path and body. |
| `sdlc decision list` | List decision entities, filtered, sorted and paged. |
| `sdlc decision related` | Entities of any kind that a decision links to or that link to it. |
| `sdlc decision search` | Search decision entities by words in the id, title, frontmatter and body. |
| `sdlc dev history` | List every checkout ever linked, most recent first (`dev use #n` picks one). |
| `sdlc dev install` | Put a shim for this checkout’s launcher onto PATH as `sdlc` (idempotent). |
| `sdlc dev link` | Make a source checkout the one every sdlc on this machine runs (recorded). |
| `sdlc dev off` | Clear the link; revert sdlc to the local/published launcher. |
| `sdlc dev status` | Report which source (env / cwd checkout / link / local) the launcher will run. |
| `sdlc dev use` | Switch sdlc to a history entry (#n), a checkout path, or the canonical install. |
| `sdlc docs build-subsites` | Build the nested child sites declared in site.yaml (subsites:) into the parent docs-site dist, each under /<slug>/. |
| `sdlc docs generate` | Regenerate the generated-docs artifacts (docs/index.md, docs/glossary.md, docs/references.md from docs/planning/, docs/sdlc-yaml-reference.md from the config schema, docs/schema/sdlc.schema.json, site reference pages). |
| `sdlc driver get` | Get one driver by id or basename: frontmatter, title, path and body. |
| `sdlc driver list` | List driver entities, filtered, sorted and paged. |
| `sdlc driver related` | Entities of any kind that a driver links to or that link to it. |
| `sdlc driver search` | Search driver entities by words in the id, title, frontmatter and body. |
| `sdlc duplication-scan apply-verdicts` | Apply Arbiter verdicts and dispositions; report convergence. |
| `sdlc duplication-scan init` | Create the scan ledger and its rounds/ and data/ directories. |
| `sdlc duplication-scan merge-findings` | Assign F-nnnn ids to Prospector findings, with folds. |
| `sdlc duplication-scan merge-objections` | Assign O-nnnn ids to Adversary objections; record survived reports. |
| `sdlc duplication-scan report-tables` | Print the markdown tables report.md starts from. |
| `sdlc duplication-scan status` | Report a round's convergence checklist. |
| `sdlc entities audit` | Audit the docs/planning corpus against entity schemas (read-only report). |
| `sdlc entities check-identifiers` | Report entity instances whose filename/id breaks the AA-NNNN[-slug] shape. |
| `sdlc entities get-schema` | Emit registered entity types' frontmatter contracts as JSON Schema. |
| `sdlc entities migrate` | Apply mechanical, auto-fixable schema drift to entity instances. |
| `sdlc entities validate` | Validate frontmatter against entity schemas, or .base view files. |
| `sdlc gate comment-only` | Every source line a diff removes must be a comment. |
| `sdlc gate corpus-invocation` | Fail when the skill corpus references the old cli/sdlc.ts entry, a `bun run …cli/sdlc` shape, or `${CLAUDE_PLUGIN_ROOT}` without a following slash. |
| `sdlc gate markdown-fixtures` | Gate the pinned rumdl version against the D-0011 markdown stress fixtures. |
| `sdlc gate skill-prose` | Lint skill vault notes against their per-skill markdown-contract overlay. |
| `sdlc gate worktree-scope` | Reject commits on main whose staged paths collide with an active worktree. |
| `sdlc harness detect` | Report installed AI tools, local model runtimes and session hosts; optionally write host.default. |
| `sdlc harness install claude` | Wire the SDLC Claude-Code harness into a consumer project (idempotent). |
| `sdlc harness model` | Derive the target-agnostic harness model from the running plugin surface. |
| `sdlc hooks run` | Run the hooks: steps configured for one git hook (the entrypoint a hook shim calls). |
| `sdlc issues sync` | Report where a linked issue's sdlc:stage/<x> label disagrees with its task's state (report-only, no writes). |
| `sdlc journal append` | Append a free event (`note` or `x.<name>`) to the project journal. |
| `sdlc journal tail` | Show the newest project journal events. |
| `sdlc lease heartbeat` | Bump expires_at on an active lease (single CAS round-trip). |
| `sdlc lease inspect` | Fetch + render a single lease ref. |
| `sdlc lease list` | Enumerate refs under a prefix. |
| `sdlc lease migrate` | One-shot cutover migration. |
| `sdlc lease op claim` | Claim a fresh operation lease (CAS-CREATE). |
| `sdlc lease parse-footer` | Extract the lease footer from a PR body. |
| `sdlc lease reconcile` | Reconcile the frontmatter status cache against the lease authority. |
| `sdlc lease release` | Atomic archive-then-delete for completed leases. |
| `sdlc lease task acquire` | Acquire-or-inherit the task-lifecycle lease. |
| `sdlc lease task archive` | Idempotent terminal archive step. |
| `sdlc lease task claim` | Claim a fresh task-lifecycle lease (CAS-CREATE). |
| `sdlc lease task fence` | PR ↔ lease binding check: discovered lease_id == expected lease_id. |
| `sdlc lease task footer` | Print the PR-body (or review) lease footer for a task lease. |
| `sdlc lease task reacquire` | Re-acquire + phase transition (CAS-REPLACE-with-steal-on-expired). |
| `sdlc lease task release` | Release a task lease before it expires; refuses while the holder looks alive. |
| `sdlc lease task sweep` | Bulk-archive leases whose task state starts closed/ (reconcile the tasks namespace). |
| `sdlc lease task transition` | CAS-REPLACE phase rotation (caller-driven, no steal). |
| `sdlc lease task update-gates` | CAS-REPLACE the lease payload's per-run gate stamps. |
| `sdlc log hooks install` | Append sdlc's hook entries to the harness's user hook config (codex: ~/.codex/hooks.json, cursor: ~/.cursor/hooks.json). |
| `sdlc log hooks status` | Show whether sdlc's hook entries are installed and approved in the harness. |
| `sdlc log hooks uninstall` | Remove sdlc's hook entries from the harness's user hook config; other entries stay. |
| `sdlc log prune` | Delete log content older than log.retention allows. |
| `sdlc log search` | Search the journal and session logs, newest first. |
| `sdlc log setup` | Add the .gitattributes merge rule a committed log store needs. |
| `sdlc log stage` | Stage the committed log store so the next commit carries it. |
| `sdlc markdown check` | Check markdown with the pinned rumdl, writing nothing. |
| `sdlc markdown fmt` | Format markdown in place with the pinned rumdl. |
| `sdlc markdown verify-reflow` | Verify a reflow changed only prose wrapping, comparing against a git ref. |
| `sdlc mcp register` | Compute (or write) the per-harness MCP client registration stanza pointing at 'sdlc mcp start'. |
| `sdlc milestone create` | Author a new milestone with a system-assigned M-NNNN id. |
| `sdlc milestone get` | Get one milestone by id or basename: frontmatter, title, path and body. |
| `sdlc milestone list` | List milestone entities, filtered, sorted and paged. |
| `sdlc milestone related` | Entities of any kind that a milestone links to or that link to it. |
| `sdlc milestone search` | Search milestone entities by words in the id, title, frontmatter and body. |
| `sdlc note create` | Author a new freeform note with a minted N-XXXX identity. |
| `sdlc note get` | Get one note by id or basename: frontmatter, title, path and body. |
| `sdlc note list` | List note entities, filtered, sorted and paged. |
| `sdlc note preview-id` | Preview the slug + id 'create' would assign for a note (read-only; reports collisions). |
| `sdlc note related` | Entities of any kind that a note links to or that link to it. |
| `sdlc note search` | Search note entities by words in the id, title, frontmatter and body. |
| `sdlc notify send` | Send a manual test message to one named notify.channels entry. |
| `sdlc notify test` | Send a probe message to every configured notify.channels entry. |
| `sdlc orchestrate base-health skip` | Let the work tick dispatch although the base is red, for the current sha of the target branch. |
| `sdlc orchestrate log-tail` | Read the last N lines of .sdlc/orchestrator-log.md. |
| `sdlc orchestrate log-tick` | Compose and append one orchestrate tick digest line to .sdlc/orchestrator-log.md. |
| `sdlc orchestrate pause` | Pause the orchestrator (all loops, or specific --loop names) until resumed. |
| `sdlc orchestrate prs` | List the open PRs the loop handles: verdict, review rounds, and merge gate refusals. |
| `sdlc orchestrate resume` | Resume the orchestrator (all loops, or specific --loop names). |
| `sdlc orchestrate run` | Deterministic orchestrate tick: dispatch ready tasks, classify + act on open PRs, walk chain-labeled issues, start due schedules, per --loop work,prs,merges,issues,schedule. |
| `sdlc orchestrate run-info` | One task's work-run: pid, state, outcome, failure detail and log tail. |
| `sdlc orchestrate start` | Start the named orchestrator loops as detached processes (one per loop). |
| `sdlc orchestrate status` | Report the orchestrator's pause state, per loop, and its base health. |
| `sdlc orchestrate stop` | Stop the named orchestrator loops (SIGTERM to each loop's pid only). |
| `sdlc orchestrate target` | Print the branch task work builds against, verified on the remote. |
| `sdlc orchestrate watch` | Deterministic pending-work gate: emit one line IFF /sdlc:orchestrate has actionable work. |
| `sdlc orchestrate work-run` | Internal: run one task's dispatch sequence for the work tick, as the detached process it started. |
| `sdlc orchestrate work-start` | Start a task's dispatch sequence as a detached work-run. |
| `sdlc orchestrate work-stop` | Ask a task's running work-run to stop (SIGTERM to its pid only). |
| `sdlc plugin info` | Report the runtime state of the loaded sdlc plugin (root, version, git, registrations). |
| `sdlc plugin install-permissions` | Seed the project-level Claude Code allowlist for SDLC tooling. |
| `sdlc plugin resolve` | Resolve a plugin name to its repo root and origin URL. |
| `sdlc pr classify` | Classify a pull request into a verdict enum and update its cursor. |
| `sdlc pr create` | Open a PR from a branch, with its body read from a file. |
| `sdlc pr detail` | One PR's head, base, checks, verdict, merge gate refusals and review threads. |
| `sdlc pr merge` | Merge a reviewed task PR into its allowed target once every merge condition holds. |
| `sdlc pr ready` | Mark a draft PR ready for review, only when its current head has a passing recorded check. |
| `sdlc pr record-check` | Record the local check outcome for the checked-out commit of a PR, for `pr ready`. |
| `sdlc pr review` | Open a pull request in a local worktree and start an interactive review session there. |
| `sdlc pr review-verdict` | Record the pr-review verdict for a PR head in the evidence ledger, for the merge gate. |
| `sdlc pr route` | Manually deliver a PR's current feedback bundle into the session that produced it — the same live/resume/fresh Router chain the orchestrator's prs tick uses. Works even while `sdlc orchestrate pause` is active. |
| `sdlc pr stats` | Line, category, API and complexity statistics for a PR or branch. |
| `sdlc pr survey` | Report every open PR's drift from its base, in stack order. |
| `sdlc pr update` | Bring out-of-date PRs up to date, in stack order. |
| `sdlc principle get` | Get one principle by id or basename: frontmatter, title, path and body. |
| `sdlc principle list` | List principle entities, filtered, sorted and paged. |
| `sdlc principle related` | Entities of any kind that a principle links to or that link to it. |
| `sdlc principle search` | Search principle entities by words in the id, title, frontmatter and body. |
| `sdlc product get` | Get one product by id or basename: frontmatter, title, path and body. |
| `sdlc product list` | List product entities, filtered, sorted and paged. |
| `sdlc product related` | Entities of any kind that a product links to or that link to it. |
| `sdlc product search` | Search product entities by words in the id, title, frontmatter and body. |
| `sdlc project apply-cleanup` | Apply approved rows of a cleanup plan: re-check, delete compare-and-swap, verify, write a receipt. |
| `sdlc project cleanup` | Gather and classify branches, worktrees, remote branches, and task linkage. Fetches first; otherwise read-only apart from an optional plan file. |
| `sdlc project create-worktree` | Create the `.sdlc/worktrees/<basename>` task worktree under the main checkout on a new branch; prints its absolute path. |
| `sdlc project detect-setup` | Probe a project root for setup bootstrap commands (deps install, git hooks, tool trust). |
| `sdlc project doctor` | Probe runtime dependencies (git, gh, bun/node), the AI tools installed (as `harness detect` reports them), code host auth and the harness install. |
| `sdlc project follow` | Fast-forward a detached, clean checkout to origin/main when safe; --status shows the last outcome. |
| `sdlc project migrate-runtime-state` | Move a project's legacy .claude runtime state to its .sdlc home. |
| `sdlc project preflight-worktree` | Probe dirty-tree + worktree/branch collisions + open PR before scaffolding a worktree. Report-only. |
| `sdlc project scan candidates` | Walk a project root and emit planning-shaped candidate files for /sdlc:import-planning. |
| `sdlc project scan comments` | Nominate comment blocks for a documentation sweep, sharded one owner per file. |
| `sdlc project scan todos` | Find TODO / FIXME / HACK / XXX comments in tracked source files. |
| `sdlc project setup` | Bootstrap docs/planning/, sdlc.yaml, .gitignore, .sdlc/ and markdown rules. |
| `sdlc project teardown-worktree` | Remove a `.sdlc/worktrees/<basename>` worktree and optionally delete its branch (cancel path). |
| `sdlc quality baseline capture` | Capture a quality-check baseline (run verbs and write <baseline-dir>/<sha>.json). |
| `sdlc quality baseline diff` | Diff current verb results against a captured baseline; surface new-drift vs pre-existing findings. |
| `sdlc quality detect` | Probe a project root for the tasks it can run (just, npm, make, cargo, python). |
| `sdlc quality run` | Run quality checks from the project sdlc.yaml (or --config, or a verb list); stream per-check output in --mode log. |
| `sdlc reference create` | Author a new external-source reference with a minted RF-NNNN identity. |
| `sdlc reference get` | Get one reference by id or basename: frontmatter, title, path and body. |
| `sdlc reference list` | List reference entities, filtered, sorted and paged. |
| `sdlc reference preview-id` | Preview the slug + id 'create' would assign for a reference (read-only; reports collisions). |
| `sdlc reference related` | Entities of any kind that a reference links to or that link to it. |
| `sdlc reference search` | Search reference entities by words in the id, title, frontmatter and body. |
| `sdlc repo checkout` | Clone a repository URL into the directory the checkout layout names, or find its existing checkout. |
| `sdlc repo trust` | Grant, or report, repo-level consent to run project-sourced verbs. |
| `sdlc repo untrust` | Revoke repo-level consent to run project-sourced verbs. |
| `sdlc roadmap check` | Validate a roadmap (or every roadmap) against the milestone/task corpus it links to. |
| `sdlc roadmap create` | Author a new roadmap with a system-assigned RM-NNNN id. |
| `sdlc roadmap get` | Get one roadmap by id or basename: frontmatter, title, path and body. |
| `sdlc roadmap list` | List roadmap entities, filtered, sorted and paged. |
| `sdlc roadmap progress` | Report a roadmap's (or every roadmap's) linked milestones and their member tasks' state. |
| `sdlc roadmap related` | Entities of any kind that a roadmap links to or that link to it. |
| `sdlc roadmap search` | Search roadmap entities by words in the id, title, frontmatter and body. |
| `sdlc session adopt` | Track the coding-agent session you are in even though sdlc did not start it. Hooks cannot be added to a running session, so it stays launch-only. |
| `sdlc session attach` | Reopen a launched session, or print how to when there is nothing to attach to. |
| `sdlc session digest` | Write an ended session's transcript activity and redacted raw copy into its log. |
| `sdlc session done` | Declare the current session done (ok or failed); a new prompt reopens it. |
| `sdlc session focus` | Bring a task's, PR's or branch's live session forward in its host. |
| `sdlc session hook` | Record a coding-agent harness's hook payload (stdin) in the session log; exits 0 always. |
| `sdlc session launch` | Start one coding-agent session — interactive or headless — from a workflow, a skill, or a literal prompt. |
| `sdlc session list` | List session records — headless and interactive, across every host. |
| `sdlc session reconcile` | End sessions whose process and pane are gone (outcome lost); optionally backfill digests. |
| `sdlc session rename` | Name a session: the name `session list`, `session show` and the log explorer show. |
| `sdlc session run` | Run a harness command as a recorded session (its pid, real exit and usage); what a hands-off host's launch runs. |
| `sdlc session show` | Show a session record and its merged lifecycle and activity timeline. |
| `sdlc setup apply` | Apply the adoption wizard steps on branch sdlc/setup and open the PR (additive; honours --dry-run, --no-pr). |
| `sdlc setup plan` | Plan the adoption wizard steps: the file changes and notes, without writing. |
| `sdlc shell completion` | Print the shell completion script for sdlc, or install it with --install. |
| `sdlc shell init` | Print shell functions (sdlc-cd, sdlc-at, sdlc-pr-review, sdlc-repo-checkout) for an rc file to evaluate. |
| `sdlc site build` | Build the docs site: materialize the Astro scaffold, regenerate content, install deps (bun/npm), run astro build, and report the dist path. |
| `sdlc standard create` | Author a new standard with a minted S-NNNN identity. |
| `sdlc standard get` | Get one standard by id or basename: frontmatter, title, path and body. |
| `sdlc standard list` | List standard entities, filtered, sorted and paged. |
| `sdlc standard preview-id` | Preview the slug + id 'create' would assign for a standard (read-only; reports collisions). |
| `sdlc standard related` | Entities of any kind that a standard links to or that link to it. |
| `sdlc standard search` | Search standard entities by words in the id, title, frontmatter and body. |
| `sdlc standard supersede` | Mark a standard closed/superseded, pointing superseded_by at a successor. |
| `sdlc standard update` | Apply JSON frontmatter updates to a standard doc, schema-validated before write. |
| `sdlc task begin` | Start a task's lease and reset its worktree onto the target branch tip. |
| `sdlc task check-ancestry` | Classify a feature branch's ancestry against origin/<target> (default origin/main). |
| `sdlc task check-claims` | Run every claim resolver against a task doc; findings keyed by resolver. |
| `sdlc task close-commit` | Commit a task's closing state to the target branch (default main) via an ephemeral worktree off origin/<target> (no shared-checkout contamination). |
| `sdlc task create` | Author a new task with a minted T-NNNN identity. |
| `sdlc task dedup-search` | Score a post-mortem bullet against unfinished tasks and render the search trail. |
| `sdlc task dispatch` | Run the define/implement/check/judge dispatch sequence for one task on demand — the same per-task lease/sequence a work tick uses, callable manually even while `sdlc orchestrate pause` is active. |
| `sdlc task ensure-ready-mutate` | Apply the readiness gate's verdict, on whichever plane owns the fact. |
| `sdlc task gap-report` | Deterministic readiness-gap composite for a task doc (sections + placeholders + touchpoints + claims + parenthood); no LLM. |
| `sdlc task get` | Get one task by id or basename: frontmatter, title, path and body. |
| `sdlc task inflight` | Categorise in-flight tasks (implementing / awaiting-review / stale / other). |
| `sdlc task lint-state-origin` | Enforce task-state frontmatter commits only on main, not task branches. |
| `sdlc task list` | List task entities, filtered, sorted and paged. |
| `sdlc task list-unmerged` | Task files present on an open PR head ref but not on the base branch. |
| `sdlc task next` | Select the next dispatchable tasks: pickup-order sort (milestone release order > priority > impact > complexity > created > basename, with dependency lift), keeping only leaves (a task with children is a rollup) of kind implementation (absent kind counts as implementation), and filtering out tasks whose own or milestone depends_on is unsatisfied (use --include-blocked to keep them). --milestone / --until-version cut the frontier to a goal scope. |
| `sdlc task parse-touchpoints` | Parse the `## Areas` table out of a task doc. |
| `sdlc task preflight-permissions` | Report the Claude Code permissions a task's implementer will be missing. |
| `sdlc task preview-id` | Preview the slug + id 'create' would assign for a task (read-only; reports collisions). |
| `sdlc task probe-state` | Report single-task pre-flight signals (worktree/branch/PR/state + resume detector) for /sdlc:task-work. |
| `sdlc task related` | Entities of any kind that a task links to or that link to it. |
| `sdlc task resolve` | Resolve an arg naming a task (absolute or relative path / filename / slug) to its task file, searching every planning root in the repo (deterministic, per file-resolution.md). |
| `sdlc task retry` | Lift the work loop's hold on a task whose runs kept failing, so the next tick retries it. |
| `sdlc task scan-corpus-assumptions` | Emit candidate uniform-corpus assumptions from a task body, one JSON line each. |
| `sdlc task scan-placeholders` | Scan a task doc's spec-bearing sections for unresolved spec-drift placeholders. |
| `sdlc task scan-validate-ac` | Report which files a task's acceptance criteria ask to make validate, and why they fail. |
| `sdlc task search` | Search task entities by words in the id, title, frontmatter and body. |
| `sdlc task start` | Start a task yourself: lease it as a person, open its worktree and a task-work session, focus it. |
| `sdlc task summarize-dedup-telemetry` | Bucket a dedup-search telemetry log by top-candidate score and print the histogram. |
| `sdlc task update` | Apply JSON frontmatter updates to a task doc, schema-validated before write. |
| `sdlc term create` | Author a new glossary term with a minted TM-NNNN identity. |
| `sdlc term get` | Get one term by id or basename: frontmatter, title, path and body. |
| `sdlc term list` | List term entities, filtered, sorted and paged. |
| `sdlc term preview-id` | Preview the slug + id 'create' would assign for a term (read-only; reports collisions). |
| `sdlc term related` | Entities of any kind that a term links to or that link to it. |
| `sdlc term search` | Search term entities by words in the id, title, frontmatter and body. |
| `sdlc verify changes` | Judge a changeset against every instruction file and standard that governs it. |
| `sdlc workflow list` | List every known workflow name (built-in and project-defined). |
| `sdlc workflow run` | Run a workflow’s resolved steps in order (verbs trust-gated); --dry-run prints the plan. |
| `sdlc workflow show` | Print a workflow's resolved step list, each step tagged with its provenance. |

## Exit codes

| Name | Code |
|---|---|
| `GENERIC` | 1 |
| `UNKNOWN_OP` | 2 |
| `INVALID_INPUT` | 1 |
| `INVALID_OUTPUT` | 1 |
| `CAS_FAILED` | 10 |
| `LEASE_EXPIRED` | 11 |
| `LEASE_CONFLICT` | 12 |
| `REF_NOT_FOUND` | 13 |
| `SCHEMA_ERROR` | 14 |
| `NAMESPACE_CONFLICT` | 15 |
| `MODEL_ERROR` | 16 |
| `SERVICE_ERROR` | 17 |
| `DIRTY_WORKTREE` | 18 |
| `UNTRUSTED` | 21 |
| `APPLY_STEP_FAILED` | 22 |
| `UNMERGED_COMMITS` | 23 |
| `LEASE_HOLDER_ALIVE` | 24 |
| `IMPLEMENT_NO_WORKTREE` | 25 |
| `RELEASE_PARTIAL` | 26 |
| `NO_PASSING_CHECK` | 27 |
| `CHECKED_HEAD_MISMATCH` | 28 |
| `HOST_UNSUPPORTED` | 29 |
| `LOOP_ALREADY_RUNNING` | 30 |
| `NOT_RUNNING` | 31 |
| `WORK_RUN_ALREADY_RUNNING` | 32 |
| `LOG_STORE_IGNORED` | 33 |
| `HOST_CHECKS_FAILED` | 34 |
| `SETUP_BRANCH_UNPUSHED` | 35 |
| `REFLOW_CHANGED` | 36 |
| `SCHEDULE_ENTRY_UNKNOWN` | 37 |
| `SCHEDULE_POOL_FULL` | 38 |
| `SCHEDULE_SLOT_INVALID` | 39 |
| `SCHEDULE_RUN_UNKNOWN` | 40 |
| `BASE_REF_NOT_FOUND` | 41 |
| `VERDICT_MISSING` | 42 |
| `VERDICT_BLOCKING` | 43 |
| `SCHEDULE_ENTRY_EXISTS` | 44 |
| `SCHEDULE_STALE_REVISION` | 45 |
| `SCHEDULE_ENTRY_RUNNING` | 46 |
| `SCHEDULE_RUN_EXISTS` | 47 |
| `SCHEDULE_PROMOTE_EXISTS` | 48 |
| `LEASE_AUTHORITY_UNSET` | 49 |

## `--output json` shapes

Top-level fields of each command output (`?` marks an optional field). The full JSON
Schemas are in the [snapshot](contract-inventory.jsonl).

| Command | Fields |
|---|---|
| `sdlc apply run` | `mode`, `plans`, `warnings` |
| `sdlc backlog create` | `path`, `id` |
| `sdlc backlog get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc backlog list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc backlog preview-id` | `entity`, `input`, `slug`, `id`, `exists`, `exact_match`, `similar`, `warning` |
| `sdlc backlog related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc backlog search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc backlog update` | `path`, `changed`, `wrote` |
| `sdlc capability coverage` | `root`, `root_label`, `scope`, `units`, `unanchored_units`, `capabilities`, `unanchored_capabilities`, `anchor_rot`, `warnings`, `scanned` |
| `sdlc capability create` | `path`, `id` |
| `sdlc capability get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc capability graph` | `root`, `root_label`, `nodes`, `edges`, `warnings`, `scanned` |
| `sdlc capability list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc capability preview-id` | `entity`, `input`, `slug`, `id`, `exists`, `exact_match`, `similar`, `warning` |
| `sdlc capability related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc capability relations` | `capability`, `contains`, `milestones` |
| `sdlc capability search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc capability update` | `path`, `changed`, `wrote` |
| `sdlc commit create` | `subject`, `sha`, `wrote` |
| `sdlc commit get-schema` | `kinds`, `jsonSchema?` |
| `sdlc config explain` | `key`, `section`, `description`, `meta`, `shape`, `schema`, `default`, `fields`, `source`, `files`, `value`, `ignored`, `lifecycle` |
| `sdlc config get` | `key`, `layer`, `found`, `value`, `source`, `ignored`, `supportedLayers` |
| `sdlc config get-backlog-policy` | `enabled`, `pr_grouping`, `pr_title_pattern` |
| `sdlc config get-spawn-policy` | `enabled`, `drive_to_ready`, `fallback_status`, `pr_grouping`, `pr_title_pattern` |
| `sdlc config get-verbs` | `verbs`, `fileExists`, `path` |
| `sdlc config init` | `path`, `wrote`, `overwrote`, `verbs`, `test`, `yaml`, `dryRun` |
| `sdlc config list` | `keys` |
| `sdlc config migrate-machine` | `path`, `from`, `to`, `applied`, `written`, `dryRun` |
| `sdlc config pin-test` | `command`, `written`, `reason`, `path` |
| `sdlc config scaffold` | `yaml` |
| `sdlc config set` | `key`, `file`, `layer`, `created`, `changed`, `before`, `after`, `applier`, `dryRun` |
| `sdlc config set-verbs` | `verbs`, `count`, `unchanged`, `created`, `path` |
| `sdlc config unset` | `key`, `file`, `layer`, `created`, `changed`, `before`, `after`, `applier`, `dryRun` |
| `sdlc config validate` | `valid`, `target` |
| `sdlc conventions show` | `name`, `text` |
| `sdlc decision get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc decision list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc decision related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc decision search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc dev history` | `entries` |
| `sdlc dev install` | `binDir`, `linkPath`, `target`, `status`, `onPath`, `dryRun` |
| `sdlc dev link` | `checkout`, `entry`, `configPath`, `previous`, `branch`, `head`, `wrote`, `dryRun` |
| `sdlc dev off` | `configPath`, `previous`, `removed`, `dryRun` |
| `sdlc dev status` | `source`, `active`, `envHome`, `cwd`, `link`, `history`, `local`, `path`, `shim` |
| `sdlc dev use` | — |
| `sdlc docs build-subsites` | `dist`, `subsites` |
| `sdlc docs generate` | `artifacts`, `drift?` |
| `sdlc driver get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc driver list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc driver related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc driver search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc duplication-scan apply-verdicts` | `changed`, `unknown`, `convergence`, `findings`, `objections` |
| `sdlc duplication-scan init` | `ledger`, `findings`, `objections` |
| `sdlc duplication-scan merge-findings` | `idmap`, `unknown`, `findings`, `objections` |
| `sdlc duplication-scan merge-objections` | `idmap`, `unknown`, `findings`, `objections` |
| `sdlc duplication-scan report-tables` | `markdown`, `findings`, `objections` |
| `sdlc duplication-scan status` | `convergence`, `findings`, `objections` |
| `sdlc entities audit` | `serious`, `entities`, `files`, `markdown_report` |
| `sdlc entities check-identifiers` | `violations` |
| `sdlc entities get-schema` | `types` |
| `sdlc entities migrate` | `fixesApplied`, `fixesPlanned`, `manualPending`, `skippedClosed`, `failures` |
| `sdlc entities validate` | `results`, `failed`, `total`, `skipped`, `quiet` |
| `sdlc gate comment-only` | `removed`, `violations`, `filesDeleted` |
| `sdlc gate corpus-invocation` | `violations`, `filesScanned` |
| `sdlc gate markdown-fixtures` | `version`, `results`, `fatal?` |
| `sdlc gate skill-prose` | `violations`, `inspected`, `covered` |
| `sdlc gate worktree-scope` | `collisions`, `skipped`, `reason?` |
| `sdlc harness detect` | `tools`, `providers`, `runtimes`, `hosts`, `suggestedHost`, `hostDefaultWritten`, `hostDefaultNotes` |
| `sdlc harness install claude` | `mode`, `autoDetected`, `projectRoot`, `sourcePath`, `version`, `marketplacePath`, `installPath`, `dryRun`, `tree`, `stamp`, `settings`, `settingsCleanup`, `gitignore`, `localSettingsGitignore`, `changed` |
| `sdlc harness model` | `pluginRoot`, `model`, `stats` |
| `sdlc hooks run` | `hook`, `root`, `outcome`, `steps`, `park_marker` |
| `sdlc issues sync` | `checked`, `conflicts`, `items` |
| `sdlc journal append` | `written`, `kind` |
| `sdlc journal tail` | `events` |
| `sdlc lease heartbeat` | `ref`, `expiresAt` |
| `sdlc lease inspect` | — |
| `sdlc lease list` | `prefix`, `rows` |
| `sdlc lease migrate` | `dryRun`, `migrated`, `stale` |
| `sdlc lease op claim` | `operation`, `operationKey`, `leaseId` |
| `sdlc lease parse-footer` | `taskId`, `leaseId` |
| `sdlc lease reconcile` | `authority`, `timestampUtc`, `prunedLocals`, `categoriesRun`, `status`, `stubbed`, `anomalies`, `actions`, `loop`, `summary`, `exitTier` |
| `sdlc lease release` | `ref` |
| `sdlc lease task acquire` | `taskId`, `leaseId`, `phase` |
| `sdlc lease task archive` | `taskId`, `archiveRef` |
| `sdlc lease task claim` | `taskId`, `leaseId`, `leaseToken` |
| `sdlc lease task fence` | `matched`, `taskId`, `expected`, `actual` |
| `sdlc lease task footer` | `footer` |
| `sdlc lease task reacquire` | `taskId`, `phase`, `leaseId` |
| `sdlc lease task release` | `taskId`, `archiveRef`, `archived`, `dryRun`, `forced`, `evidence`, `workRun`, `opsCleared`, `opsSkipped` |
| `sdlc lease task sweep` | `authority`, `dryRun`, `rows`, `summary` |
| `sdlc lease task transition` | `taskId`, `phase`, `leaseId` |
| `sdlc lease task update-gates` | `taskId`, `leaseId`, `readinessVerifiedAt`, `touchpointsVerifiedAt` |
| `sdlc log hooks install` | `harness`, `hooksPath`, `dryRun`, `added`, `present`, `updated`, `backup`, `command`, `approveInstruction` |
| `sdlc log hooks status` | `harness`, `hooksPath`, `readable`, `error?`, `warnings`, `events`, `installed`, `ready`, `next` |
| `sdlc log hooks uninstall` | `harness`, `hooksPath`, `dryRun`, `removed`, `shifted`, `backup` |
| `sdlc log prune` | `dryRun`, `files`, `bytes`, `items` |
| `sdlc log search` | `hits`, `next`, `facets` |
| `sdlc log setup` | `status`, `file`, `rule` |
| `sdlc log stage` | `staged`, `skipped` |
| `sdlc markdown check` | `exitCode` |
| `sdlc markdown fmt` | `exitCode` |
| `sdlc markdown verify-reflow` | `exitCode`, `results` |
| `sdlc mcp register` | `harness`, `dryRun`, `targetPath`, `format`, `command`, `args`, `stanza`, `changed`, `wrote` |
| `sdlc milestone create` | `path`, `id` |
| `sdlc milestone get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc milestone list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc milestone related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc milestone search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc note create` | `path`, `id` |
| `sdlc note get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc note list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc note preview-id` | `entity`, `input`, `slug`, `id`, `exists`, `exact_match`, `similar`, `warning` |
| `sdlc note related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc note search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc notify send` | `channel`, `ok`, `error`, `dryRun` |
| `sdlc notify test` | `results` |
| `sdlc orchestrate base-health skip` | `applied`, `target`, `sha`, `skipped` |
| `sdlc orchestrate log-tail` | `logPath`, `exists`, `total`, `lines` |
| `sdlc orchestrate log-tick` | `tick`, `summaryLine`, `eventLines`, `logPath`, `written` |
| `sdlc orchestrate pause` | `applied`, `pausedAt`, `loops` |
| `sdlc orchestrate prs` | `prs`, `prsChecked` |
| `sdlc orchestrate resume` | `applied`, `paused`, `loops` |
| `sdlc orchestrate run` | `loop`, `passes` |
| `sdlc orchestrate run-info` | `basename`, `runId`, `manual`, `output`, `found`, `state`, `startedAt`, `endedAt`, `pid`, `ok`, `failedWorkflow?`, `failedStep?`, `failedExitCode?`, `failedOutputTail?`, `failedReason?`, `failedStepName?`, `failures?`, `died?`, `logPath`, `logTail` |
| `sdlc orchestrate start` | `root`, `rootMode`, `results` |
| `sdlc orchestrate status` | `paused`, `loops`, `pausedAt`, `cooldowns`, `baseHealth`, `running`, `stateRoot`, `loopRecords`, `configFiles`, `waiting?`, `processes?` |
| `sdlc orchestrate stop` | `results` |
| `sdlc orchestrate target` | `branch`, `ref`, `source`, `sha` |
| `sdlc orchestrate watch` | `pending`, `reasons`, `prs`, `tasks`, `emitted` |
| `sdlc orchestrate work-run` | `basename`, `ok`, `failedWorkflow?`, `failedStep?`, `failedExitCode?`, `failedOutputTail?`, `failedReason?`, `failedStepName?` |
| `sdlc orchestrate work-start` | `basename`, `ok`, `message`, `applied` |
| `sdlc orchestrate work-stop` | `basename`, `ok`, `message`, `applied` |
| `sdlc plugin info` | `root`, `version`, `liveness`, `git`, `tag`, `ok`, `renderText` |
| `sdlc plugin install-permissions` | `rules`, `added`, `total`, `settingsPath`, `dryRun`, `listOnly` |
| `sdlc plugin resolve` | `repoRoot`, `originUrl` |
| `sdlc pr classify` | `verdict`, `reason`, `mock`, `cursorPath`, `lastSeenCommentAt`, `lastInvokedAt` |
| `sdlc pr create` | `created`, `number`, `url` |
| `sdlc pr detail` | `number`, `title`, `url`, `state`, `isDraft`, `author`, `base`, `head`, `headSha`, `mergeable`, `reviewDecision`, `checks`, `verdict`, `reviewRounds`, `maxRounds`, `refusals`, `threads` |
| `sdlc pr merge` | `pr`, `status`, `base`, `headSha`, `method`, `refusals`, `error` |
| `sdlc pr ready` | `pr`, `status`, `headSha` |
| `sdlc pr record-check` | `pr`, `headSha`, `checkOk`, `checkedAt` |
| `sdlc pr review` | `pr`, `repoRoot`, `worktreePath`, `worktreeCreated`, `branch`, `host`, `engine`, `launched`, `exitCode`, `printPath`, `trusted`, `setup`, `refresh`, `prompt` |
| `sdlc pr review-verdict` | `pr`, `head`, `ok`, `detail` |
| `sdlc pr route` | `pr`, `route`, `status`, `reason?`, `evidence?`, `armed?`, `signaturesSent`, `resolvedSessionId` |
| `sdlc pr stats` | `source`, `files`, `lines`, `categories`, `fileList`, `dependencies`, `api`, `complexity`, `notes`, `markdown`, `written` |
| `sdlc pr survey` | `repos`, `prs`, `cycles`, `counts` |
| `sdlc pr update` | `applied`, `interactive`, `results`, `skipped`, `counts`, `notified` |
| `sdlc principle get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc principle list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc principle related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc principle search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc product get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc product list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc product related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc product search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc project apply-cleanup` | `receiptPath`, `receipt` |
| `sdlc project cleanup` | `repo`, `items`, `summary`, `scanSummary`, `markdownReport`, `repos`, `duplicates`, `unresolved`, `scan`, `planPath` |
| `sdlc project create-worktree` | `path`, `branch`, `startPoint` |
| `sdlc project detect-setup` | `project_root`, `candidates` |
| `sdlc project doctor` | `probes`, `harnesses`, `hosts`, `liveSend`, `allRequiredOk`, `sources`, `auth`, `sameInstall`, `checks`, `configNeeds`, `verbs`, `trust`, `writeCapableHooks`, `applyDrift`, `parkMarkers`, `routing`, `ignoredLayerValues`, `machineConfig`, `routingRules`, `routingEnv`, `modelCatalogue`, `logStore`, `logStoreFailures`, `installedHooks`, `cooldowns`, `lostSessions`, `logPrunable`, `deprecations`, `configError` |
| `sdlc project follow` | `outcome?`, `reason?`, `wouldFetch?`, `commit`, `time`, `mode` |
| `sdlc project migrate-runtime-state` | `exitCode` |
| `sdlc project preflight-worktree` | `worktreePath`, `branch`, `dirty`, `porcelain`, `worktreeCollision`, `branchCollision`, `prChecked`, `openPrs`, `anyCollision` |
| `sdlc project scan candidates` | `candidates` |
| `sdlc project scan comments` | `scanned`, `files`, `shards`, `totals` |
| `sdlc project scan todos` | `hits` |
| `sdlc project setup` | `projectRoot`, `planningRoot`, `entitiesDir`, `dryRun`, `dirs`, `sdlcYaml`, `gitignore`, `sdlcDir`, `rumdl`, `leaseNamespace`, `obsidianBases` |
| `sdlc project teardown-worktree` | `worktreePath`, `dryRun`, `worktreeExisted`, `removed`, `branch`, `branchExisted`, `branchDeleted`, `notes` |
| `sdlc quality baseline capture` | `path`, `sha` |
| `sdlc quality baseline diff` | `newDrift`, `preExisting`, `gatePass`, `newDriftCount` |
| `sdlc quality detect` | `project_root`, `tasks`, `diagnostics` |
| `sdlc quality run` | `passed`, `total`, `ok`, `mode`, `baselineGated`, `dryRun`, `planned`, `results` |
| `sdlc reference create` | `path`, `id` |
| `sdlc reference get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc reference list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc reference preview-id` | `entity`, `input`, `slug`, `id`, `exists`, `exact_match`, `similar`, `warning` |
| `sdlc reference related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc reference search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc repo checkout` | `repo`, `host`, `cloneUrl`, `path`, `created`, `found`, `branch`, `dryRun`, `printPath` |
| `sdlc repo trust` | — |
| `sdlc repo untrust` | `root`, `wasTrusted`, `dryRun`, `policyBy`, `policyRule?` |
| `sdlc roadmap check` | `serious`, `roadmaps_checked`, `findings` |
| `sdlc roadmap create` | `path`, `id` |
| `sdlc roadmap get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc roadmap list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc roadmap progress` | `roadmaps` |
| `sdlc roadmap related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc roadmap search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc session adopt` | `id`, `engine`, `host`, `cwd`, `branch`, `task`, `harnessSessionId`, `pid`, `hooks`, `exportLine` |
| `sdlc session attach` | `id`, `host`, `harnessSessionId`, `attached`, `exitCode`, `message` |
| `sdlc session digest` | `id`, `status`, `reason?`, `transcript`, `usage_turn`, `turn_prompt`, `turn_stop`, `tool_call`, `raw_files` |
| `sdlc session done` | `id`, `state`, `outcome`, `note` |
| `sdlc session focus` | `id`, `host`, `branch` |
| `sdlc session hook` | `session`, `event`, `lifecycle`, `activity` |
| `sdlc session launch` | `id`, `engine`, `host`, `cwd`, `launched`, `headless`, `exitCode`, `harnessSessionId`, `logPath`, `timedOut?` |
| `sdlc session list` | `rows`, `tree?`, `unreadable?`, `untracked?` |
| `sdlc session reconcile` | `dryRun`, `rows`, `lost`, `live`, `quiet`, `unknown` |
| `sdlc session rename` | `id`, `name` |
| `sdlc session run` | `exitCode` |
| `sdlc session show` | `record`, `name`, `timeline`, `turns`, `spans`, `parent`, `children`, `run`, `subject`, `costUsd`, `durationMs`, `resume` |
| `sdlc setup apply` | `mode`, `steps`, `changes`, `delivery?`, `candidates?`, `hostChecks?`, `notWritten?` |
| `sdlc setup plan` | `mode`, `steps`, `changes`, `delivery?`, `candidates?`, `hostChecks?`, `notWritten?` |
| `sdlc shell completion` | `shell`, `text?`, `path?`, `status?`, `note?` |
| `sdlc shell init` | `shell`, `text` |
| `sdlc site build` | `dist`, `siteDir`, `contentRoot`, `packageManager`, `runner`, `installed`, `built` |
| `sdlc standard create` | `path`, `id` |
| `sdlc standard get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc standard list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc standard preview-id` | `entity`, `input`, `slug`, `id`, `exists`, `exact_match`, `similar`, `warning` |
| `sdlc standard related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc standard search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc standard supersede` | `path`, `changed`, `wrote`, `superseded` |
| `sdlc standard update` | `path`, `changed`, `wrote` |
| `sdlc task begin` | `exitCode` |
| `sdlc task check-ancestry` | `exitCode` |
| `sdlc task check-claims` | — |
| `sdlc task close-commit` | `taskPath`, `pushedSha`, `prsAction`, `prsCount`, `prsAddendum`, `postMortemStubbed`, `dryRun`, `diff` |
| `sdlc task create` | `path`, `id` |
| `sdlc task dedup-search` | `exitCode` |
| `sdlc task dispatch` | `basename`, `claim`, `ok?`, `failedWorkflow?`, `failedStep?`, `failedExitCode?`, `failedOutputTail?`, `failedReason?`, `failedStepName?`, `parked?`, `unsatisfied?`, `interrupted?` |
| `sdlc task ensure-ready-mutate` | `exitCode` |
| `sdlc task gap-report` | `has_gaps`, `gaps`, `parenthood`, `sections`, `placeholders`, `touchpoints`, `claims` |
| `sdlc task get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc task inflight` | `limits`, `counts`, `caps_reached`, `tasks` |
| `sdlc task lint-state-origin` | `totalViolations`, `okBranches`, `violationLines` |
| `sdlc task list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc task list-unmerged` | `base`, `remote`, `gh_available`, `prs_scanned`, `skipped_refs`, `tasks` |
| `sdlc task next` | `ordered`, `cycle`, `explain`, `skipped_blocked`, `skipped_parent`, `skipped_kind`, `skipped_scope`, `skipped_human` |
| `sdlc task parse-touchpoints` | `areas` |
| `sdlc task preflight-permissions` | `exitCode` |
| `sdlc task preview-id` | `entity`, `input`, `slug`, `id`, `exists`, `exact_match`, `similar`, `warning` |
| `sdlc task probe-state` | `basename`, `worktree_path`, `branch`, `worktree_exists`, `branch_exists`, `task_state`, `readiness_verified_at`, `open_pr_number`, `lease_present`, `lease_phase`, `lease_pr_number`, `lease_holder`, `main_head_subject`, `main_head_is_verify_stamp`, `blocked_preflight`, `resume_candidate` |
| `sdlc task related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc task resolve` | `arg`, `resolved`, `reason`, `path`, `basename`, `candidates` |
| `sdlc task retry` | `basename`, `unparked`, `failures` |
| `sdlc task scan-corpus-assumptions` | `exitCode` |
| `sdlc task scan-placeholders` | — |
| `sdlc task scan-validate-ac` | `exitCode` |
| `sdlc task search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc task start` | `task`, `branch`, `worktree`, `leaseOwnerKind`, `leasePhase`, `sessionId`, `host`, `reused`, `focused` |
| `sdlc task summarize-dedup-telemetry` | `exitCode` |
| `sdlc task update` | `path`, `changed`, `wrote` |
| `sdlc term create` | `path`, `id` |
| `sdlc term get` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc term list` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc term preview-id` | `entity`, `input`, `slug`, `id`, `exists`, `exact_match`, `similar`, `warning` |
| `sdlc term related` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc term search` | `result`, `diagnostics`, `stale`, `next_cursor` |
| `sdlc verify changes` | `base`, `baseSource`, `resolvedBase`, `changedFiles`, `jobs`, `skipped`, `findings`, `degraded`, `blocking`, `severityFloor`, `perSource`, `reportPath?`, `dryRun` |
| `sdlc workflow list` | `names` |
| `sdlc workflow run` | `workflow`, `dryRun`, `ok`, `exitCode`, `steps` |
| `sdlc workflow show` | `name`, `layer`, `steps` |

## Entity frontmatter

| Type | Fields (`?` optional) |
|---|---|
| `backlog` | `type?`, `schema_version?`, `id?`, `state?`, `title?`, `created?`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `likely_type?`, `result?` |
| `capability` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `kind?`, `audience?`, `parent_key?`, `locations?`, `product?` |
| `decision` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `supersedes?`, `superseded_by?`, `deprecation_note?` |
| `driver` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `kind`, `product` |
| `milestone` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `version?`, `target_date?`, `tasks?`, `capabilities?`, `relevance_note?`, `completion_note?` |
| `note` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `genre`, `parent?`, `superseded_by?` |
| `principle` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?` |
| `product` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?` |
| `reference` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `url?` |
| `roadmap` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `plan_doc`, `completion_note?` |
| `standard` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `applies_to`, `supersedes?`, `superseded_by?`, `enforcement?`, `severity_floor?`, `deprecation_note?` |
| `task` | `type?`, `schema_version?`, `id`, `state?`, `title?`, `created?`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `impact?`, `priority?`, `complexity?`, `autonomy?`, `session?`, `kind?`, `scope?`, `scheduling?`, `parent_key?`, `depends_on?`, `prs?`, `issue?`, `relevance_note?`, `completion_note?`, `definition_gap?`, `resolution?`, `resolution_date?`, `resolution_commit?`, `readiness_verified_at?`, `touchpoints_verified_at?` |
| `term` | `type`, `schema_version?`, `id`, `state?`, `title`, `created`, `last_reviewed?`, `related?`, `tags?`, `need_human_review?`, `created_at?`, `provenance?`, `status?`, `description?`, `resource?`, `stale_after?`, `generated?`, `verified?`, `sources?`, `usage_window?`, `aliases?` |

## Skills

| Invocation |
|---|
| `/craft:api-enhancement-scan` |
| `/craft:capability-groom` |
| `/craft:check-pr` |
| `/craft:code-review` |
| `/craft:collaborate` |
| `/craft:comment-review` |
| `/craft:decision-make` |
| `/craft:discuss` |
| `/craft:explain` |
| `/craft:explore-codebase` |
| `/craft:lib-publish-ready` |
| `/craft:parley` |
| `/craft:pr-mermaid-comment` |
| `/craft:pr-review` |
| `/craft:pr-stats` |
| `/craft:research` |
| `/craft:review-pr` |
| `/craft:skill-author` |
| `/craft:skill-review` |
| `/sdlc:backlog-capture` |
| `/sdlc:backlog-triage` |
| `/sdlc:bug-finder` |
| `/sdlc:capability-map` |
| `/sdlc:capability-review` |
| `/sdlc:consolidate-task-prs` |
| `/sdlc:docs` |
| `/sdlc:docs-sweep` |
| `/sdlc:duplication-scan` |
| `/sdlc:entities-audit` |
| `/sdlc:entities-migrate` |
| `/sdlc:find-verbs` |
| `/sdlc:import-planning` |
| `/sdlc:info` |
| `/sdlc:markdown-format` |
| `/sdlc:migrate` |
| `/sdlc:migrate-runtime-state` |
| `/sdlc:milestone-new` |
| `/sdlc:milestone-review` |
| `/sdlc:milestones-from-file` |
| `/sdlc:orchestrate` |
| `/sdlc:pr-check` |
| `/sdlc:pr-respond` |
| `/sdlc:pr-review` |
| `/sdlc:principle-review` |
| `/sdlc:product-new` |
| `/sdlc:project-cleanup` |
| `/sdlc:review-todos` |
| `/sdlc:session-cleanup` |
| `/sdlc:setup` |
| `/sdlc:spawn-task-pr` |
| `/sdlc:standard-new` |
| `/sdlc:standard-review` |
| `/sdlc:status-dashboard` |
| `/sdlc:task-auto-define` |
| `/sdlc:task-close-out` |
| `/sdlc:task-define` |
| `/sdlc:task-ensure-ready` |
| `/sdlc:task-new` |
| `/sdlc:task-review` |
| `/sdlc:task-work` |

## Experimental

Not covered by the promise.

| Surface | Item |
|---|---|
| `sdlc.yaml` key | `router` |
| `sdlc.yaml` key | `harness` |
| `sdlc.yaml` key | `usage` |
| `sdlc.yaml` key | `schedules` |
| `sdlc.yaml` key | `runner` |
| Command | `sdlc friction create` |
| Command | `sdlc friction detect` |
| Command | `sdlc friction get` |
| Command | `sdlc friction list` |
| Command | `sdlc friction preview-id` |
| Command | `sdlc friction promote` |
| Command | `sdlc friction related` |
| Command | `sdlc friction search` |
| Command | `sdlc friction update` |
| Command | `sdlc lib history` |
| Command | `sdlc models explain` |
| Command | `sdlc models list` |
| Command | `sdlc models refresh` |
| Command | `sdlc okf validate` |
| Command | `sdlc report get-schema` |
| Command | `sdlc report render` |
| Command | `sdlc route report` |
| Command | `sdlc route resolve` |
| Command | `sdlc schedule add` |
| Command | `sdlc schedule edit` |
| Command | `sdlc schedule list` |
| Command | `sdlc schedule pause` |
| Command | `sdlc schedule promote` |
| Command | `sdlc schedule remove` |
| Command | `sdlc schedule resume` |
| Command | `sdlc schedule run` |
| Command | `sdlc schedule runs` |
| Command | `sdlc schedule status` |
| Command | `sdlc trace show` |
| Command | `sdlc tui launch` |
| Command | `sdlc usage report` |
| Entity type | `friction` |
