# Contributing to claude-code-audit

Thanks for your interest in contributing.

## Ways to contribute

- **Report a bug** — open a [bug report](https://github.com/DataUniversum/claude-code-audit/issues/new?template=bug_report.yml)
- **Suggest an improvement** — open a [feature request](https://github.com/DataUniversum/claude-code-audit/issues/new?template=feature_request.yml)
- **Propose a new skill** — open a [new skill issue](https://github.com/DataUniversum/claude-code-audit/issues/new?template=new_skill.yml)
- **Submit a PR** — fork, branch, and open a pull request

## Which skill to run

`audit-setup` is the generic, broad-coverage audit and the recommended starting point. The other skills are specialists that go deeper on one dimension:

- `audit-architecture` — structural design
- `audit-security` — security posture

More specialists may be added over time (`audit-performance`, `audit-cost`, etc.). When filing a bug or feature request, please mention which skill you ran first — usually that'll be `audit-setup`.

## Setup

No dependencies beyond Claude Code.

```bash
git clone https://github.com/DataUniversum/claude-code-audit
cd claude-code-audit
```

Test the plugin against any project:

```bash
cd /path/to/target-project
claude --plugin-dir /path/to/claude-code-audit
```

Test with local fixtures by adding a project to `sandbox/` (gitignored):

```bash
mkdir sandbox/my-test-project
cd sandbox/my-test-project
claude --plugin-dir /path/to/claude-code-audit
```

## Skill structure

Each skill lives in `skills/<skill-name>/SKILL.md`. Required frontmatter:

```yaml
---
name: audit-<context>
description: <one sentence — Claude reads this to decide when to invoke it>
---
```

Skills follow the `audit-<context>` naming pattern — e.g., `audit-setup`, `audit-security`, `audit-performance`. `skills/` at repo root is the single source of truth. `.claude/` is gitignored and not part of the plugin.

## PR checklist

- [ ] Skill frontmatter has `name:` and `description:`
- [ ] Skill directory name matches `name:` in frontmatter
- [ ] `README.md` skills table updated if adding a new skill
- [ ] Example output or test evidence included in PR description
- [ ] No `.claude/` contents in the commit

## Branch protection

Direct pushes to `main` are disabled. All changes require a PR with at least one review.
