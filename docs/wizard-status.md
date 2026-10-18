---
type: guide
title: Wizard build status
---

# Wizard build status

> **Generated** from section meta (`lib/config/sections/`) and the unsupported-choice list
> (`lib/services/setup/status.ts`) by `sdlc docs generate wizard-status`. **Do not hand-edit.**

| Status | Meaning | Rows |
|---|---|---|
| `real` | A section with a reader; the wizard can write it. | 29 |
| `planned` | A reserved section; nothing reads it yet. | 2 |
| `no-key` | A wizard choice with no `sdlc.yaml` key; reported, never written. | 16 |

| Key | Status | Why |
|---|---|---|
| `workflows` | `real` | 14 consumers |
| `profiles` | `real` | 1 consumer |
| `routing` | `real` | 1 consumer |
| `router` | `real` | 3 consumers |
| `orchestrator` | `real` | 18 consumers |
| `pr_check` | `real` | 4 consumers |
| `pr_update` | `real` | 2 consumers |
| `tracker` | `real` | 1 consumer |
| `code_host` | `real` | 1 consumer |
| `task` | `real` | 1 consumer |
| `backlog` | `real` | 2 consumers |
| `lease_authority` | `real` | 4 consumers |
| `docs_site` | `real` | 5 consumers |
| `paths` | `real` | 2 consumers |
| `repos` | `real` | 1 consumer |
| `checkout` | `real` | 1 consumer |
| `host` | `real` | 4 consumers |
| `log` | `real` | 5 consumers |
| `pr_review` | `real` | 1 consumer |
| `verify` | `real` | 3 consumers |
| `lib` | `real` | 1 consumer |
| `chain` | `real` | 2 consumers |
| `hooks` | `real` | 3 consumers |
| `harness` | `real` | 1 consumer |
| `notify` | `real` | 3 consumers |
| `mcp` | `real` | 2 consumers |
| `knowledge` | `planned` | no reader yet |
| `issues` | `planned` | sdlc issues sync and the issues loop (sdlc 1.0 Phase 7) |
| `usage` | `real` | 3 consumers |
| `schedules` | `real` | 2 consumers |
| `runner` | `real` | 1 consumer |
| `issues.source` | `no-key` | step source: only the repository itself is a supported issue source |
| `lease_authority` | `no-key` | step source: a local folder path is needed and the wizard does not ask for one |
| `preset` | `no-key` | step connect: no preset key; only solo is the default |
| `paths.model` | `no-key` | step connect: `paths:` is a free-form map and nothing reads a `model` entry |
| `orchestrator.dispatch` | `no-key` | step level: no switch turns dispatch off |
| `harnesses` | `no-key` | step tools: the installed AI tools are not recorded |
| `local_model` | `no-key` | step tools: no key for a local model |
| `verify.modes` | `no-key` | step review: review depth has no key |
| `improvement.look_back` | `no-key` | step improve: no key for a look-back PR |
| `improvement.when` | `no-key` | step improve: no key for when to look back |
| `milestone look-back` | `no-key` | step improve: there is no milestone-review workflow to add it to |
| `on_fail for workflow steps` | `no-key` | step extend: only git hook steps have `on_fail` |
| `notify.channels.phone` | `no-key` | step alerts: needs a topic address (`phoneTopicUrl`) |
| `sandbox.where` | `no-key` | step sandbox: no key for where the sandbox runs |
| `sandbox.network` | `no-key` | step sandbox: no key for sandbox network access |
| `sandbox.secrets_file` | `no-key` | step sandbox: no key for a sandbox secrets file |
