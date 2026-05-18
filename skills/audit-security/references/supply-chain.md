# Supply chain

Plugins, marketplaces, external skills, MCP servers from third parties — each is code/instructions running in your harness.

## Inspect

- enabled plugins
- disabled plugins (still installed)
- plugin-provided commands, agents, skills, hooks
- marketplaces configured (default + extra)
- skills installed from external sources
- MCP servers installed from external sources (covered also in `mcp-trust.md`)
- version pinning (or floating versions)

## Threat model

- a compromised plugin can install hooks, agents, skills, MCP servers
- an updated plugin can silently change behavior
- a marketplace can serve malicious packages
- external skills can contain prompt-injection payloads or destructive instructions
- a backdoored MCP server can exfiltrate every tool call

## Flag

- plugins from unknown publishers
- plugins installed at floating versions (auto-update without review)
- extra marketplaces from untrusted sources
- skills installed via copy-paste from forums/issues without review
- plugin-provided hooks (especially `SessionStart`, `UserPromptSubmit`, `PreToolUse`) without inspection
- MCP servers installed via `npx <pkg>@latest` instead of pinned versions
- no record of what's installed and why
- disabled-but-installed plugins (still present on disk; could be re-enabled accidentally)

## Recommend

- pin versions for plugins, MCP servers, external skills
- only enable plugins after reading their hooks, agents, skills
- single trusted marketplace; remove extras
- maintain an inventory of installed third-party components
- review plugin updates before accepting
- remove unused/disabled plugins entirely
- prefer plugins from publishers you can verify
