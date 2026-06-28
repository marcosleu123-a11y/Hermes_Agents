# Architecture

Hermes Agents is an experimental layer on top of Hermes Agent to organize profiles as an AI team.

## Layers

1. **SOUL.md** — defines identity, mission, and boundaries for each profile.
2. **agents.yaml** — team registry: agent name, profile, role, permissions, and skills.
3. **CLI `hermes agents`** — control surface to list agents, output JSON, initialize registry, handoff to Kanban, and open the dashboard.
4. **Dashboard** — terminal visualization with Rich and plain text fallback.
5. **Kanban** — durable handoff layer between profiles.

## Main implementation files

- `hermes_cli/agents.py`
- `hermes_cli/agents_dashboard.py`
- `hermes_cli/subcommands/agents.py`
- Changes to `hermes_cli/main.py` (parser/command registration — see the patch)
- Tests in `tests/hermes_cli/test_agents_cli.py`

## Note

This repository does **not** replace the official Hermes Agent. It documents and packages the Hermes Agents project as a customization/experiment.
