---
name: ai-team-orchestration
description: Orchestrate a profile-based AI team in Hermes using SOUL.md identity files, agents.yaml registry, Kanban handoffs, and evidence-based review.
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [multi-agent, orchestration, profiles, kanban, ai-team]
    related_skills: [kanban-orchestrator, hermes-agent]
    created_by: agent
---

# AI Team Orchestration

Use this skill when the user wants Hermes profiles to behave like a coordinated team of specialized AI agents instead of a single assistant doing everything.

Typical triggers:
- The user asks to create, configure, or improve a team of Hermes profiles such as `orch`, `coding`, `fast`, `strong`, and `search`.
- The user wants a dashboard/command such as `hermes agents` or `agents` to see and route work across agents.
- The task involves deciding what belongs in `SOUL.md`, `agents.yaml`, skills, and Kanban.
- The user wants profile-specific models to be used intentionally, especially to avoid spending a premium model on simple or implementation-only work.

## Core model

Keep these layers separate:

1. `SOUL.md` = identity, mission, boundaries, decision style, cooperation rules for each profile.
2. Skills = reusable procedures, checklists, playbooks, and workflows.
3. `agents.yaml` = registry of the AI team: agent names, mapped Hermes profiles, roles, permissions, default skills, escalation route.
4. Kanban = durable execution/handoff layer between profiles.
5. `hermes agents` / dashboard = user-facing control surface.

Do not collapse these into one giant prompt. A clean system is easier to debug and cheaper to run.

## Recommended roles

Baseline team:

- `orch`: manager/orchestrator. Understands goal, decomposes work, routes to the right profile, tracks blockers, reviews evidence, consolidates final answer.
- `coding`: implementer. Edits code, runs tests/builds/commands, debugs, returns concrete evidence. Does not decide product/architecture scope alone.
- `fast`: cheap/simple worker. Handles simple, low-risk, reversible tasks. Escalates if the task grows.
- `strong`: senior reasoning/review. Handles architecture, strategy, trade-offs, risk analysis, difficult decisions, and reviews.
- `search`: researcher. Looks up current facts, docs, APIs, references, and sources. Does not make architecture decisions or implement.

## Routing policy

Use the cheapest capable path:

- Simple operational task -> `fast`.
- Code/product file edit, tests, debugging, app behavior -> `coding`.
- Current documentation, external facts, sources, API details -> `search`.
- Architecture, ambiguous trade-off, strategic decision, review -> `strong`.
- Multi-step/multi-profile task -> `orch` decomposes and coordinates.

Pitfall: generic `delegate_task` may not call the real named profile/model. For a real AI team, prefer Kanban or explicit Hermes profile execution so `coding` actually uses the `coding` profile/model, `fast` uses `fast`, etc.

## SOUL.md governance

Strengthen profile `SOUL.md` files before expecting reliable team behavior.

Start with `orch/SOUL.md`:
- Make it explicit that `orch` is the AI-team manager, not the implementer.
- Add a model/cost discipline: do not use premium reasoning for cheap/simple tasks.
- Add a routing matrix for `coding`, `fast`, `strong`, and `search`.
- Add blocker protocol: workers escalate unclear scope, architecture, security, cost, UX, and product decisions to `orch`.
- Add review protocol: `orch` must check real evidence before saying work is done.
- Add Kanban preference for durable multi-agent execution.

Then refine `coding/SOUL.md`:
- Require files changed, commands run, tests/build/manual checks, and explicit blockers.
- Keep it implementation-focused; avoid long brainstorms unless asked.
- Product/architecture decisions go back to `orch`/`strong`.

Then refine `strong`, `search`, and `fast`:
- `strong`: review, architecture, trade-offs, risks; not routine execution.
- `search`: sourced research and documentation; no implementation or architecture ownership.
- `fast`: simple low-risk work only; escalates when complexity grows.

## agents.yaml guidance

Create a profile-scoped registry such as `$HERMES_HOME/agents.yaml`.

Starter shape:

```yaml
version: 1
agents:
  orch:
    profile: orch
    role: orchestrator
    description: Planeja, delega, acompanha e consolida trabalho do time de IAs.
    can_edit_files: false
    escalates_to: user
    skills: [kanban-orchestrator, ai-team-orchestration]

  coding:
    profile: coding
    role: implementer
    description: Implementa código, roda testes, depura e entrega evidências reais.
    can_edit_files: true
    escalates_to: orch
    skills: [systematic-debugging, test-driven-development, requesting-code-review]

  fast:
    profile: fast
    role: quick_worker
    description: Executa tarefas rápidas, simples e de baixo risco.
    can_edit_files: limited
    escalates_to: orch
    skills: []

  strong:
    profile: strong
    role: architect_reviewer
    description: Analisa arquitetura, trade-offs, riscos e decisões difíceis.
    can_edit_files: false
    escalates_to: orch
    skills: []

  search:
    profile: search
    role: researcher
    description: Pesquisa documentação, fatos atuais e fontes externas.
    can_edit_files: false
    escalates_to: orch
    skills: []
```

