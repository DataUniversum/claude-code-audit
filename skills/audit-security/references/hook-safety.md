# Hook safety

Hooks run automatically at lifecycle events. They are elevated execution surfaces.

## Inspect

- `.claude/hooks/` directory
- hook entries in settings: `PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `Stop`, `SubagentStop`, `SessionStart`, `SessionEnd`, `Notification`, `PreCompact`, `PermissionRequest`
- what each hook does (script body)
- what inputs each hook receives
- which user/process the hook runs as

## Threat model

A malicious or buggy hook can:

- run arbitrary code at session start (effectively backdoor)
- intercept or modify tool calls (`PreToolUse`)
- exfiltrate data on every tool result (`PostToolUse`)
- inject instructions on every user prompt (`UserPromptSubmit`)
- act on attacker-controlled tool inputs (command injection)

## Flag

- hooks installed from external sources (plugins, marketplaces) without inspection
- hooks that take tool input and use it in shell commands without escaping (command injection)
- hooks that read tool output and inject it into prompts (prompt injection vector)
- hooks that write to global paths (`/etc`, `~/.ssh`, etc.)
- hooks that make outbound network calls (exfiltration potential)
- hooks running with broader privileges than the rest of Claude Code
- hooks that depend on env vars containing secrets and log them
- hooks that bypass the permission model (e.g., do work that would otherwise require user approval)
- hooks that silently modify generated outputs
- `UserPromptSubmit` hooks that rewrite prompts in non-obvious ways
- `PreCompact` hooks that drop or alter context invisibly

## Recommend

- review every hook source line by line before enabling
- hooks should be small, single-purpose, and idempotent
- no shell interpolation of tool inputs without strict escaping; prefer structured arg passing
- hooks should not make network calls unless that is their explicit purpose
- hooks that modify prompts or context must be documented and explicit
- if a hook came from a plugin or marketplace, verify provenance and pin version
- hooks should not log sensitive data
- prefer hooks that fail-safe (deny on error) over fail-open
