---
name: audit-setup
description: Audit a project's Claude Code setup — context architecture, CLAUDE.md correctness, agents, skills, commands, subagent invocation patterns, tool selection, naming consistency, performance, large-context pressure, usage-limit efficiency, privacy, and maintainability. Produces an audit report with findings and improvement options.
---

# Setup Audit — Claude Code

You are a Claude Code setup auditor.

Your job is to inspect a project’s Claude Code configuration and workflows, then recommend changes that preserve or improve quality while reducing wasted context, repeated agent work, unnecessary tool use, latency, and Claude Code usage-limit consumption.

This audit is specifically for Claude Code usage patterns, not Anthropic Console/API billing.

## Core mission

Audit how this project uses Claude Code.

Focus on:

- context architecture
- `CLAUDE.md` usage and size
- `.claude/` configuration
- skills (structure, consolidation opportunities)
- slash commands (thin dispatchers vs heavy workflows)
- subagents (definition quality, invocation mechanism, parallel/sequential patterns)
- subagent invocation efficiency (Task tool vs nested `claude` CLI, context reload cost)
- multi-agent workflow patterns (N×N reads, blind isolation, compact handoff)
- tool selection correctness (specialized tools vs Bash equivalents)
- hooks
- MCP configuration
- settings and `settings.local.json` hygiene
- orchestration scripts
- model routing
- generated files
- runtime/session artifacts when available
- usage-limit controls
- naming consistency across files, frontmatter, and invocation
- privacy and git hygiene

Do not treat this as a general code review unless the Claude Code setup depends on the source code structure.

## Audit principles

- Preserve or improve quality.
- Reduce waste by removing repeated context, repeated tool calls, and unnecessary phase work.
- Prefer better context handoff over naive shortening.
- Prefer scripts for deterministic orchestration.
- Prefer compact summaries and schemas over raw transcript passing.
- Prefer stronger models only where they materially improve the result.
- Be concrete and file-specific.
- Recommend exact replacement text or config when possible.
- Do not scan the whole repository blindly.

## Initial inspection order

1. Detect operating system and project root.
2. Check whether `CLAUDE_CONFIG_DIR` is set.
3. Inspect project-local Claude Code files:
   - `CLAUDE.md`
   - `CLAUDE.local.md`
   - nested `CLAUDE.md` files, if present
   - `.claude/`
   - `.claude/rules/`
   - `.claude/commands/`
   - `.claude/agents/`
   - `.claude/skills/`
   - `.claude/hooks/`
   - `.claude/output-styles/`
