# Skill vs command vs agent vs script

Which abstraction for which job.

## Definitions

- **Slash command** — user-facing entry point. Triggered by the user typing `/name`. Short. A dispatcher.
- **Skill** — reusable workflow logic with description-based triggering. Can be invoked by Claude or by the user. Should route to `references/` for detail.
- **Subagent** — role-specific behavior, invoked by orchestrator agents via Task tool or nested CLI. Has its own system prompt and tools.
- **Rule** — modular instruction with YAML frontmatter, optionally path-scoped. Applies to many contexts.
- **Script** — deterministic code, no model calls. Bash, Python, Node, etc.
- **Hook** — runs at lifecycle events (PreToolUse, Stop, etc.). Deterministic.

## Decision matrix

| Job | Use |
|---|---|
| User-facing entry point | Slash command |
| Reusable multi-step workflow that Claude should pick autonomously | Skill |
| Role with distinct system prompt, tools, model | Subagent |
| Convention that applies across many files/contexts | Rule |
| Fixed logic, no judgment | Script |
| Reaction to lifecycle event | Hook |
| Background context for a role | Subagent definition body |
| Background context for a workflow | Skill body or reference |
| Background context for the whole project | `CLAUDE.md` or a global rule |

## Flag

- slash commands containing more than a paragraph of workflow logic
- skills that exist only to be called by one slash command (1:1 mapping with no reuse) — consider merging
- subagents that exist only to wrap a single tool call — consider inlining
- skills doing orchestration that should be a script
- rules duplicating content from `CLAUDE.md`
- hooks doing work that needs model judgment
- scripts that contain prompts and call models (use a skill or agent instead)
- naming inconsistency: slash command name and the skill it invokes diverging when 1:1 — pick one form
- multiple skills with overlapping descriptions (skill triggering will be ambiguous)

## Recommend

- one entry point per user-facing workflow: slash command
- one skill per reusable workflow shape
- subagents only when a role needs different tools, model, or significantly different system prompt
- rules for cross-cutting conventions
- scripts for everything deterministic
- consolidate skill variants into one parameterized skill when the differences are small
