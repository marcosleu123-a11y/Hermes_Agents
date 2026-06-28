# Installation / Applying the Patch

This project is distributed as a patch over Hermes Agent.

## Apply patch

```bash
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
git apply ../Hermes_Agents/patches/0001-feat-add-hermes-agents-dashboard.patch
```

## Test

```bash
venv/bin/python -m pytest tests/hermes_cli/test_agents_cli.py tests/hermes_cli/test_kanban_core_functionality.py -q -o 'addopts='
```

## Smoke tests

```bash
hermes agents --help
hermes agents
hermes agents list --json
hermes agents dashboard --help
hermes agents dashboard --no-detail
hermes agents orchestrate 'final test with "quotes" and $HOME' --no-create
```
