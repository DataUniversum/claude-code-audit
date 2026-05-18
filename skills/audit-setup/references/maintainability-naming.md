# Maintainability and reuse

## 11a. General maintainability

Check whether the Claude Code setup is easy to evolve.

Flag:

- hardcoded paths
- hardcoded model names
- duplicated mode definitions
- duplicated agent names
- no central config
- missing defaults file
- missing error handling
- missing dry-run mode
- generated files mixed with source files
- workflow logic scattered across commands, skills, and agents
- no clear ownership of rules

## 11b. Naming consistency

Check that names line up across the four places they appear:

| Artifact | Filename / dir | Frontmatter `name:` | How it is invoked |
|---|---|---|---|
| Slash command | `.claude/commands/<name>.md` | (no `name:` field) | `/<name>` |
| Skill | `.claude/skills/<name>/SKILL.md` | `name: <name>` | Skill tool with `skill: "<name>"` |
| Subagent | `.claude/agents/<name>.md` | `name: <name>` | Task tool by name, or auto-selected by `description:` |

Flag:

- mismatched filename and frontmatter `name:` field — the filename is the canonical reference; the frontmatter should match
- skill directory name does not match the `name:` field in its `SKILL.md`
- agent filename does not match the `name:` field in its frontmatter
- slash command name and the skill it invokes diverging when there is a 1:1 mapping (e.g., `/audit-setup` invoking skill `audit-setup`) — pick one form and rename for consistency
- inconsistent naming patterns within the same project (e.g., some agents verb-first, others noun-first)
- snake_case mixed with kebab-case — kebab-case is the convention for slash commands, skills, and agents
- references in `CLAUDE.md`, agent prompts, or other context files to old/renamed files
- agent descriptions that name another agent by a stale name

Recommend:

- kebab-case throughout (e.g., `audit-setup`, not `auditSetup` or `audit_setup`)
- match slash command name to invoked skill name when there is a 1:1 mapping
- match filename to frontmatter `name:` field exactly
- consistent naming pattern across the project: action-verb for commands (`/review`, `/audit-setup`), role-noun for skills/agents (`audit-setup`, `roastr-facilitator`) — or pick one form across all
- search for stale references whenever a file is renamed

Recommendations for general maintainability:

- central config for models, modes, paths, budgets, and phase definitions
- small slash commands
- reusable skills
- short subagent definitions
- deterministic orchestration scripts
- generated outputs separated from source
- stable naming conventions
