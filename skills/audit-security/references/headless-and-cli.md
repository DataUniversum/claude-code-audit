# Headless and CLI risk

`claude -p` and nested CLI invocations have their own surfaces.

## Inspect

- every script that calls `claude -p` or `claude --agent ... -p`
- how prompts are passed: inline string vs file path
- shell escaping of dynamic content
- `--allowedTools` / `--disallowedTools` flags
- `--dangerously-skip-permissions` or equivalent flags
- whether output is parsed (`--output-format json`, `stream-json`)
- `--resume` and `--continue` usage
- whether headless invocations log to private or shared paths

## Threat model

- shell injection via unescaped dynamic content in `-p "..."`
- secrets visible in process listings and shell history
- prompts much larger than intended due to unintended interpolation
- bypassed permissions via `--dangerously-skip-permissions`
- resumed sessions that include stale or attacker-influenced context
- output parsed without schema, vulnerable to injection from inside the response

## Flag

- inline `-p "..."` strings longer than ~200 chars (use a file path instead)
- dynamic interpolation into `-p` arguments without escaping
- secrets in `-p` arguments (visible in `ps`, shell history, audit logs)
- `--dangerously-skip-permissions` used outside contained environments
- `--allowedTools` set permissively in headless scripts
- inconsistency between agent `tools:` frontmatter and `--allowedTools` CLI flag
- output consumed as text and `eval`/executed (catastrophic injection path)
- `--resume <id>` with attacker-influenced session IDs
- log files capturing full headless prompts including secrets

## Recommend

- pass prompts as file paths, not inline
- never include secrets in command-line arguments
- explicit `--allowedTools` set to minimum
- never `--dangerously-skip-permissions` outside an isolated sandbox
- one source of truth for tool restrictions
- parse `--output-format json` output as structured data; never `eval` it
- avoid logging full prompts; if logging is needed, redact first
