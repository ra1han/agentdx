# Category: Agent Config Files

## Purpose
Assess the presence, location, and completeness of agent configuration files in the repository. Includes platform detection and advanced Claude Code ecosystem checks.

## Files to Check

When the main skill specifies a target platform, filter this list before scoring:

| Target Platform | Include | Exclude |
|-----------------|---------|---------|
| GitHub Copilot | `AGENTS.md`, `.github/copilot-instructions.md`, `.github/instructions/**`, `.github/prompts/**`, `.github/copilot/mcp.json`, `.vscode/mcp.json` | `CLAUDE.md`, `.claude/**`, `.cursorrules`, `.cursor/**`, `.windsurfrules` |
| Claude Code | `AGENTS.md`, `CLAUDE.md`, `.claude/**`, `.mcp.json` | `.github/copilot-instructions.md`, `.github/instructions/**`, `.github/prompts/**`, `.github/copilot/**`, `.vscode/mcp.json`, `.cursorrules`, `.cursor/**`, `.windsurfrules` |
| Cursor | `AGENTS.md`, `.cursorrules`, `.cursor/**` | `CLAUDE.md`, `.claude/**`, `.github/copilot-instructions.md`, `.github/instructions/**`, `.github/prompts/**`, `.windsurfrules` |
| Windsurf | `AGENTS.md`, `.windsurfrules` | `CLAUDE.md`, `.claude/**`, `.github/copilot-instructions.md`, `.github/instructions/**`, `.github/prompts/**`, `.cursorrules`, `.cursor/**` |

For a tool-scoped scan, do not read, score, list, or recommend missing files from the Exclude column. For example, a GitHub Copilot scan must not count `CLAUDE.md` as present, absent, too long, contradictory, or missing a cross-reference.

### Primary Agent Instruction Files

| File | Platform | Expected Location |
|------|----------|-------------------|
| `AGENTS.md` | Universal | Repository root |
| `.github/copilot-instructions.md` | GitHub Copilot | `.github/` directory |
| `CLAUDE.md` | Claude Code | Repository root |
| `.cursorrules` | Cursor | Repository root |
| `.windsurfrules` | Windsurf | Repository root |

### Claude Code Ecosystem Files

| File/Directory | Purpose | Expected Location |
|----------------|---------|-------------------|
| `.claude/settings.json` | Project-level shared settings | `.claude/` directory |
| `.claude/settings.local.json` | Personal overrides (should be gitignored) | `.claude/` directory |
| `.claude/rules/*.md` | Path-scoped lazy-loaded rules | `.claude/rules/` directory |
| `.claude/skills/**/SKILL.md` | Reusable workflow skills | `.claude/skills/` directory |
| `.claude/agents/*.md` | Subagent definitions | `.claude/agents/` directory |
| `hooks/hooks.json` | Event hook configurations | `hooks/` directory |

## Criteria

### 1. File Existence (30% of category score)
For each primary instruction file, check if it exists in the expected location.
- **Pass**: File exists at correct location
- **Partial**: File exists but in wrong location (e.g., `copilot-instructions.md` at root instead of `.github/`)
- **Missing**: File does not exist

Score: (files found / 5) × 100. Award partial credit (half) for mislocated files.

Note: Not every project needs all 5 files. Award full marks if at least 2 platform-specific files exist AND they cover the platforms the project actually uses (check for platform indicators like `.github/workflows/` for Copilot, existence of other `.cursor/` files for Cursor, etc.).

### 2. Minimum Substance (25% of category score)
For each found file, verify it has meaningful content:
- **Pass**: >50 words of substantive content (not boilerplate/placeholders)
- **Partial**: 20–50 words, or contains placeholder text ("TODO", "Add instructions here")
- **Fail**: <20 words or entirely placeholder content

### 3. Key Sections (25% of category score)
For each substantive file, check for coverage of these topics:
- Project overview / what this project is
- Coding conventions / style guidelines
- Tooling / how to build, test, lint
- Constraints / things the agent should NOT do

Scoring:
- **Pass**: 3–4 topics covered
- **Partial**: 1–2 topics covered
- **Fail**: 0 topics covered

### 4. Claude Code Ecosystem (20% of category score)
Check for advanced Claude Code configuration that improves agent effectiveness:

This criterion applies only to cross-platform scans and Claude Code scans. For GitHub Copilot, Cursor, or Windsurf scans, mark this criterion N/A and reweight the remaining Agent Config Files criteria proportionally.

- **`.claude/settings.json`**: Project settings committed for team sharing
- **`.claude/rules/*.md`**: Scoped rules with `paths:` frontmatter for lazy-loading
- **`.claude/skills/`**: Reusable skills defined as SKILL.md files
- **`.claude/agents/`**: Subagent definitions for specialized tasks
- **`hooks/hooks.json`**: Event-driven automations

Scoring:
- **Pass**: 3+ ecosystem files/directories present and configured
- **Partial**: 1–2 ecosystem items present
- **Fail**: No Claude Code ecosystem configuration

Additional checks for this criterion:
- If `.claude/rules/*.md` exist, verify they have `paths:` in YAML frontmatter (otherwise they inflate context like CLAUDE.md)
- If `.claude/settings.json` exists, verify `.claude/settings.local.json` is in `.gitignore`
- If project has multiple domains (frontend + backend), suggest splitting rules by path

### 5. MCP Server Configuration (Informational bonus — up to +10 points)
Check whether MCP (Model Context Protocol) servers are configured to extend agent capabilities.

Files to check:
- `.mcp.json` (Claude Code)
- `.vscode/mcp.json` (VS Code / Copilot)
- `.cursor/mcp.json` (Cursor)
- `.github/copilot/mcp.json` (GitHub Copilot)

For tool-scoped scans, only check MCP files relevant to the target platform.

Scoring:
- **Pass (+10)**: MCP config exists with at least one relevant server properly configured (valid transport, env vars for secrets)
- **Partial (+5)**: MCP config exists but servers are incomplete, irrelevant, or excessive (>8 for a simple project)
- **N/A (+0)**: No MCP config — acceptable for simple projects, no penalty

Note: MCP is relatively new. Not every project needs it. Do not penalize absence — only reward presence.

### 6. Platform Detection (Informational — not scored)
Detect which platforms are in use and report in findings:
- Check for `.github/workflows/` → GitHub Copilot likely in use
- Check for `.cursor/` directory → Cursor in use
- Check for `CLAUDE.md` or `.claude/` → Claude Code in use
- Check for `.windsurfrules` → Windsurf in use

Report as: "Detected platforms: Claude Code, GitHub Copilot" (informational, helps contextualize missing files).

For tool-scoped scans, also report: "Target platform: GitHub Copilot" (or the requested platform) and separate it from any other detected platforms. Detected non-target platforms are informational only and must not create findings.

## How to Evaluate

1. Apply the target platform filter from the main skill
2. Use file search to check for each in-scope file in the expected location
3. Read found in-scope files and assess word count (excluding code blocks for word count)
4. Evaluate whether in-scope content covers the key sections listed above
5. Check platform ecosystem files only when they are in scope for the target platform
6. Detect platforms in use for contextual reporting
7. Calculate category score using the weights above, reweighting criteria marked N/A
