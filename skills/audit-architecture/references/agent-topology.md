# Agent topology

Shape of the call graph between agents.

## Common patterns

- **Single agent** — one role does everything. Simplest, often correct.
- **Orchestrator + workers** — one agent routes work to specialized agents. Workers do not call each other.
- **Fan-out + fan-in** — orchestrator dispatches N parallel workers; results aggregated by a synthesis step.
- **Pipeline** — phase 1 → phase 2 → phase 3, each phase a different agent.
- **Debate / crossfire** — agents read each other's outputs and respond. Expensive; rarely the right shape.
- **Recursive** — an agent invokes another instance of itself. Almost always a smell.

## Flag

- pipelines with more than 4 phases — likely over-decomposed
- fan-out with more than 6 workers — usually over-parallelized
- every agent participating in every phase
- workers that call other workers without going through the orchestrator
- agents whose only job is to call another agent
- crossfire patterns where each of N agents reads each of N outputs (N×N reads)
- agents meant to be orchestrator-only that lack a `Never invoke directly` guard in their description
- agents whose role overlaps significantly with another agent in the same project
- nesting deeper than 2 levels (parent → child → grandchild)
- recursive invocation
- a synthesis agent that receives every raw worker output instead of a compact evidence bundle

## Decision questions

- can this be a single agent with branching logic?
- does the parallelism actually save wall-clock time, or just multiply calls?
- could two workers be merged into one with a longer prompt?
- is the orchestrator doing anything a script could do better?

## Recommend

- start with single agent; split only when the agent prompt becomes unmanageable or when isolation is required
- prefer fan-out + fan-in over crossfire
- cap parallel workers at the number where wall-clock gain exceeds setup cost
- orchestrator routes; orchestrator does not do work
- synthesis agent receives compact summaries, not raw transcripts
