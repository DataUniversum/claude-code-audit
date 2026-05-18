# Prompt and instruction quality

Flag prompts that are:

- too long
- too vague
- too theatrical
- missing word limits
- missing schemas
- asking for unbounded debate
- asking for hidden reasoning instead of concise outputs
- mixing planning, execution, validation, and final synthesis
- allowing "continue until complete" without budget limits
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
