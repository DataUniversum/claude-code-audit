# claude-code-audit

This repo is a collection of distributable Claude Code audit skills, packaged as an official Claude Code plugin.

## Skills overview

- `audit-setup` — broad/generic audit, default entry point.
- `audit-architecture` — specialist, deeper on structural design.
- `audit-security` — specialist, deeper on security posture.
- Future audits will follow the same pattern (`audit-performance`, `audit-cost`, etc.).

Recommended order: run `audit-setup` first for breadth; follow up with a specialist only if a dimension needs depth. Keep this disambiguation in `README.md` and `.github/CONTRIBUTING.md` in sync when adding new specialists.

## Repo layout

```
claude-code-audit/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest — name, description, author
├── skills/                  # Plugin artifacts (what gets distributed)
│   ├── audit-setup/
│   │   ├── SKILL.md
│   │   └── references/      # Topic-specific reference docs loaded on demand
│   ├── audit-architecture/
│   │   ├── SKILL.md
│   │   └── references/
│   └── audit-security/
│       ├── SKILL.md
│       └── references/
├── .github/                 # Community health files + issue templates
├── sample_reports/          # Anonymised example audit reports (committed)
├── sandbox/                 # Gitignored — local test fixtures
├── .claude/                 # Gitignored — local contributor config only
├── CLAUDE.md                # Shared project conventions (this file, committed)
├── CLAUDE.local.md          # Gitignored — personal/machine-specific overrides (optional)
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
   version: 0.1.0
   ---
   ```
2. Update the skills table in `README.md` (description + version columns).
3. Bump the skill's `version:` on each meaningful change (semver). The plugin's `.claude-plugin/plugin.json` version is separate — bump it on release.

## Naming conventions

- All names: kebab-case
- Skills and commands follow the pattern `audit-<context>` — e.g., `audit-setup`, `audit-architecture`, `audit-security`
- Skill directory name must match `name:` in SKILL.md frontmatter exactly
- Slash command filename must match the skill it invokes (1:1 mapping)
- Audit reports are saved as `.claude/audits/audit_<name>_<timestamp>.md` in the audited project

## What not to commit

- `.claude/` — entire directory is gitignored (machine-specific permissions, generated audit reports that may contain sensitive project context)
- `CLAUDE.local.md` — personal/machine-specific overrides for this repo; gitignored. Use for things like local sandbox paths or experimental conventions. Keep shared conventions in `CLAUDE.md` instead.
- `sandbox/` — local test fixtures, gitignored
