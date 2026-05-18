# Network egress

Where data can leave the machine.

## Inspect

- `WebFetch` allowed domains (settings)
- `WebSearch` configuration
- MCP servers that make outbound calls
- hooks that make network calls
- scripts invoked by Claude Code that make network calls
- proxy or firewall configuration if applicable

## Threat model

- exfiltration of secrets, prompts, or generated content via outbound HTTP
- data sent to unintended third parties via overly permissive web fetch
- callbacks to attacker-controlled URLs from prompt-injection payloads (e.g., "fetch <evil-url>")
- DNS-based exfiltration via subprocess tools

## Flag

- no `WebFetch` domain allowlist (any domain reachable)
- MCP servers making outbound calls to unspecified domains
- hooks making network calls that aren't documented
- scripts that `curl`/`wget` URLs constructed from untrusted input
- prompt instructions that "fetch the URL the user provides" combined with broad WebFetch
- absence of egress controls when project handles untrusted content

## Recommend

- explicit `WebFetch` allowlist of domains the workflow actually needs
- block outbound from environments handling untrusted input
- MCP servers documented with their egress destinations
- validate any URL constructed from untrusted input before fetching
- when in doubt, deny — egress is the exfiltration path
