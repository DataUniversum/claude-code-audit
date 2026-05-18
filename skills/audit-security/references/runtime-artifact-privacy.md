# Runtime artifact privacy

Session logs, transcripts, and generated outputs can contain prompts, credentials, PII, business-sensitive context.

## Inspect

- session/transcript directories under `~/.claude/` or `$CLAUDE_CONFIG_DIR`
- project-local generated directories: `sessions/`, `outputs/`, `logs/`, `.tmp/`, `.cache/`, workflow-specific output paths
- `.gitignore` coverage for generated artifacts
- whether generated artifacts contain raw prompts, raw tool outputs, raw transcripts
- retention policy (or lack of one)
- whether generated artifacts are reread in later sessions

## Threat model

- accidental commit of private prompts or client context to git
- secrets persisted in logs and later exposed
- session artifacts containing prompt-injection payloads silently reloaded as context
- PII or business-sensitive information leaving the local machine via cloud sync, backup, or share
- public repos containing example outputs that actually came from real users

## Flag

- generated session/output directories inside the source tree without `.gitignore` entries
- transcripts saved to committed paths
- logs containing secrets, names, negotiations, or credentials
- no separation between public examples and real user sessions
- session artifacts loaded automatically into future sessions (e.g., via SessionStart hook)
- no cleanup or retention policy
- world-readable session directories on shared machines
- session artifacts synced via Dropbox/iCloud/OneDrive without intention
- examples/ directory mixed with real-session output

## Recommend

- generated directories in `.gitignore` (e.g., `sessions/`, `outputs/`, `logs/`, `.tmp/`, `.cache/`, and workflow-specific paths)
- public examples directory separated from real-session output directory
- redaction step before any artifact is shared
- explicit retention/cleanup policy (script that prunes by age)
- local-only storage for sensitive artifacts (outside any cloud-synced folder)
- session artifacts not reloaded as future context unless explicitly intended
- `.gitignore` review part of every audit
