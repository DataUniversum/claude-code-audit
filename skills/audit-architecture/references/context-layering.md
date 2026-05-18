# Context layering

What belongs in which layer.

## Layers

1. `CLAUDE.md` — project-wide rules that apply to every Claude Code session. Should be small. Should describe the repo, conventions, and entry points only.
2. `CLAUDE.local.md` — local-only overrides, never committed. Private paths, sandbox URLs, machine-specific notes.
3. `.claude/rules/` — modular rules with YAML frontmatter, optionally path-scoped. For rules that apply only to certain directories or file types.
4. Skills — reusable workflow logic. Triggered by description match. SKILL.md should route to `references/` for detail.
5. Slash commands — user-facing entry points. Thin dispatchers. Should not contain workflow logic.
6. Subagent definitions — role-specific behavior, loaded once per agent invocation. Background context that an agent always needs belongs here, not in prompts.
7. External context files — long or task-specific context referenced by path, loaded on demand.
8. Scripts — deterministic orchestration, file I/O, computation. No model calls inside the deterministic parts.
9. Compact handoff artifacts — structured summaries between phases.

## Flag

- `CLAUDE.md` containing workflow-specific rules
- `CLAUDE.md` containing task-specific context
- rules duplicated across `CLAUDE.md`, agents, commands, and skills
- persona instructions copied into many agent files when they could live in one rule file
- long generated files repeatedly reread because no compact summary exists
- skills containing orchestration logic that should be a script
- slash commands containing workflow logic that should be a skill
- subagent prompts re-stating background that is already in the agent's system prompt
- workflow-specific rules placed globally
- background context inlined into prompts instead of referenced by path

## Decision questions

For each piece of context, ask:

- does this apply to every session, or only some workflows?
- does this change per invocation, or is it stable?
- is this judgment (model) or fixed logic (script)?
- is this loaded once per session, once per agent, or once per call?

If it applies to every session and is stable → `CLAUDE.md` or a global rule.
If it applies to one workflow → skill or command.
If it applies to one role within a workflow → subagent definition.
If it changes per invocation → prompt or argument.
If it is fixed logic → script.

## Recommend

- minimum viable `CLAUDE.md`
- workflow-specific context inside the skill or command that owns the workflow
- path-scoped rules for directory-specific conventions
- compact handoff artifacts between phases instead of full transcripts
- on-demand loading via reference files
