# Generated files, privacy, and git hygiene

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
