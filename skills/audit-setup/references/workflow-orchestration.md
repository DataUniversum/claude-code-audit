# Workflow orchestration

Flag workflows where Claude Code is manually orchestrating what should be automated.

Look for:

- long inline Bash commands
- repeated generated command strings
- manually constructed paths
- no central config
- missing defaults file
- missing error handling
- no resumability
- no dry-run mode
- no phase status tracking
- no idempotency
- automatic retries without a usage budget
- no stop points between expensive phases
- no stepwise mode
- no way to run only one phase
- no way to resume after failure
- no way to estimate execution size before running

Recommend:

- small scripts invoked by short commands
- central config for models, phases, paths, and budgets
- resumable phases
- explicit `--compact`, `--deep`, `--stepwise`, and `--dry-run` modes
- no automatic retries unless enabled
- budget checks before expensive phases
- phase status stored in metadata
- short Claude-facing commands, with complexity hidden in deterministic scripts
