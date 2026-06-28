# Verification

## Automated tests

Validated command on the local Hermes repo:

```bash
venv/bin/python -m pytest tests/hermes_cli/test_agents_cli.py tests/hermes_cli/test_kanban_core_functionality.py -q -o 'addopts='
```

Validated result:

```
210 passed
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

## Verified behaviors

- `hermes agents --help` lists `list`, `init`, `orchestrate`, `dashboard`.
- `hermes agents` shows configured agents.
- `hermes agents list --json` returns valid JSON.
- `hermes agents dashboard --no-detail` renders a table with Kanban summary.
- `default` appears as fallback.
- `orchestrate` uses safe quoting with `shlex.quote`.
- Dashboard exits with `q` and `Esc`.
- `Esc` in detail view returns to dashboard.
- Kanban summary shows tasks by status when Kanban DB exists.
- Kanban summary shows friendly message when no tasks or no Kanban DB.
- Kanban read failure does not break the dashboard.
