# Category: Security & Git Hygiene

## Purpose
Assess whether the repository properly handles secrets, credentials, and agent-related file gitignore patterns. Agents must never encounter hardcoded secrets, and agent state files should be properly separated between shared and personal.

## Criteria

### 1. Secrets in Repository (30% of category score)
Check that no hardcoded secrets, API keys, or credentials are committed.

- **Pass**: No secrets detected in tracked files; `.env` files are gitignored; no API keys in config files
- **Partial**: `.env` is gitignored but some config files contain suspicious values that may be keys (ambiguous)
- **Fail**: Committed `.env` file, hardcoded API keys in source, or credentials in config files

Patterns to scan for in tracked files:
- `.env` file committed (not in `.gitignore`)
- AWS keys: `AKIA[0-9A-Z]{16}`
- GitHub tokens: `ghp_[a-zA-Z0-9]{36}`, `gho_`, `ghs_`, `ghr_`
- OpenAI/Anthropic keys: `sk-[a-zA-Z0-9]{20,}`, `sk-ant-`
- Slack tokens: `xoxb-`, `xoxp-`, `xoxa-`
- Generic patterns: `password=`, `secret=`, `token=` with literal values (not env var references)
- Connection strings with embedded passwords: `://user:pass@`
- Private keys: `-----BEGIN (RSA |EC )?PRIVATE KEY-----`

Note: Exclude false positives in documentation, examples, and test fixtures that use obviously fake values.

### 2. Agent File Gitignore (30% of category score)
Verify that personal/local agent files are properly gitignored while shared configs are committed.

**Should be gitignored (personal state):**
- `.claude/settings.local.json` — personal overrides
- `.claude/memory/` — personal memory/context
- `.claude/todos/` — personal task tracking
- `.env` and `.env.local` — environment secrets

**Should NOT be gitignored (shared config):**
- `.claude/settings.json` — project-level shared settings
- `.claude/rules/` — shared scoped rules
- `.claude/skills/` — shared skills
- `CLAUDE.md`, `AGENTS.md` — instruction files
- `.mcp.json` — shared MCP config

Scoring:
- **Pass**: Personal files are gitignored AND shared configs are committed (not ignored)
- **Partial**: Some personal files are not gitignored; OR shared config is accidentally ignored
- **Fail**: `.claude/settings.local.json` or `.claude/memory/` is committed; OR `.env` is committed

### 3. Dangerous Permissions (20% of category score)
Check agent permission settings for overly permissive or dangerous configurations.

- **Pass**: Permissions follow least-privilege; no wildcard allows for destructive commands; `git push` and similar are explicitly denied or require confirmation
- **Partial**: Some overly broad permissions but nothing critical (e.g., broad Bash access but push is denied)
- **Fail**: Settings allow dangerous commands without confirmation (e.g., `Bash(git push:*)`, `Bash(rm -rf:*)`, unrestricted file deletion)

Where to check:
- `.claude/settings.json` → `permissions` field
- Look for `allow` rules with wildcards on destructive commands
- Flag: `git push`, `git force-push`, `rm -rf`, `DROP TABLE`, `kubectl delete`

### 4. Sensitive Files Excluded from Agent Context (20% of category score)
Are sensitive directories/files excluded from agent access or clearly marked?

- **Pass**: Sensitive paths (credentials, private keys, production configs) are either gitignored or documented as off-limits in agent instructions
- **Partial**: Some sensitive files exist but there's no explicit guidance for agents to avoid them
- **Fail**: Sensitive files (private keys, production credentials) are committed and no agent restrictions exist

Things to check:
- Presence of `.git-credentials`, `*.pem`, `*.key` in repo
- Production config with real credentials committed
- Agent instructions (CLAUDE.md) mentioning what files/directories to avoid
- `.claude/settings.json` deny rules for sensitive paths

## How to Evaluate

1. Check `.gitignore` for proper agent file patterns
2. Scan for common secret patterns in tracked files (focus on config files, not all source)
3. If `.claude/settings.json` exists, check permission rules
4. Look for sensitive file types committed to the repository
5. Cross-reference with agent instruction files for documented exclusions
6. Calculate category score using the weights above

## Important Notes

- False positives are common with secret scanning. Be conservative — only flag high-confidence matches.
- Example/test values (like `sk-test-xxx`, `AKIA_EXAMPLE`) should not be flagged.
- If a project has no `.claude/` directory at all, score criteria 2 and 3 as N/A and weight remaining criteria proportionally.
