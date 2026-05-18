# Large-context and context-window pressure

Assess whether the setup creates large contexts, repeated contexts, or context-window pressure that can reduce speed, increase usage-limit consumption, or degrade answer quality.

Flag:

- very large `CLAUDE.md` or always-loaded memory files
- repeated project background across commands, agents, and skills
- generated session outputs reread in later phases
- agents reading full raw outputs instead of compact summaries
- long prompts embedded in shell commands
- full transcripts passed between phases
- large logs or JSON files loaded without truncation
- many files read before the actual task begins
- static reference material loaded every run instead of on demand
- duplicate context at project-level and user-level Claude configuration
- nested Claude calls where each call reloads context independently
- final synthesis steps that receive every intermediate artifact instead of a compact evidence bundle
- context that is stale, overly broad, or unrelated to the active workflow

Recommend improvement options such as:

- compact task briefs
- structured intermediate summaries
- decision matrices
- phase manifests
- on-demand context loading
- smaller `CLAUDE.md` plus workflow-specific skills/rules
- path-scoped rules
- log truncation
- generated artifact retention/ignore policy
- final synthesis evidence bundles

For each large-context finding, include:

- context source
- why it is large or repeatedly loaded
- likely impact on usage, latency, quality, or reliability
- improvement options for developer assessment
- context-size impact: high / medium / low
