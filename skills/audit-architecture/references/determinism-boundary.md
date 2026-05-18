# Determinism boundary

What should be script vs what should be model.

## Principle

Model calls are expensive, slow, and non-deterministic. Use them only where judgment is required. Everything else is a script.

## Signs that work should be deterministic (script)

- the same input always produces the same correct output
- the logic can be expressed in code without ambiguity
- the output is structured data, not prose
- the step is about file movement, parsing, formatting, validation, computation, or orchestration
- the step would benefit from being testable and resumable

## Signs that work needs a model

- the input is unstructured natural language or code
- the output requires synthesis, judgment, or evaluation
- the step involves understanding intent, weighing trade-offs, or producing prose
- the correct answer varies by context in ways that cannot be enumerated

## Flag

- agents doing file movement, path construction, manifest generation, format conversion
- skills containing orchestration logic (which phase runs when, how to retry, how to resume)
- workflows that ask Claude to "decide which files to read" when the answer is fixed
- model calls used to count, sort, filter, deduplicate, or aggregate
- model calls used to enforce schemas (validators belong in scripts)
- scripts that prompt the user for judgment that a model could make in one call (the opposite mistake)
- hardcoded paths constructed in prompts that should be in a config file
- retry logic implemented in prompts instead of in the orchestration layer

## Decision question

For each step, ask: if I knew the answer to this in advance, could I write it in code? If yes, it's a script.

## Recommend

- extract orchestration to scripts; let Claude run them via Bash or a slash command
- central config file for paths, models, phase definitions, budgets
- scripts return structured output (JSON) that Claude can consume
- model calls only at judgment points; everything around them is deterministic
- dry-run mode in every orchestration script
