# Hermes Agents

Hermes Agents is an experimental/custom project to turn Hermes Agent profiles into a coordinated AI team.

The idea is to use specialized profiles as:

- **orch** — orchestrator/manager
- **coding** — implementation, testing, and debugging
- **fast** — quick and simple tasks
- **strong** — architecture, strategy, and critical review
- **search** — research, documentation, and current facts

## What this project adds

The MVP adds a CLI/dashboard layer to visualize and operate this team:

- `hermes agents`
- `hermes agents list --json`
- `hermes agents init`
- `hermes agents orchestrate "<task>"`
- `hermes agents dashboard`

## Status

MVP functional and validated locally.

Verification run:

```bash
venv/bin/python -m pytest tests/hermes_cli/test_agents_cli.py tests/hermes_cli/test_kanban_core_functionality.py -q -o 'addopts='
```

Validated result:

```
197 passed
```

## Base repository

This project is based on Hermes Agent:

https://github.com/NousResearch/hermes-agent

## How to apply the patch

Clone the official Hermes Agent:

```bash
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
```

Apply the patch:

```bash
git apply ../Hermes_Agents/patches/0001-feat-add-hermes-agents-dashboard.patch
```

Then run the relevant tests:

```bash
venv/bin/python -m pytest tests/hermes_cli/test_agents_cli.py tests/hermes_cli/test_kanban_core_functionality.py -q -o 'addopts='
```

## Note

This repository is **not** the official Hermes Agent. It is a personal documentation/customization of the Hermes Agents project.
