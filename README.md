# AgentDX

Assess how well your repository is set up for agentic engineering workflows. Get a score (0–100) with actionable findings and recommendations.

AgentDX is a skill-based plugin that runs inside GitHub Copilot and Claude Code sessions — no external tools or dependencies required.

## What It Checks

| Category | Weight | What It Evaluates |
|----------|--------|-------------------|
| Agent Config Files | 25% | Presence and completeness of AGENTS.md, copilot-instructions, CLAUDE.md, etc. |
| Instruction Quality | 25% | Specificity, actionability, and completeness of agent instructions |
| MCP Server Setup | 15% | MCP configuration files and tool definitions |
| Custom Skills/Commands | 15% | Project-specific skills and workflow definitions |
| Repository Structure | 10% | README quality, docs, folder organization |
| CI/CD Integration | 10% | Documented build/test/lint commands for agents |

## Usage

### GitHub Copilot

Install as a skill plugin, then ask:
> "Check my repo's agent readiness"

### Claude Code

Reference the skill in your project, then ask:
> "Run an agentdx scan"

### Selective Scanning

You can scan specific categories:
> "Check only my agent config files and instruction quality"

## Output

AgentDX produces a hybrid score + checklist report:

```
## AgentDX Score: 72/100 (Good)

### Category Breakdown
| Category              | Score | Status |
|-----------------------|-------|--------|
| Agent Config Files    | 85    | ✅     |
| Instruction Quality   | 70    | ⚠️     |
| ...                   | ...   | ...    |

### Findings
#### Agent Config Files (85/100)
- ✅ `.github/copilot-instructions.md` exists and is substantive
- ⚠️ `CLAUDE.md` present but missing constraints section
- ❌ No `.cursorrules` file found

### Top 3 Recommendations
1. Add constraints section to CLAUDE.md
2. Create .cursorrules for Cursor users
3. Document test commands in agent config
```

## Installation

### GitHub Copilot CLI

**Add the marketplace and install the plugin:**

```bash
copilot plugin marketplace add ra1han/agentdx
copilot plugin install agentdx@agentdx
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

## License

MIT
