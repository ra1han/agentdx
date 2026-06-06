# AgentDX

Assess how well your repository is set up for agentic engineering workflows. Get a score (0–100) with actionable findings and recommendations.

AgentDX is a skill-based plugin that runs inside GitHub Copilot and Claude Code sessions — no external tools or dependencies required.

## What It Checks

| Category | Weight | What It Evaluates |
|----------|--------|-------------------|
| Agent Config Files | 20% | Presence of AGENTS.md, copilot-instructions, CLAUDE.md, .claude/ ecosystem files |
| Instruction Quality | 20% | Specificity, actionability, brevity, structure, and cross-references |
| MCP Server Setup | 15% | MCP configuration, server relevance, and security |
| Custom Skills/Commands | 15% | Project-specific skills, structure quality, and discoverability |
| Security & Git Hygiene | 15% | Secrets detection, gitignore patterns, dangerous permissions |
| Repository Structure | 8% | README quality, docs, folder organization |
| CI/CD Integration | 7% | Documented build/test/lint commands for agents |

## Usage

### GitHub Copilot

Install as a skill plugin, then ask:
> "Check my repo's agent readiness"

### Claude Code

Install the plugin, then ask:
> "Run an agentdx scan"

Or use the agentdx-fix agent for guided fixes:
> "Run agentdx-fix"

### Selective Scanning

You can scan specific categories:
> "Check only my agent config files and security"

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
| MCP Server Setup      | 50    | ⚠️     |
| Custom Skills         | 60    | ⚠️     |
| Security & Git Hygiene| 90    | ✅     |
| Repository Structure  | 75    | ⚠️     |
| CI/CD Integration     | 65    | ⚠️     |

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
