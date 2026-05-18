# Performance and runtime efficiency

Assess whether the Claude Code setup runs as fast and reliably as it reasonably can while preserving output quality.

Flag:

- workflows that run too many sequential phases
- phases that could safely run in parallel
- phases that run in parallel but create too many simultaneous Claude sessions
- long-running agent calls without progress checkpoints
- unnecessary rereading of files between phases
- repeated filesystem scans
- repeated generated command construction
- slow shell commands used where faster scripts would work
- missing caching of stable intermediate artifacts
- missing resumability after partial completion
- missing dry-run mode
- missing stepwise execution mode
- missing timeout or failure handling
- slow final synthesis caused by reading raw phase outputs instead of compact summaries
- workflows where wall-clock time increases Claude Code session pressure even if visible token output is small
- orchestration that depends on Claude reasoning instead of deterministic scripts
- runtime hotspots visible in session logs or generated artifacts

Recommend improvement options such as:

- compact intermediate artifacts
- script-based orchestration
- safe parallelization
- fewer sequential model passes
- caching stable summaries
- explicit phase checkpoints
- resumable workflows
- timeout handling
- smaller final-context bundles
- model routing for speed as well as quality
- measuring runtime per phase from available logs

For each performance finding, include:

- observed or inferred bottleneck
- affected phase or workflow
- whether the issue affects latency, usage limits, reliability, or all three
- improvement options for developer assessment
- expected runtime impact: high / medium / low
- quality risk if changed: none / low / medium / high
