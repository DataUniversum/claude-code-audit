# Audit report artifact

The audit should produce a saveable report artifact.

Write the full audit report to a dedicated Markdown file named:

```text
audit_setup_<timestamp>.md
```

Recommended location:

```text
.claude/audits/audit_setup_<timestamp>.md
```

Use a timestamp format that sorts naturally, such as:

```text
YYYYMMDD_HHMMSS
```

Rules for the audit report file:

- create `.claude/audits/` if it does not exist
- write the complete audit report to the file
- include the user-provided audit brief, if any
- include the project root and timestamp
- include the list of inspected locations
- include findings, evidence, risks, and improvement options
- include recommended next steps for developer assessment
- include unknowns and limitations
- do not include full private transcripts
- redact secrets and sensitive values
- do not overwrite previous reports
- if writing the file fails, return the full report in the chat and explain why the file could not be written

In the chat response after the audit, return a short summary and the path to the created report file.

## Output format

Return the audit in this exact structure, and also save it to `.claude/audits/audit_setup_<timestamp>.md`.

# Executive Summary

- Overall rating:
- Audit focus used:
- Report file:
- Biggest usage burner:
- Biggest performance bottleneck:
- Biggest large-context problem:
- Biggest CLAUDE.md/repo-structure mismatch:
- Biggest orchestration problem:
- Fastest safe win:
- Recommended default mode:

# Architecture Decision Points

Major design choices found in this setup that may be intentional. List them and ask the developer to confirm before recommending changes. Use this format:

- **Decision:** <e.g., "Nested `claude` CLI calls instead of Task tool for subagents">
  - **Apparent intent:** <e.g., "True isolation for blind deliberation">
  - **Cost:** <e.g., "CLAUDE.md reloaded on every nested call">
  - **Alternative:** <e.g., "Task tool with explicit no-context-share instruction in the prompt">
  - **Verify with developer:** yes / no

Omit this section if no clearly-intentional architecture choices are visible.

# Per-Workflow Usage Estimate

When the audited setup runs a defined workflow (slash command, multi-agent pipeline), produce a rough per-execution estimate:

- Nested model calls per execution: <N>
- Approximate context reload cost: <CLAUDE.md tokens × N nested calls> ≈ <total tokens>
- Files read repeatedly across agents: <list with counts>
- Files written per execution: <count>
- Models used: <haiku × N, sonnet × N, opus × N>

Omit this section if the project is general-purpose tooling without a defined workflow shape.

# Findings

For each finding:

## Finding N — Title

- Severity: critical / high / medium / low
- Category:
- Confidence: high / medium / low
- Evidence:
- Why it matters in Claude Code:
- Expected usage-limit impact: high / medium / low / none
- Expected runtime impact: high / medium / low / none
- Context-size impact: high / medium / low / none
- Repo-structure correctness impact: high / medium / low / none
- Quality risk if changed: none / low / medium / high
- Improvement options:
- Developer assessment needed: yes / no
- Suggested replacement text/config, if obvious:
- Files likely affected:
- Proven from runtime evidence or inferred from configuration:

# Quick Wins

List changes possible in under 30 minutes.

For each quick win:

- Change:
- Why:
- Expected impact:
- Quality risk:
- Developer assessment needed:
- Files:

# Deeper Refactor

List structural changes that may take more work.

For each refactor:

- Change:
- Why:
- Expected impact:
- Quality risk:
- Developer assessment needed:
- Files/areas:

# Recommended Claude Code Architecture

Describe the improved setup in simple terms.

Include:

- context layering
- slash command responsibilities
- skill responsibilities
- subagent responsibilities
- script responsibilities
- runtime artifact handling
- model routing
- compact vs deep mode
- performance strategy
- large-context strategy

# Claude Code Feature Completeness

Assess relevant Claude Code features without chasing a score.

Include:

- Modular rules:
- Slash command metadata:
- Skill metadata:
- Subagent metadata:
- Settings/permissions:
- Hooks:
- MCP:
- Plugins:
- Output styles:
- Headless mode:
- Status line:
- Checkpointing:
- Relevance assessment:

# Proposed Budget Policy

Give compact/default and deep-mode guardrails if relevant.

Include:

- max model calls
- max agent calls
- max files read
- max retries
- max words per intermediate output
- when stronger models may be used
- when the workflow must stop and ask before continuing

# Runtime Evidence Checked

List runtime/session/log locations checked and whether anything useful was found.

Include:

- location:
- status: checked / unavailable / skipped / too sensitive / too large
- useful evidence found:

# Unknowns

State what could not be verified.

# Developer Assessment Notes

List recommendations that require a developer or project owner decision before implementation.

# Suggested Next Audit Command

Recommend the next most useful audit command or follow-up focus.

# Report Save Status

- Report file path:
- Save status:
- Notes:
