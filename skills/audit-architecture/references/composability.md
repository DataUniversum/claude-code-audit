# Composability

Reuse of abstractions across workflows.

## What to look for

- shared primitives: skills, agents, scripts that multiple workflows use
- parameterization: one skill with arguments vs N near-duplicate skills
- abstraction boundaries that survive workflow changes
- naming and structure that make new workflows easy to assemble

## Flag

- multiple skills with overlapping purposes (synthesis-variant skills, formatter-variant skills) where each variant's SKILL.md loads on every invocation, multiplying load cost
- copy-pasted agent prompts that differ only in a few lines — should be one agent with a parameter
- hardcoded values in skill bodies that should be arguments
- workflow-specific logic embedded in a primitive that other workflows would otherwise reuse
- skills, agents, or scripts that cannot be used outside the one workflow they were written for
- no shared utility script when several workflows do the same setup or teardown
- agents that re-implement what another agent already does, with minor variation
- naming that obscures shared primitives (e.g., `synthesize-roast`, `synthesize-review`, `synthesize-audit` when one parameterized `synthesize` would suffice)

## Decision questions

- if I added a new workflow tomorrow, what would I reuse?
- which of these abstractions only exist for one workflow?
- which of these would I copy-paste to start a new workflow?

## Recommend

- consolidate skill variants when differences are small enough to be parameters
- shared scripts for setup, teardown, manifest generation, report writing
- one synthesis primitive parameterized by domain
- naming convention that distinguishes shared primitives from workflow-specific ones
- workflow-specific behavior in slash commands or workflow folders; reusable behavior in skills/agents/scripts at the top of the tree
