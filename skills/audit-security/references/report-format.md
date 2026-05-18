# Security audit report format

Save to `.claude/audits/audit_security_<timestamp>.md`.

Timestamp: `YYYYMMDD_HHMMSS`.

## Structure

# Executive Summary

- Overall security rating: critical-issues / hardening-needed / acceptable / strong
- Audit focus used:
- Report file:
- Most exposed surface:
- Most critical finding:
- Fastest hardening win:
- Highest-priority follow-up:

# Threat Model

Brief description of:

- what this Claude Code setup does
- what data it touches
- what untrusted inputs reach it
- who/what could plausibly attack it
- what the worst-case impact would be

# Exposed Surfaces

Table of surfaces inspected and their status:

| Surface | Status | Notes |
|---|---|---|
| Permission model | ok / weak / exposed |  |
| Tool allowlists | ok / weak / exposed |  |
| MCP trust | ok / weak / exposed / n/a |  |
| Prompt injection | ok / weak / exposed |  |
| Secret handling | ok / weak / exposed |  |
| Hook safety | ok / weak / exposed / n/a |  |
| Runtime artifact privacy | ok / weak / exposed |  |
| Supply chain | ok / weak / exposed / n/a |  |
| Headless / CLI | ok / weak / exposed / n/a |  |
| Network egress | ok / weak / exposed |  |

# Findings

For each finding:

## Finding N — Title

- Severity: critical / high / medium / low / informational
- Surface:
- Confidence: high / medium / low
- Exploitability: now / requires-conditions / theoretical
- Threat: <what an attacker could do>
- Impact: <what is lost or compromised>
- Evidence: <file / config / pattern>
- Recommended fix:
- Recommended detection (logging, monitoring):
- Files likely affected:
- Hardening options for developer assessment:

# Quick Hardening Wins

Changes possible in under 30 minutes.

For each:

- Change:
- Why:
- Risk if not done:
- Files:

# Deeper Hardening

Structural security changes.

For each:

- Change:
- Why:
- Risk if not done:
- Files/areas:

# Secrets Status

- Secrets discovered in committed files: yes / no  (do NOT include values, only locations)
- Secrets discovered in session artifacts: yes / no
- `.env` and `settings.local.json` gitignored: yes / no
- MCP credentials in env vars: yes / no / mixed
- Recommended rotations: <list, redacted>

# Supply Chain Inventory

- Plugins enabled:
- Plugins installed but disabled:
- Marketplaces configured:
- MCP servers (project):
- MCP servers (user):
- Version pinning status:
- Provenance concerns:

# Runtime Artifact Privacy

- Generated directories in repo:
- Gitignore coverage:
- Sensitive content found (redacted summary):
- Retention policy:
- Cloud sync exposure:

# Unknowns

What could not be verified (external MCP source code, third-party plugin internals, etc.).

# Developer Assessment Notes

Decisions that depend on the project owner's risk tolerance.

# Suggested Next Audit

Recommend the next most useful audit (architecture audit, general auditor, or rerun after fixes).

# Report Save Status

- Report file path:
- Save status:
- Notes:
