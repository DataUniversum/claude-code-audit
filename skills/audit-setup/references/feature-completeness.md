# Claude Code feature-completeness checks

Assess whether the project uses relevant Claude Code features correctly. Do not treat missing features as automatic problems. Only recommend them when they would improve quality, safety, performance, maintainability, context management, or usage efficiency.

Check:

- `CLAUDE.local.md` for local-only configuration, private paths, sandbox URLs, or machine-specific notes
- `.claude/rules/` and `~/.claude/rules/`
- modular rule files with YAML frontmatter
- path-scoped rules using `paths:`
- slash command frontmatter:
  - `description`
  - `allowed-tools`
  - `argument-hint`
  - model override if used
- slash command argument handling:
  - `$ARGUMENTS`
  - `$1`, `$2`, etc.
- skill frontmatter:
  - `name`
  - `description`
  - `allowed-tools`
- skill trigger/discovery quality
- subagent frontmatter:
  - `name`
  - `description`
  - `tools`
  - `model`
  - `permissionMode`
  - `skills`
  - `agentId` or resumability fields where applicable
- whether subagent descriptions are specific enough for correct invocation
- whether proactive language such as "Use PROACTIVELY" or "MUST BE USED" is used only when appropriate
- project MCP config in `.mcp.json`
- user MCP config in `~/.claude.json` under `mcpServers`
- settings fields:
  - permissions allow list
  - permissions deny list
  - sandbox configuration
  - environment variables
  - attribution
  - default permission mode
- hooks, when present:
  - PreToolUse
  - PostToolUse
  - UserPromptSubmit
  - Stop
  - SubagentStop
  - SessionStart
  - SessionEnd
  - Notification
  - PreCompact
  - PermissionRequest, if supported
- plugins:
  - enabled plugins
  - disabled plugins
  - plugin-provided commands, agents, and skills
  - extra known marketplaces
- output styles:
  - `.claude/output-styles/`
  - `~/.claude/output-styles/`
  - `keep-coding-instructions`
- checkpointing / rewind support, if available
- headless mode scripts:
  - `claude -p`
  - `--output-format json`
  - `--output-format stream-json`
  - `--allowedTools`
  - `--disallowedTools`
  - `--mcp-config`
  - `--resume`
  - `--continue`
- status line configuration:
  - status line script
  - model display
  - cost/usage display
  - context display
  - git branch display
- IDE and terminal integration only when relevant to the project workflow

For each missing or weak feature, classify it as:

- required
- useful
- optional
- not relevant

Do not recommend adding features just to maximize a score. The audit should explain why a feature matters for this project, or explicitly mark it not relevant.
