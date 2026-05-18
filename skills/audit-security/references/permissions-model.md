# Permission model

The first defense. Wrong here and everything else is downstream damage control.

## Inspect

- `.claude/settings.json` — project permissions
- `.claude/settings.local.json` — local overrides (should be gitignored)
- `~/.claude/settings.json` — user permissions
- default permission mode
- sandbox configuration
- environment variable references

## Surfaces to evaluate

- `permissions.allow` — explicit allow list
- `permissions.deny` — explicit deny list
- `permissions.defaultMode` — what happens to unlisted tools
- sandbox config — whether tools run in restricted environment
- `attribution` — whether outputs are attributed correctly
- env vars passed into the harness

## Flag

- empty deny list with permissive default mode — every tool implicitly allowed
- broad allow patterns: `Bash`, `Bash(*)`, `Read`, `Read(*)`, `WebFetch` without domain scope
- write tools allowed for paths outside the project
- destructive Bash patterns allowed: `rm -rf`, `chmod`, network installers (`curl | sh`, `wget | bash`)
- `Bash(git push)` allowed automatically (can publish unintended changes)
- `Bash(*)` allowed in any settings file
- `settings.local.json` committed to git
- `settings.local.json` widening permissions beyond project defaults without ownership
- inconsistent permissions between project and user settings (user-level allow overrides project-level deny)
- `defaultMode: "allow"` or equivalent permissive default
- sandbox not configured when project handles untrusted input
- no `WebFetch` domain allowlist
- no `WebSearch` allow/deny
- env vars in settings that reference secrets by name visible in commits

## Recommend

- explicit allow list — no wildcards
- explicit deny list for destructive patterns even if not in allow list (defense in depth)
- `WebFetch` scoped to allowed domains
- `Bash` scoped to specific commands when possible: `Bash(git status:*)`, `Bash(npm test)`, not `Bash(*)`
- write tools scoped to project paths
- `settings.local.json` always in `.gitignore`
- sandbox enabled when project handles untrusted input
- default permission mode set to "ask" or stricter
- review user-level settings to ensure they do not silently widen project permissions
