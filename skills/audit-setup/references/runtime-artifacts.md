# Runtime and session-artifact inspection

## Section 8 — Inspection

Inspect Claude Code runtime/session artifacts when available, because many usage problems are only visible in actual executions, not in static project files.

This inspection must be read-only and privacy-aware.

### Inspect these locations when available

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

### What to extract from runtime artifacts

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

### Privacy and safety rules for runtime artifacts

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

### If runtime artifacts are unavailable

State clearly:

- which locations were checked
- which were unavailable
- whether the audit is based only on static configuration
- what additional artifacts would improve the audit

Recommend running a representative workflow once and then rerunning the audit.

## Section 9 — Runtime evidence from actual Claude Code sessions

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
