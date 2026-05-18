# Tool allowlists per agent

Each subagent should have the minimum tools it needs. Not the maximum.

## Inspect

- `tools:` frontmatter in every `.claude/agents/<name>.md`
- `--allowedTools` and `--disallowedTools` in every script that calls `claude -p`
- skill `allowed-tools` frontmatter
- slash command `allowed-tools` frontmatter
- which tools are inherited from parent agent vs. overridden

## Flag

- agents without `tools:` frontmatter (inherits everything)
- agents with `tools:` listing more than the agent actually uses
- agents with Write access that only need Read
- agents with Bash access that only need specific commands
- agents with WebFetch that only need to read local files
- agents with MCP tool access not relevant to the role
- inconsistency between agent `tools:` frontmatter and `--allowedTools` CLI argument — pick one source of truth
- read-only research agents that have Edit or Write
- summarizer/synthesis agents with Bash (they should only read other phases' outputs)
- orchestrator agents with broad tools — orchestrators should mostly use Task, not work tools

## Recommend

- explicit `tools:` on every agent
- minimum scope per agent
- one source of truth for tool restrictions (frontmatter for static, CLI flag only when overriding for a specific invocation)
- read-only roles: Read, Glob, Grep
- write-capable roles: add Edit and/or Write with path scope when possible
- orchestrators: Task only, plus the bare minimum for setup
- audit/security/review agents: Read, Glob, Grep only — never Bash, never Edit
