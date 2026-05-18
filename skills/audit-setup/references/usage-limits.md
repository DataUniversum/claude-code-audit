# Usage-limit controls

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
