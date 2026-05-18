# MCP trust boundaries

Every connected MCP server is a trust relationship. Each tool an MCP server exposes runs with the agent's permission to call it.

## Inspect

- project `.mcp.json`
- user `~/.claude.json` `mcpServers`
- which servers are local processes vs. remote URLs
- credentials each server requires
- which tools each server exposes
- which agents call which MCP tools

## Surfaces to evaluate per server

- **provenance** — who wrote the server, where it's installed from
- **transport** — local stdio process, local socket, remote URL
- **credentials** — does it hold tokens, API keys, OAuth?
- **scope** — what does it have access to (filesystem, accounts, networks)
- **trust** — first-party, vetted third-party, or unknown
- **outputs** — does it return data that flows into prompts (prompt injection vector)

## Flag

- remote MCP server with no auth or credential scoping
- MCP server that holds high-privilege credentials (admin tokens, root API keys)
- MCP server source not pinned (e.g., `npx <package>@latest` instead of a specific version)
- MCP servers with broad filesystem or network access exposed to agents that don't need them
- MCP server outputs flowing directly into prompts without sanitization (prompt injection)
- credentials in `.mcp.json` committed to git
- duplicate MCP servers configured at project and user level with different credentials
- MCP servers that return executable content (URLs, commands, file paths) consumed by agents that act on them
- no per-agent restriction on which MCP tools are usable

## Recommend

- pin MCP server versions, not floating tags
- per-server credential scoping to minimum required
- segregate high-privilege MCP servers; restrict to a single trusted agent
- treat all MCP outputs as untrusted by default — sanitize or constrain
- review every MCP server periodically; remove unused ones
- credentials in env vars or a secrets manager, never in `.mcp.json` committed to git
- per-agent `tools:` frontmatter restricting which MCP tools each agent can call