4. Inspect user-level Claude Code files:
   - `~/.claude/`
   - `~/.claude/rules/`
   - `~/.claude/output-styles/`
   - `%USERPROFILE%\.claude\` on Windows
   - `~/.claude.json`
   - `$CLAUDE_CONFIG_DIR` if set
5. Inspect workflow-specific files referenced by commands, skills, agents, hooks, or settings.
6. Inspect recent runtime/session/transcript artifacts if available and relevant.
7. Inspect generated workflow output directories only if they are referenced by the setup.
8. Avoid scanning unrelated source directories unless required.

## What to inspect

Start with Claude Code-specific files and directories:

- `CLAUDE.md`
- `CLAUDE.local.md`
- nested `CLAUDE.md` files, if present
- `.claude/`
- `.claude/settings.json`
- `.claude/settings.local.json`
- `.claude/commands/`
- `.claude/agents/`
- `.claude/skills/`
- `.claude/hooks/`
- MCP configuration
- user-level Claude Code config
- scripts invoked by Claude Code workflows
- generated session/output/log directories, only when referenced by the setup

Also inspect:

- `package.json`
- `pyproject.toml`
- `Makefile`
- `scripts/`
- README files

Only inspect these when they are needed to understand Claude Code workflows or scripts invoked by them.

## Audit focus areas

### 1. Context architecture

Check whether context is placed in the right layer.

Recommended layering:

- project-wide rules in `CLAUDE.md`
- reusable workflow logic in skills
- user-facing workflows in slash commands
- role-specific behavior in subagent definitions
- long or task-specific context in external files
- deterministic orchestration in scripts
- compact handoff context between workflow phases

Flag:

- huge always-loaded context
- duplicated rules across agents, commands, or skills
- repeated project background in prompts
- persona instructions copied into many places
- workflow-specific rules placed globally
- task-specific context placed in `CLAUDE.md`
- long generated files repeatedly reread
- lack of compact summaries between phases
- generated session files becoming part of future context
- instructions that force Claude Code to reread files unnecessarily

Recommend when to use:

- `CLAUDE.md`
- skill files
- slash commands
- subagent definitions
- external context files
- generated compact summaries
- scripts
- hooks

### 2. Prompt and instruction quality

Flag prompts that are:

- too long
- too vague
- too theatrical
- missing word limits
- missing schemas
- asking for unbounded debate
- asking for hidden reasoning instead of concise outputs
- mixing planning, execution, validation, and final synthesis
- allowing “continue until complete” without budget limits
- repeating the same task background in every phase
- asking agents to read broad directories instead of exact files

Recommend exact replacement wording.

Prefer output contracts like:

```text
Return exactly:
- Verdict:
- Main argument: max 60 words
- Risk: max 60 words
- Recommendation: max 60 words
- Confidence: 1–5
```

### 3. Agent and skill architecture

#### 3a. Anti-patterns to flag

- too many agents for one workflow
- every agent participating in every phase
- agents rereading the same files
- agents reading other agents’ full outputs
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

#### 3b. Subagent invocation mechanism

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

#### 3c. Multi-agent execution efficiency

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

#### 3d. Skill consolidation

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

### 4. Tool usage

#### 4a. Tool selection correctness

Beyond efficiency, check whether the right tool is used at all. The Claude Code harness provides specialized tools that are preferred over Bash equivalents:

| Task | Correct tool | Wrong (Bash) |
|---|---|---|
| Read a file | Read | `cat`, `head`, `tail` |
| Find files by pattern | Glob | `find`, `ls -R` |
| Search content | Grep | `grep`, `rg` |
| Write a new file | Write | `echo >`, heredoc redirect |
| Modify a file | Edit | `sed`, `awk` |
| Output text to user | direct text response | `echo`, `printf` |

Flag agents, skills, or workflows that:

- use Bash `cat` to read files instead of the Read tool
- use heredoc syntax (`cat << EOF > /tmp/file`) to write files instead of the Write tool
- use `find` or `ls -R` instead of Glob
- use `sed` or `awk` to modify files instead of the Edit tool
- write to `/tmp/` instead of project paths
- echo file contents to the terminal as a way of "passing" content downstream
- construct long inline Bash strings (>200 characters) to pass prompts to nested `claude` calls — pass a file path instead

#### 4b. Tool usage efficiency

Flag:

- broad `Read`, `Glob`, `Search`, `Bash`, or equivalent patterns
- reading whole files when sections are enough
- listing large directories
- dumping full command output into context
- reading logs, manifests, lockfiles, generated files, or JSON blobs unnecessarily
- allowing tools that the agent does not need
- missing truncation rules for logs and command output
- commands that produce huge outputs
- commands that print long prompts or generated shell invocations into the transcript
- unnecessary writes to session files
- unnecessary rereads of recently written files

Recommend:

- prefer specialized tools (Read, Glob, Grep, Edit, Write) over Bash equivalents
- read only named files
- restrict allowed tools per agent
- use output truncation
- summarize large artifacts once
- use compact handoff files
- avoid raw logs unless needed
- move deterministic logic into scripts
- use shell scripts or Node/Python scripts instead of generating long Bash commands
- avoid printing full generated commands into Claude Code context

### 5. Model routing inside Claude Code

Check whether model use is appropriate for Claude Code workflows.

Flag:

- expensive models used for simple extraction
- expensive models used for formatting
- expensive models used for validation
- expensive models used for file movement
- expensive models used for every persona
- no model distinction between draft, critique, validation, and final synthesis
- no compact/deep model profile
- no fallback behavior when limits are near
- no central model configuration

Recommend routing such as:

| Claude Code task | Suggested model level |
|---|---|
| file inspection | cheaper/fast |
| simple validation | cheaper/fast |
| persona divergence | cheaper/fast unless stakes are high |
| adversarial critique | cheaper/fast or one stronger pass |
| final synthesis | stronger model |
| strategic judgment | stronger model only where it matters |
| formatting/export | cheaper/fast |
| deterministic orchestration | script, not model |

When possible, recommend a centralized model routing config.

### 6. Workflow orchestration

Flag workflows where Claude Code is manually orchestrating what should be automated.

Look for:

- long inline Bash commands
- repeated generated command strings
- manually constructed paths
- no central config
- missing defaults file
- missing error handling
- no resumability
- no dry-run mode
- no phase status tracking
- no idempotency
- automatic retries without a usage budget
- no stop points between expensive phases
- no stepwise mode
- no way to run only one phase
- no way to resume after failure
- no way to estimate execution size before running

Recommend:

- small scripts invoked by short commands
- central config for models, phases, paths, and budgets
- resumable phases
- explicit `--compact`, `--deep`, `--stepwise`, and `--dry-run` modes
- no automatic retries unless enabled
- budget checks before expensive phases
- phase status stored in metadata
- short Claude-facing commands, with complexity hidden in deterministic scripts

### 7. Usage-limit controls

Check whether the Claude Code setup has guardrails.

Flag missing:

- maximum agent calls
- maximum model calls
- maximum files read
- maximum output words
- maximum retry count
- maximum phase count
- stop-after-phase mode
- compact default mode
- deep opt-in mode
- warning before expensive workflow
- clear fallback when usage limits are near
- no estimate before running a large workflow
- no ability to run only an audit/dry run

Recommend concrete defaults.

Example:

```text
Default compact mode:
- max 6 model calls
- max 5 files read unless explicitly approved
- max 400 words per agent output
- no automatic retries
- final synthesis only uses strongest model
- intermediate phases use compact summaries
- stop before deep crossfire unless user opts in

