# Architecture audit report format

Save to `.claude/audits/audit_architecture_<timestamp>.md`.

Timestamp: `YYYYMMDD_HHMMSS`.

## Structure

# Executive Summary

- Overall architecture rating:
- Audit focus used:
- Report file:
- Biggest structural problem:
- Biggest abstraction-boundary problem:
- Biggest handoff problem:
- Fastest safe structural win:

# Architecture Decision Map

Text diagram of the current topology. Then the recommended topology. Highlight differences.

Format:

```
Current:
  /command-name
    → skill: skill-name
      → agent: orchestrator
        → agent: worker-1
        → agent: worker-2
        → agent: synthesizer (reads raw outputs of worker-1, worker-2)

Recommended:
  /command-name
    → script: prepare.sh (deterministic setup)
    → skill: skill-name
      → agent: orchestrator
        ‖ agent: worker-1  (writes summary-1.json)
        ‖ agent: worker-2  (writes summary-2.json)
      → agent: synthesizer (reads summary-1.json + summary-2.json)
    → script: finalize.sh (writes report)
```

# Architecture Decision Points

Major design choices found in this setup that may be intentional. Format:

- **Decision:** <description>
  - **Apparent intent:** <best guess at why>
  - **Cost:** <what it costs structurally>
  - **Alternative:** <what would change if reversed>
  - **Verify with developer:** yes / no

# Findings

For each finding:

## Finding N — Title

- Severity: critical / high / medium / low
- Category: context-layering / agent-topology / handoff / invocation / abstraction / determinism / composability / evolvability
- Confidence: high / medium / low
- Evidence:
- Why it matters structurally:
- Quality risk if changed: none / low / medium / high
- Improvement options:
- Developer assessment needed: yes / no
- Suggested change:
- Files likely affected:

# Quick Wins

Structural changes possible in under 30 minutes (renames, moves, single-file edits).

# Deeper Refactor

Structural changes that take more work (topology reshaping, consolidation, central config introduction).

# Recommended Architecture

Describe the improved setup. Include:

- context layering
- agent topology
- handoff contracts
- invocation mechanism choices
- abstraction boundaries
- determinism boundary
- composability primitives
- evolvability provisions

# Unknowns

What could not be verified.

# Developer Assessment Notes

Recommendations that require a developer decision before implementation.

# Suggested Next Audit

Recommend the next most useful audit (e.g., the general `audit-setup` for usage cost, or `audit-security` for security).

# Report Save Status

- Report file path:
- Save status:
- Notes:
