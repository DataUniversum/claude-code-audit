# claude-code-audit

This repo is a collection of distributable Claude Code audit skills, packaged as an official Claude Code plugin.

## Repo layout

```
claude-code-audit/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest — name, description, author
├── skills/                  # Plugin artifacts (what gets distributed)
│   └── audit-setup/
│       └── SKILL.md
├── .github/                 # Community health files + issue templates
├── sample_reports/          # Anonymised example audit reports (committed)
├── sandbox/                 # Gitignored — local test fixtures
├── .claude/                 # Gitignored — local contributor config only
├── .gitignore
├── LICENSE
└── README.md
```

## Development workflow

`skills/` at repo root is the **single source of truth** for plugin artifacts. `.claude/` is gitignored — it is local-only and never committed.

**To develop and test locally, open a target project with `--plugin-dir`:**
```
cd /path/to/target-project
claude --plugin-dir /path/to/claude-code-audit
```

**To test using local fixtures:**
Add test projects to `sandbox/` (gitignored), open them with `--plugin-dir /path/to/claude-code-audit`.

## Adding a new skill

1. Create `skills/audit-<context>/SKILL.md` with required frontmatter:
   ```yaml
   ---
   name: audit-<context>
   description: <one sentence — Claude reads this to decide when to invoke it>
   ---
   ```
2. Update the skills table in `README.md`

## Naming conventions

- All names: kebab-case
- Skills and commands follow the pattern `audit-<context>` — e.g., `audit-setup`, `audit-security`, `audit-performance`
- Skill directory name must match `name:` in SKILL.md frontmatter exactly
- Slash command filename must match the skill it invokes (1:1 mapping)

## What not to commit

- `.claude/settings.local.json` — machine-specific permissions, gitignored
- `.claude/audits/` — generated reports may contain sensitive project context, gitignored
- `sandbox/` — local test fixtures, gitignored
