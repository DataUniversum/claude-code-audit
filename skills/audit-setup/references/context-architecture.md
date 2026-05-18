# Context architecture

Check whether context is placed in the right layer.

Recommended layering:

- project-wide rules in `CLAUDE.md`
- reusable workflow logic in skills
- user-facing workflows in slash commands
- role-specific behavior in subagent definitions
- long or task-specific context in external files
- deterministic orchestration in scripts
- compact handoff context between workflow phases

Flag:

- huge always-loaded context
- duplicated rules across agents, commands, or skills
- repeated project background in prompts
- persona instructions copied into many places
- workflow-specific rules placed globally
- task-specific context placed in `CLAUDE.md`
- long generated files repeatedly reread
- lack of compact summaries between phases
- generated session files becoming part of future context
- instructions that force Claude Code to reread files unnecessarily

Recommend when to use:

- `CLAUDE.md`
- skill files
- slash commands
- subagent definitions
- external context files
- generated compact summaries
- scripts
- hooks