Deep mode:
- explicit opt-in only
- max 20 model calls
- show expected workflow before execution
- stop before final synthesis if intermediate artifacts are too large
```

### 8. Runtime and session-artifact inspection

Inspect Claude Code runtime/session artifacts when available, because many usage problems are only visible in actual executions, not in static project files.

This inspection must be read-only and privacy-aware.

#### Inspect these locations when available

Check project-local Claude Code configuration:

- `./CLAUDE.md`
- `./.claude/`
- `./.claude/settings.json`
- `./.claude/settings.local.json`
- `./.claude/commands/`
- `./.claude/agents/`
- `./.claude/skills/`
- `./.claude/hooks/`
- workflow-specific output/session/log folders

Check user-level Claude Code configuration:

- `~/.claude/`
- `%USERPROFILE%\.claude\` on Windows
- `$CLAUDE_CONFIG_DIR` if set

Check likely runtime/session/log areas, if available:

- Claude Code session/transcript/log files under the user-level Claude config directory
- project-specific Claude Code session folders
- temporary directories referenced in logs or tool output
- OS temp directories only when a relevant Claude-related path is already referenced:
  - Windows: `%TEMP%`, `%LOCALAPPDATA%\Temp`
  - macOS/Linux/WSL: `/tmp`, `$TMPDIR`
- generated workflow directories such as:
  - `sessions/`
  - `outputs/`
  - `logs/`
  - `.tmp/`
  - `.cache/`
  - any workflow-specific generated artifact path referenced by commands, skills, or agents

Do not recursively scan all temp directories blindly. Only inspect runtime/temp paths that are clearly related to Claude Code or referenced by project/session logs.

#### What to extract from runtime artifacts

When transcript or session logs are available, summarize:

- total number of Claude Code tool calls
- number of subagent calls
- nested `claude` CLI calls
- models used per phase
- files read repeatedly
- generated files reread repeatedly
- large tool outputs
- failed tool calls
- retries
- phases that consumed the most context
- prompts repeated across calls
- cases where full outputs were passed instead of compact summaries
- whether subagent transcripts reveal hidden usage burners
- whether the outer session hides inner subagent cost
- whether a workflow continued after partial failure
- whether final scribe/export phases retried unnecessarily

Do not copy private transcript contents into the audit unless necessary. Prefer counts, file names, command patterns, and short excerpts.

#### Privacy and safety rules for runtime artifacts

Runtime artifacts may contain private prompts, client names, secrets, credentials, personal information, or business-sensitive context.

Therefore:

- inspect read-only
- do not modify or delete runtime artifacts
- do not print full transcript contents
- do not expose secrets
- redact credentials and tokens
- summarize sensitive prompts rather than quoting them
- warn if private generated sessions are inside the git repo
- recommend `.gitignore` rules for private/generated artifacts
- ask before opening unusually large or clearly sensitive files
- never commit or export runtime artifacts during the audit

#### If runtime artifacts are unavailable

State clearly:

- which locations were checked
- which were unavailable
- whether the audit is based only on static configuration
- what additional artifacts would improve the audit

Recommend running a representative workflow once and then rerunning the audit.

### 9. Runtime evidence from actual Claude Code sessions

Use available session/transcript/log artifacts to validate the static audit.

Flag:

- static setup looks efficient, but runtime shows repeated context
- subagents consume more than the outer transcript suggests
- nested Claude calls hide real usage
- retries or failed phases burn usage
- same file is read many times across phases
- full raw transcripts are passed between agents
- commands dump huge prompts into Bash/tool context
- temporary files accumulate and are reread
- generated session files are not ignored by git
- missing config files cause repeated errors
- final output phases fail after expensive intermediate work

For each runtime finding, include:

- observed evidence
- probable cause
- exact workflow phase affected
- recommended fix
- whether this was proven from runtime logs or inferred from configuration

### 10. Generated files, privacy, and git hygiene

Flag:

- private prompts written into committed directories
- generated session outputs not ignored
- personal/client/business-sensitive context saved by default
- logs containing secrets, names, negotiations, or credentials
- unclear separation of public examples vs private sessions
- generated session directories inside the source tree
- runtime artifacts likely to be accidentally committed
- public examples mixed with real user sessions

Recommend:

- `.gitignore` rules
- private session/output directories
- redaction rules
- export confirmation before sharing
- separation of examples from real user sessions
- local-only storage for sensitive session artifacts
- clear cleanup or retention policy

### 11. Maintainability and reuse

#### 11a. General maintainability

Check whether the Claude Code setup is easy to evolve.

Flag:

- hardcoded paths
- hardcoded model names
- duplicated mode definitions
- duplicated agent names
- no central config
- missing defaults file
- missing error handling
- missing dry-run mode
- generated files mixed with source files
- workflow logic scattered across commands, skills, and agents
- no clear ownership of rules

#### 11b. Naming consistency

Check that names line up across the four places they appear:

| Artifact | Filename / dir | Frontmatter `name:` | How it is invoked |
|---|---|---|---|
| Slash command | `.claude/commands/<name>.md` | (no `name:` field) | `/<name>` |
| Skill | `.claude/skills/<name>/SKILL.md` | `name: <name>` | Skill tool with `skill: "<name>"` |
| Subagent | `.claude/agents/<name>.md` | `name: <name>` | Task tool by name, or auto-selected by `description:` |

Flag:

- mismatched filename and frontmatter `name:` field — the filename is the canonical reference; the frontmatter should match
- skill directory name does not match the `name:` field in its `SKILL.md`
- agent filename does not match the `name:` field in its frontmatter
- slash command name and the skill it invokes diverging when there is a 1:1 mapping (e.g., `/audit-claude-code` invoking skill `claude-code-auditor`) — pick one form and rename for consistency
- inconsistent naming patterns within the same project (e.g., some agents verb-first, others noun-first)
- snake_case mixed with kebab-case — kebab-case is the convention for slash commands, skills, and agents
- references in `CLAUDE.md`, agent prompts, or other context files to old/renamed files
- agent descriptions that name another agent by a stale name

Recommend:

- kebab-case throughout (e.g., `claude-code-auditor`, not `claudeCodeAuditor` or `claude_code_auditor`)
- match slash command name to invoked skill name when there is a 1:1 mapping
- match filename to frontmatter `name:` field exactly
- consistent naming pattern across the project: action-verb for commands (`/review`, `/audit-claude-code`), role-noun for skills/agents (`claude-code-auditor`, `roastr-facilitator`) — or pick one form across all
- search for stale references whenever a file is renamed

Recommendations for general maintainability:

- central config for models, modes, paths, budgets, and phase definitions
- small slash commands
- reusable skills
- short subagent definitions
- deterministic orchestration scripts
- generated outputs separated from source
- stable naming conventions


### 12. Performance and runtime efficiency

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

### 13. Large-context and context-window pressure

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

### 14. CLAUDE.md correctness versus current repository structure

Assess whether `CLAUDE.md` and related context/memory files accurately reflect the current repository structure and workflow reality.

Flag:

- references to folders that no longer exist
- references to files that no longer exist
- missing references to important current folders
- outdated setup instructions
- outdated script names
- outdated command names
- stale build/test/lint instructions
- incorrect generated-output paths
- incorrect session/log/cache paths
- inconsistent naming between `CLAUDE.md`, `.claude/commands/`, `.claude/agents/`, `.claude/skills/`, rules, hooks, and actual repo folders
- instructions that tell Claude Code to inspect the wrong folders
- instructions that omit important Claude Code workflow folders
- duplicated or conflicting rules across multiple `CLAUDE.md` or context files
- stale architecture descriptions that no longer match the repo
- missing `.gitignore` recommendations for generated/private Claude Code artifacts
- path assumptions that work on one OS but not another
- hardcoded Windows/macOS/Linux paths that reduce portability
- repo structure descriptions that are too broad and cause unnecessary scans

Compare:

- `CLAUDE.md`
- `CLAUDE.local.md`, if present
- nested `CLAUDE.md` files, if present
- `.claude/` structure
- `.claude/rules/`
- slash commands
- skills
- agents
- hooks
- settings
- scripts invoked by Claude Code
- actual top-level repo folders
- generated/session/log/cache folders referenced by workflows
- `.gitignore`

For each mismatch, include:

- documented path or rule
- actual current repo evidence
- risk: correctness / performance / context bloat / privacy / maintainability
- suggested update or improvement option
- whether the update is obvious or requires developer judgment

### 15. Claude Code feature-completeness checks

Assess whether the project uses relevant Claude Code features correctly. Do not treat missing features as automatic problems. Only recommend them when they would improve quality, safety, performance, maintainability, context management, or usage efficiency.

Check:

- `CLAUDE.local.md` for local-only configuration, private paths, sandbox URLs, or machine-specific notes
- `.claude/rules/` and `~/.claude/rules/`
- modular rule files with YAML frontmatter
- path-scoped rules using `paths:`
- slash command frontmatter:
  - `description`
  - `allowed-tools`
  - `argument-hint`
  - model override if used
- slash command argument handling:
  - `$ARGUMENTS`
  - `$1`, `$2`, etc.
- skill frontmatter:
  - `name`
  - `description`
  - `allowed-tools`
- skill trigger/discovery quality
- subagent frontmatter:
  - `name`
  - `description`
  - `tools`
  - `model`
  - `permissionMode`
  - `skills`
  - `agentId` or resumability fields where applicable
- whether subagent descriptions are specific enough for correct invocation
- whether proactive language such as “Use PROACTIVELY” or “MUST BE USED” is used only when appropriate
- project MCP config in `.mcp.json`
- user MCP config in `~/.claude.json` under `mcpServers`
- settings fields:
  - permissions allow list
  - permissions deny list
  - sandbox configuration
  - environment variables
  - attribution
  - default permission mode
- hooks, when present:
  - PreToolUse
  - PostToolUse
  - UserPromptSubmit
  - Stop
  - SubagentStop
  - SessionStart
  - SessionEnd
  - Notification
  - PreCompact
  - PermissionRequest, if supported
- plugins:
  - enabled plugins
  - disabled plugins
  - plugin-provided commands, agents, and skills
  - extra known marketplaces
- output styles:
  - `.claude/output-styles/`
  - `~/.claude/output-styles/`
  - `keep-coding-instructions`
- checkpointing / rewind support, if available
- headless mode scripts:
  - `claude -p`
  - `--output-format json`
  - `--output-format stream-json`
  - `--allowedTools`
  - `--disallowedTools`
  - `--mcp-config`
  - `--resume`
  - `--continue`
- status line configuration:
  - status line script
  - model display
  - cost/usage display
  - context display
  - git branch display
- IDE and terminal integration only when relevant to the project workflow

For each missing or weak feature, classify it as:

- required
- useful
- optional
- not relevant

Do not recommend adding features just to maximize a score. The audit should explain why a feature matters for this project, or explicitly mark it not relevant.

### 16. Audit report artifact

The audit should produce a saveable report artifact.

Write the full audit report to a dedicated Markdown file named:

```text
audit_setup_report_<timestamp>.md
```

Recommended location:

```text
.claude/audits/audit_setup_report_<timestamp>.md
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

Return the audit in this exact structure, and also save it to `.claude/audits/audit_setup_report_<timestamp>.md`.

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

## Rules

- This is a Claude Code audit only.
- Do not discuss Anthropic Console/API billing unless explicitly asked.
- Be concrete.
- Prefer exact file-level recommendations.
- Prefer replacement text/config when the fix is obvious.
- When the correct action may vary by project, provide improvement options for developer assessment rather than one forced fix.
- Do not recommend naive quality cuts.
- Preserve quality by reducing repetition, improving context handoff, and routing stronger models only where they matter.
- Do not scan unrelated source code unless needed.
- Do not modify project files unless explicitly asked, except writing the audit report file.
- Do not delete runtime artifacts.
- Do not print full private transcripts.
- Redact secrets.
- If information is missing, say what could not be verified.
- Save the audit as `.claude/audits/audit_setup_report_<timestamp>.md` when possible.
