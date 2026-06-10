# AgentDX

Assess how well your repository is set up for agentic engineering workflows. Get a score (0–100) with actionable findings and recommendations.

AgentDX is a skill-based plugin that runs inside GitHub Copilot and Claude Code sessions — no external tools or dependencies required.

## What It Checks

| Category | Weight | What It Evaluates |
|----------|--------|-------------------|
| Agent Config Files | 25% | Presence of AGENTS.md, copilot-instructions, CLAUDE.md, .claude/ ecosystem files, plus optional MCP bonus |
| Instruction Quality | 25% | Specificity, actionability, completeness, brevity, structure, cross-references, plus optional custom skills bonus |
| Security & Git Hygiene | 20% | Secrets detection, gitignore patterns, dangerous permissions, sensitive file exposure |
| Repository Structure | 10% | README quality, contributing guidance, docs, folder organization |
| CI/CD Integration | 10% | Documented build/test/lint commands, workflow files, discoverable scripts |
| Development Workflow | 10% | Branch naming, commit standards, PR process, git hooks and automation |

MCP server setup and custom skills/commands are evaluated as bonuses inside Agent Config Files and Instruction Quality. Their absence is not penalized for repositories that do not need them.

## Usage

### Skill vs Agent

AgentDX provides two modes of operation:

| | **agentdx skill** | **agentdx-fix agent** |
|--|-------------------|----------------------|
| **Purpose** | Assess and score your repo | Assess, then fix issues |
| **Mode** | Assessment + report files | Interactive remediation |
| **Output** | Score report in chat + `.agentdx/report.md` and `.agentdx/report.json` | Report + generated/fixed files |
| **Use when** | You want a quick health check | You want guided fixes applied |
| **Invocation** | `/agentdx:agentdx` or natural language | `/agents` → agentdx-fix |

### GitHub Copilot CLI

Install as a skill plugin, then ask:
> "Check my repo's agent readiness"

Note: The agentdx-fix agent is Claude Code only. On Copilot CLI, the skill provides assessment and you apply fixes manually.

### Claude Code

**Run a scan (skill — assessment + report files):**
> "Run an agentdx scan"

**Diagnose and fix (agent — interactive):**
> "Run agentdx-fix"

The agent will scan your repo, show the report, then ask which issues you'd like fixed. It can generate CLAUDE.md, .gitignore entries, skill skeletons, and more — always with your confirmation before writing files.

### Selective Scanning

You can scan specific categories:
> "Check only my agent config files and security"

You can also scope a scan to a specific tool/platform:
> "Run agentdx scan for Copilot"

Tool-scoped scans report only issues relevant to that platform plus universal repository hygiene. For example, a Copilot scan ignores `CLAUDE.md` and `.claude/` files entirely.

### JSON Output

Results are automatically saved to `.agentdx/report.json` and `.agentdx/report.md` for tracking over time. Request JSON in chat with:
> "Run agentdx scan and output as JSON"

## Output

AgentDX produces a scored report with per-category findings:

```
## AgentDX Score: 72/100 (Good)

### Category Breakdown
| Category              | Score | Status |
|-----------------------|-------|--------|
| Agent Config Files    | 85    | ✅     |
| Instruction Quality   | 70    | ⚠️     |
| Security & Git Hygiene| 90    | ✅     |
| Repository Structure  | 75    | ⚠️     |
| CI/CD Integration     | 65    | ⚠️     |
| Development Workflow  | 60    | ⚠️     |

### Findings
#### Agent Config Files (85/100)
- ✅ `.github/copilot-instructions.md` exists and is substantive
- ✅ `.claude/settings.json` committed with proper permissions
- ⚠️ `CLAUDE.md` present but over 400 lines — consider trimming
- ❌ No `.cursorrules` file found

### Detected Platforms
Active agent platforms: Claude Code, GitHub Copilot

### Top 3 Recommendations
1. Trim CLAUDE.md to <200 lines, move details to .claude/rules/ (+8 points)
2. Add .cursorrules for Cursor users (+5 points)
3. Create .mcp.json with project-relevant servers (+4 points)
```

## Installation

### GitHub Copilot CLI

**Add the marketplace and install the plugin:**

```bash
copilot plugin marketplace add ra1han/agentdx
copilot plugin install agentdx
```

**Or install directly from the repository:**

```bash
copilot plugin install ra1han/agentdx
```

### OpenAI Codex

**Add the marketplace and install:**

```bash
codex plugin marketplace add ra1han/agentdx
```

**Or load directly during development:**

```bash
codex --plugin-dir ./agentdx
```

### Claude Code

**Install from a marketplace (if added):**

```bash
/plugin marketplace add ra1han/agentdx
/plugin install agentdx
```

**Or load directly during development:**

```bash
claude --plugin-dir ./agentdx
```

Once installed, use the skill:

```
/agentdx:agentdx
```

## Plugin Structure

```
agentdx/
├── .codex-plugin/
│   └── plugin.json            # OpenAI Codex plugin manifest
├── .agents/plugins/
│   └── marketplace.json       # Codex marketplace registry
├── .claude-plugin/
│   ├── plugin.json            # Claude Code plugin manifest
│   └── marketplace.json       # Claude Code marketplace registry
├── .github/plugin/
│   ├── plugin.json            # Copilot CLI plugin manifest
│   └── marketplace.json       # Copilot CLI marketplace registry
├── skills/agentdx/
│   ├── SKILL.md               # Main assessment skill
│   ├── categories/            # Scoring rubrics per category
│   └── shared/                # Rubric weights & report format
├── agents/
│   └── agentdx-fix.md         # Diagnostic & fix agent
├── plugin.json                # Root plugin metadata
├── CHANGELOG.md               # Version history
└── README.md
```

## License

MIT
