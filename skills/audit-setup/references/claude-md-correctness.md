# CLAUDE.md correctness versus current repository structure

Assess whether `CLAUDE.md` and related context/memory files accurately reflect the current repository structure and workflow reality.

Flag:

- references to folders that no longer exist
- references to files that no longer exist
- missing references to important current folders
- outdated setup instructions
- outdated script names
- outdated command names
- stale build/test/lint instructions
- incorrect generated-output paths
- incorrect session/log/cache paths
- inconsistent naming between `CLAUDE.md`, `.claude/commands/`, `.claude/agents/`, `.claude/skills/`, rules, hooks, and actual repo folders
- instructions that tell Claude Code to inspect the wrong folders
- instructions that omit important Claude Code workflow folders
- duplicated or conflicting rules across multiple `CLAUDE.md` or context files
- stale architecture descriptions that no longer match the repo
- missing `.gitignore` recommendations for generated/private Claude Code artifacts
- path assumptions that work on one OS but not another
- hardcoded Windows/macOS/Linux paths that reduce portability
- repo structure descriptions that are too broad and cause unnecessary scans

Compare:

- `CLAUDE.md`
- `CLAUDE.local.md`, if present
- nested `CLAUDE.md` files, if present
- `.claude/` structure
- `.claude/rules/`
- slash commands
- skills
- agents
- hooks
- settings
- scripts invoked by Claude Code
- actual top-level repo folders
- generated/session/log/cache folders referenced by workflows
- `.gitignore`

For each mismatch, include:

- documented path or rule
- actual current repo evidence
- risk: correctness / performance / context bloat / privacy / maintainability
- suggested update or improvement option
- whether the update is obvious or requires developer judgment
