# Evolvability

How easy it is to add a new workflow, agent, phase, or rule.

## What to look for

- central configuration for models, paths, budgets, phases
- naming conventions consistent enough that a new file is named obviously
- clear ownership: each rule, each piece of context, lives in exactly one place
- documentation that explains how to add things, not just what exists
- tests or dry-run modes that let changes be validated cheaply

## Flag

- adding a new workflow requires editing many files in many directories
- no central config — model names, paths, budgets scattered across files
- inconsistent naming patterns (verb-first commands mixed with noun-first commands; snake_case mixed with kebab-case)
- references in `CLAUDE.md` or agent prompts to old/renamed files
- agent descriptions that name another agent by a stale name
- duplicated rules across multiple `CLAUDE.md` or context files
- generated files mixed with source files in the same directories
- workflow logic spread across commands, skills, and agents with no clear ownership
- no dry-run mode, so changes can only be validated by running the full workflow
- no resumability, so partial failures require starting over
- no way to estimate execution size before running

## Decision questions

- if I wanted to swap the synthesis model, how many files would I edit?
- if I renamed an agent, how many places would I need to update?
- if I added a new phase, where does it go and how does it hook in?
- can a new contributor add a workflow without reading the whole tree?

## Recommend

- central config file (`.claude/config.json` or similar) for models, paths, budgets, phase definitions
- kebab-case throughout
- match filename to frontmatter `name:` field exactly
- match slash command name to invoked skill name when 1:1
- consistent naming pattern across the project
- one place for each rule
- generated outputs separated from source by directory
- dry-run mode in every orchestration script
- resumable phases with status stored in metadata
- a brief contributor doc that explains how to add a workflow, agent, skill, rule
