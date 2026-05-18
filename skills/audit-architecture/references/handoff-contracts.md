# Handoff contracts

What gets passed between phases.

## Forms of handoff

- **Inline message** — text continues in the same context. Cheapest, only works within one agent.
- **Tool result** — structured return value from a tool call. Schema-enforced.
- **File on disk** — phase N writes a file, phase N+1 reads it. Persists; survives session boundaries.
- **Compact summary** — phase N writes a structured summary; raw artifacts stay on disk, summary is what gets read downstream.
- **Evidence bundle** — final synthesis receives a curated set of summaries, not every raw artifact.

## Flag

- full transcripts passed between phases
- raw essay outputs passed to a synthesis step
- synthesis steps that read every intermediate artifact in full
- handoffs without a schema (free-form prose between phases)
- handoffs that re-state task background already known to the next phase
- multiple phases reading the same source file independently instead of sharing one compact summary
- handoff files written but not read, or read but not written by anyone
- handoff format that differs per phase, requiring the next phase to detect format
- generated session files that become part of future context unintentionally

## Decision questions

- what does the next phase actually need from this phase?
- can the answer fit in a structured object with named fields?
- can intermediate raw artifacts stay on disk while only the summary is loaded?
- does the synthesis step need every phase's output, or a curated bundle?

## Recommend

- define a schema for every phase output: named fields, word caps per field, optional sections
- write raw artifacts to disk, pass only summaries downstream
- synthesis step receives a single evidence bundle, not N artifacts
- one file per phase, deterministic name
- handoff format identical across phases when possible
- compact summary contract:
  ```
  Verdict: ...
  Key evidence: max 3 items, ≤30 words each
  Risks: max 60 words
  Confidence: 1–5
  Artifact path: <path to raw artifact on disk>
  ```
