---
type: guide
title: sdlc.yaml enforcement
---

# `sdlc.yaml` enforcement

> **Generated** from the `enforcement` and `consumers` meta on each key of
> `SdlcConfigSchema` by `sdlc docs generate sdlc-yaml-enforcement`. **Do not
> hand-edit** — set the `enforcement` value in the `meta` of the key's section
> module under `lib/config/sections/` and regenerate.

Setting a key in `sdlc.yaml` does not always make sdlc act on it. Each key is
graded by the strongest thing that is true of its section as a whole (so a
`enforced` section may still hold plain-read fields):

| Enforcement | Meaning | Keys |
|---|---|---|
| `enforced` | sdlc refuses, blocks or caps something when a setting in the section is violated. | 6 |
| `applied` | Nothing changes until `sdlc apply` reconciles the setting into files or remote state; says nothing about run-time blocking. | 4 |
| `read` | Consumers read it at run time and behave accordingly; nothing checks against it. | 19 |
| `recorded` | Accepted and stored; nothing reads it yet. | 2 |

Per-field detail is in the [`sdlc.yaml` reference](sdlc-yaml-reference.md).

## Keys

| Key | Layers | Read by | Enforcement |
|---|---|---|---|
| `orchestrator` | project, local | `lib/services/orchestrator/ops/run.ts`<br>`lib/services/orchestrator/ticks/prs.ts`<br>`lib/services/orchestrator/ticks/merges.ts`<br>+10 more | `enforced` |
| `pr_update` | project, local | `src/survey.ts`<br>`src/update.ts` | `enforced` |
| `lease_authority` | project | `lib/config/load.ts`<br>`lib/config/sdlc_yaml.ts`<br>`lib/services/lease/runtime.ts`<br>+1 more | `enforced` |
| `verify` | project, local, subtree | `lib/services/verify/ops/changes.ts`<br>`lib/services/verify/binding.ts`<br>`lib/services/verify/nested-config.ts` | `enforced` |
| `usage` | project, local | `lib/services/usage/budget.ts`<br>`lib/services/usage/cooldown.ts`<br>`lib/services/orchestrator/ticks/work.ts` | `enforced` |
| `schedules` | project, local | `lib/services/schedule/due.ts`<br>`lib/services/orchestrator/ticks/schedule.ts` | `enforced` |
| `hooks` | project, local | `lib/config/hooks.ts`<br>`lib/services/hooks/run.ts`<br>`lib/services/apply/hooks.ts` | `applied` |
| `harness` | project, local | `lib/services/apply/harness.ts` | `applied` |
| `mcp` | project, local | `lib/services/apply/mcp.ts`<br>`lib/services/mcp/server.ts` | `applied` |
| `runner` | local | `lib/services/apply/schedule-runner.ts` | `applied` |
| `workflows` | project, local | `lib/config/workflows.ts`<br>`lib/config/hooks.ts`<br>`lib/services/config/config-file.ts`<br>+11 more | `read` |
| `profiles` | project, local | `lib/services/route/route.ts` | `read` |
| `routing` | project, local | `lib/services/route/route.ts` | `read` |
| `router` | project, local | `lib/config/models.ts`<br>`lib/services/route/pins.ts` | `read` |
| `pr_check` | project, local | `lib/services/pr/ops/classify.ts`<br>`lib/services/orchestrator/ops/watch.ts`<br>`lib/services/dashboard/server.ts` | `read` |
| `tracker` | project, local | `lib/services/issues/tracker/select.ts` | `read` |
| `code_host` | project, local | `src/select.ts` | `read` |
| `task` | project, local | `lib/services/config/ops/get-spawn-policy.ts` | `read` |
| `backlog` | project, local | `cli/backlog_cli/create.ts`<br>`lib/services/config/ops/get-backlog-policy.ts` | `read` |
| `docs_site` | project, local | `lib/config/load.ts`<br>`lib/services/docs/site.ts`<br>`lib/services/docs/site/manifest.ts`<br>+2 more | `read` |
| `paths` | project, local, machine | `lib/services/repo/locate.ts`<br>`lib/services/repo/layout.ts` | `read` |
| `repos` | project, local, machine | `lib/services/repo/locate.ts` | `read` |
| `checkout` | project, local, machine | `lib/services/repo/layout.ts` | `read` |
| `host` | project, local | `lib/services/host/index.ts`<br>`lib/services/session/ops/launch.ts`<br>`lib/services/pr/ops/review.ts`<br>+1 more | `read` |
| `log` | project, local | `lib/services/log/store.ts`<br>`lib/services/log/store-git.ts`<br>`lib/services/session/hook-report.ts`<br>+2 more | `read` |
| `pr_review` | project, local | `lib/services/pr/ops/review.ts` | `read` |
| `lib` | project, local | `lib/services/library/ops/history.ts` | `read` |
| `chain` | project, local | `lib/config/chain.ts`<br>`lib/services/orchestrator/ticks/issues.ts` | `read` |
| `notify` | project, local | `lib/services/notify/dispatch.ts`<br>`lib/services/notify/ops/send.ts`<br>`lib/services/notify/ops/test.ts` | `read` |
| `knowledge` | project, local | nothing yet | `recorded` |
| `issues` | project, local | nothing yet | `recorded` |
