# Model routing inside Claude Code

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
