# claude-code-audit

A Claude Code plugin with audit skills for inspecting Claude Code project setups.

## Available skills

| Skill | Description | Version | Status |
|---|---|---|---|
| `audit-setup` | Audits a project's Claude Code configuration — context architecture, CLAUDE.md correctness, agents, skills, commands, tool selection, performance, usage-limit efficiency, and privacy. Writes a timestamped Markdown report. | 1.0.0 | stable |
| `audit-architecture` | Audits the structural design — context layering, agent topology, handoff contracts, abstraction boundaries (skill vs command vs agent vs script), determinism boundary, composability, and evolvability. Deeper on structure than `audit-setup`. | 1.0.0 | stable |
| `audit-security` | Audits security posture — permission model, tool allowlists, MCP trust boundaries, prompt-injection surface, secret handling, hook safety, runtime artifact privacy, supply chain, headless/CLI risk, network egress. | 1.0.0 | stable |

### Which skill should I use?

**Start with `/audit-setup`.** It's the generic, broad-coverage audit and the recommended entry point for any project.

If a specific dimension needs deeper coverage, follow up with a specialist:

- `/audit-architecture` — structural design (context layering, agent topology, abstraction boundaries, evolvability)
- `/audit-security` — security posture (permissions, MCP trust, prompt injection, secrets, supply chain)

More specialist audits will be added over time (e.g. `audit-performance`, `audit-cost`). The pattern is always the same: run `audit-setup` first for breadth, then a specialist if the surface warrants depth.

## Install

You can use this in two ways. Pick whichever fits your setup.

### 1. Via `git clone` + `--plugin-dir`

Clone the repo anywhere, then point Claude Code at it when opening a target project:

```
git clone https://github.com/DataUniversum/claude-code-audit.git
cd /path/to/your-project
claude --plugin-dir /path/to/claude-code-audit
```

This keeps the skills out of the target project's tree — useful when you don't want to commit them.

### 2. Direct copy into a project

Copy individual skill folders into the target project's `.claude/skills/` directory:

```
cp -r /path/to/claude-code-audit/skills/audit-setup        /path/to/your-project/.claude/skills/
cp -r /path/to/claude-code-audit/skills/audit-architecture /path/to/your-project/.claude/skills/
cp -r /path/to/claude-code-audit/skills/audit-security     /path/to/your-project/.claude/skills/
```

Use this when you want the skills versioned inside the target project itself.

Then use `/audit-setup`, `/audit-architecture`, or `/audit-security` inside the Claude Code session.

## Usage

```
/audit-setup
/audit-setup focus on context architecture and CLAUDE.md correctness
/audit-setup check usage-limit controls and model routing

/audit-architecture
/audit-architecture focus on agent topology and handoff contracts

/audit-security
/audit-security focus on MCP trust boundaries and prompt-injection surface
```

Each skill writes a full audit report to `.claude/audits/audit_<name>_<timestamp>.md` in the audited project (e.g. `audit_setup_2026-05-18T14-22-00Z.md`).

## Sample output

The `sample_reports/` directory contains two anonymised audit reports generated on this repo itself during initial plugin development — one at the start (rating 2/5, critical blockers found) and one after those blockers were resolved (rating 4/5, only low-severity issues remaining).

### Run 1 — first audit complete

Audit complete, critical findings identified (no manifest, no `skills/` directory, no `.gitignore`):

![Run 1 result](sample_reports/img/audit_run_1_result.png)

Full report: [`sample_reports/audit_setup_report_sample_1.md`](sample_reports/audit_setup_report_sample_1.md)

### Run 2 — follow-up audit

Pre-report analysis showing progress: all critical/high findings resolved, new low-severity findings surfaced:

![Run 2 analysis](sample_reports/img/audit_run_2_analysis.png)

Executive summary with findings table:

![Run 2 result](sample_reports/img/audit_run_2_result.png)

Full report: [`sample_reports/audit_setup_report_sample_2.md`](sample_reports/audit_setup_report_sample_2.md)

---

## Contributing

See [CONTRIBUTING.md](.github/CONTRIBUTING.md). All changes go through PRs — direct pushes to `main` are disabled.

## License

MIT — see [LICENSE](LICENSE).
