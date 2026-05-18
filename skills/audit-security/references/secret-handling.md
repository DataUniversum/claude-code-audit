# Secret handling

Secrets must never enter prompts, logs, committed files, or session artifacts.

## Inspect

- `CLAUDE.md` and `CLAUDE.local.md` for inline secrets
- `.claude/settings.json` and `settings.local.json` env var references
- `.env` files in the project (and whether they're gitignored)
- credentials in `.mcp.json`
- credentials referenced by scripts invoked from Claude Code
- generated session artifacts and logs for accidental secret capture
- transcripts for prompts containing keys or tokens
- git history (light scan, not deep) for accidentally committed secrets

## Flag

- API keys, tokens, or passwords visible in any committed file
- secrets in `CLAUDE.md` (loaded into every session)
- `.env` not in `.gitignore`
- `settings.local.json` not in `.gitignore`
- MCP server credentials embedded in `.mcp.json` rather than env vars
- env vars containing secrets logged by Claude Code transcripts
- scripts that `echo $SECRET` or print env to stdout
- prompts that include the value of a secret rather than instructing Claude to use a tool that has the secret
- secrets passed as inline `-p "..."` arguments to `claude` (visible in process listings and shell history)
- session artifacts containing prompts that include credentials
- generated logs in committed directories

## Recommend

- secrets in env vars, loaded from `.env` (gitignored) or a secrets manager
- `.env`, `.envrc`, `settings.local.json`, and similar in `.gitignore`
- MCP servers reference secrets via env vars, not inline values
- never embed a secret in a prompt — give Claude a tool that uses the secret instead
- redaction of secrets in any logging or transcript output
- audit `CLAUDE.md` and skill bodies for any hardcoded credentials
- generated session/output/log directories gitignored
- if secrets have leaked into committed files or session artifacts, rotate them before doing anything else