## Orchestrated task flow

For a new user request inside the team system:

1. `orch` clarifies only if ambiguity changes scope, architecture, security, cost, or UX.
2. `orch` creates a task graph: independent lanes in parallel, dependent lanes linked.
3. `orch` routes each lane to a real profile via Kanban or explicit profile execution.
4. Workers execute and return evidence or block with a specific question.
5. `orch` resolves blockers: answer directly, ask user, or route to `strong`/`search`.
6. `orch` reviews outputs and evidence.
7. `orch` consolidates: objective, agents used, result, verification, risks, pending items, next recommended step.

## Dashboard / CLI MVP guidance

A good first CLI MVP is not a full-screen UI. Start with stable commands:

- `hermes agents` or `hermes agents list`: table of agent, profile, model, provider, role, status.
- `hermes agents list --json`: machine-readable output for a future TUI.
- `hermes agents init`: creates starter `$HERMES_HOME/agents.yaml` without overwriting by default.
- `hermes agents orchestrate "task"`: creates/prepares a Kanban planning card for `orch` rather than doing the work itself.

Avoid for MVP:
- new core model tools;
- deep agent-loop changes;
- full-screen terminal UI before the backend contract is stable;
- pretending autonomous execution is complete when only a planning handoff exists.

## Handoff prompt pattern for coding

When sending implementation to `coding`, pass:

- repository path;
- plan path;
- exact objective;
- files likely to change;
- constraints such as “do not alter core agent loop”;
- acceptance criteria;
- verification commands expected;
- requirement to return real evidence: changed files, commands run, outputs, failures/blockers.

## Verification discipline

For documentation-only planning changes, verify the file exists, is non-empty, has expected sections, and passes basic diff/format checks. Do not claim app tests passed if no executable code changed.

For code changes, require real tests/build/manual command evidence before reporting completion.

When a worker reports completion:

- Treat the report as a lead, not proof.
- Inspect the actual diff/files when accessible.
- Rerun the key test or command yourself when feasible.
- For CLI features, run the new command manually in addition to unit tests.
- For generated copy/paste shell commands, test adversarial input containing spaces, quotes, `$`, and shell metacharacters. Commands should be constructed with safe quoting (for Python, prefer `shlex.quote`).
- If a side effect already happened (card/file/task created), the final message should not tell the user to run the same creation command again. Show status/list/tail/start-dispatcher commands instead.

## Review pitfalls discovered

- A dashboard command can pass unit tests while still printing an unsafe manual command. Add tests for shell metacharacters whenever the output is intended to be copied into a terminal.
- A Kanban handoff command that successfully creates a card should not render “next step: create card” afterward; that risks duplicates and user confusion.
- If gateway/dispatcher is down but a card was created, recommend starting the dispatcher/gateway, not recreating the card.
- If a TUI/dashboard says `q/Esc to quit`, test the exact Escape byte (`"\x1b"`) in the interactive loop. Do not rely on help text or manual claims. Also make non-interactive/`--no-detail` help text avoid prompts like “press a number” when no prompt will appear.
- When a dashboard MVP is intentionally not fullscreen/Textual, define it as “navigable/semi-navigable” with a clear fallback path: Rich table in normal CLI, plain text when Rich is unavailable, and non-interactive output when stdin is not a TTY.

## Packaging / PR handoff pattern

After a Hermes AI-team feature is approved, hand `coding` a packaging task instead of more feature work:

1. Inspect `git status --short`, `git diff --stat`, `git diff --check`, and the targeted diff.
2. Separate repo files from local profile state. Commit code/tests/docs inside the repo; do not commit profile-local files such as `$HERMES_HOME/agents.yaml`, profile `SOUL.md`, memories, logs, or local skills unless they are intentionally part of the repository.
3. Re-run the accepted test command and smoke tests before committing.
4. Create a clear branch such as `feat/hermes-agents-dashboard`.
5. Commit only reviewed files with a conventional commit message.
6. Push/open PR when GitHub auth allows; otherwise report branch, commit hash, remaining local changes, and exact blocker.

See `references/hermes-agents-dashboard-mvp-review.md` for the session-specific verification checklist and final handoff used for the first dashboard MVP.

## Project packaging / fork strategy

When a Hermes-team feature is useful to the user but unlikely to be merged upstream, keep three tracks distinct: upstream PR attempt, runnable fork, and a separate public project/documentation repo. For the reusable repo shape, patch export command, and pre-publish safety checks, see `references/personal-fork-project-packaging.md`.

## Session note

See `references/agents-dashboard-mvp-governance.md` for the original MVP/governance outline that motivated this skill.
See `references/agents-dashboard-review-checklist.md` for the review checklist and quoting pitfalls from the first `hermes agents` implementation cycle.
See `references/personal-fork-project-packaging.md` for the personal fork + public project packaging pattern discovered after the upstream PR proved non-essential.
See `references/hermes-agents-dashboard-mvp-review.md` for the dashboard MVP review, Escape-key pitfall, non-interactive hint pitfall, and packaging/PR handoff.
