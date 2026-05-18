# Invocation mechanisms

Decision tree for how to invoke a subagent or downstream step.

## Mechanisms

| Mechanism | Properties | Cost |
|---|---|---|
| Task tool | inherits parent context, harness-tracked, same session | no context reload |
| Nested `claude` CLI | new session, blind to parent, can run headless or in parallel | full reload of `CLAUDE.md`, agent def, any skills used |
| Script subprocess | deterministic, no model | negligible |
| Hook | runs automatically at lifecycle events, no model | negligible to small |
| MCP server tool | external service, structured I/O | depends on server |

## Decision tree

1. Is this deterministic logic? → script.
2. Does this run at a lifecycle event regardless of agent reasoning? → hook.
3. Does this need an external system? → MCP server tool.
4. Does this need a model? Continue.
5. Does this need true context isolation (blind deliberation, sandboxed work, headless parallel execution outside Claude Code)? → nested `claude` CLI.
6. Otherwise → Task tool.

## Flag

- nested `claude` CLI used where the Task tool would suffice
- Task tool used where true isolation is required
- inconsistency between agent `tools:` frontmatter and `--allowedTools` CLI argument
- nested calls reloading a large `CLAUDE.md` repeatedly without isolation benefit
- long inline `-p "..."` strings (>200 chars) — pass a file path instead
- shell-escaping risk in inline prompts
- model invocations doing deterministic work
- hooks doing work that belongs in an agent
- MCP tools called when a script would do

## Estimate

Context reload cost: `CLAUDE.md tokens × N nested calls per workflow`. If this exceeds the useful payload, the architecture is paying for isolation it does not use.

## Recommend

- Task tool by default
- nested `claude` CLI only when isolation, parallelism, or headless execution genuinely requires it
- scripts for deterministic work
- pass prompts as file paths to nested CLI, never inline beyond ~200 chars
- one source of truth for tool allowlists (frontmatter or CLI flag, not both)
