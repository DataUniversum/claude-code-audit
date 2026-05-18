# Agent and skill architecture

## 3a. Anti-patterns to flag

- too many agents for one workflow
- every agent participating in every phase
- agents rereading the same files
- agents reading other agents' full outputs
- N agents × N outputs crossfire patterns
- expensive models used for intermediate agents
- agents doing deterministic work that scripts should do
- skills containing orchestration that should be a script
- slash commands containing too much workflow logic
- subagents with long duplicated instructions
- agents with description too vague to disambiguate from other agents (causes wrong auto-selection by the Task tool)
- agents meant to be orchestrator-only that lack a `Never invoke directly` guard in their description
- agent `tools:` frontmatter not restricted to what the agent actually needs (broad tool access by default)
- subagent prompts containing inlined file contents instead of file paths (the subagent should Read the path itself)

## 3b. Subagent invocation mechanism

Two mechanisms exist for invoking subagents inside Claude Code:

| Mechanism | When correct | Cost |
|---|---|---|
| Task tool | most subagent work; subagents inherit parent context, harness-tracked | none — no context reload; runs in same session |
| Nested `claude` CLI (`claude --model ... --agent ... -p "..."`) | true isolation (blind deliberation, sandboxed work), parallel headless execution, workflows running outside Claude Code | each call is a full new session that reloads CLAUDE.md, agent definition, any skills it invokes |

Flag:

- nested `claude` CLI calls used where the Task tool would suffice (no isolation requirement)
- Task tool used where true isolation is required (e.g., blind deliberation, parent-context contamination)
- inconsistency between agent `tools:` frontmatter and `--allowedTools` argument in CLI calls — pick one source of truth
- nested calls that reload a large `CLAUDE.md` repeatedly without isolation benefit
- long inline `-p "..."` strings (>~200 characters) constructing prompts on the command line — pass a file path instead
- shell-escaping risk in inline prompts (quotes, newlines, special chars) when the same content could be a file

Estimate the context reload cost: `CLAUDE.md size (approx tokens) × number of nested calls per workflow`. If this exceeds the useful payload, the architecture is paying for isolation it does not use.

## 3c. Multi-agent execution efficiency

Flag:

- parallel agent invocations issued sequentially (separate messages, or sequential bash calls separated by waits) when they could run in parallel (single message with multiple tool blocks, or simultaneous `&` background jobs)
- agents reading every other agent's full output (N×N read explosion) instead of compact structured summaries
- final synthesis agent receiving every raw intermediate artifact instead of a compact evidence bundle
- duplicated background context in every agent prompt (should live in the agent definition, loaded once per agent)
- subagent prompts that re-state the task background that is already in the agent's system prompt

Estimate per workflow execution:

- count of nested model calls
- approximate total tokens loaded across all subagent context reloads
- whether the same files are read by N different agents (and whether one read + compact summary would replace N rereads)

## 3d. Skill consolidation

When a project has multiple skills with overlapping purposes (e.g., several synthesis-variant skills, several formatter-variant skills), evaluate whether:

- they share enough structure to merge or share a base skill
- each variant's SKILL.md file is loaded into context on every invocation, multiplying load cost
- one parameterized skill could replace N variants

Recommend:

- compact/default mode
- deep mode only when explicitly requested
- fewer agent phases
- compact decision matrices
- one strong final synthesis step
- role-specific but short agent instructions
- scripts for deterministic orchestration
- phase outputs with strict schemas
- phase handoff through compact artifacts rather than raw essays
- Task tool for subagents that do not require true context isolation
- nested `claude` CLI only when isolation, parallelism, or headless execution genuinely requires it
- consolidate skill variants when the differences are small enough to be parameters
- pass file paths to subagents — never inlined file contents
- restrict each agent's `tools:` to the minimum it actually uses
