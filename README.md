# AgentDX

Assess how well your repository is set up for agentic engineering workflows. Get a score (0-100), readiness level, proof level, confidence, actionable findings, and recommendations.

AgentDX is a skill-based plugin that runs inside GitHub Copilot, Claude Code, and OpenAI Codex sessions — no external tools or dependencies required.

## What It Checks

| Category | Weight | What It Evaluates |
|----------|--------|-------------------|
| Agent Config Files | 12% | Presence of AGENTS.md, copilot-instructions, CLAUDE.md, platform ecosystem files, plus optional MCP bonus |
| Instruction Quality | 12% | Specificity, actionability, completeness, brevity, structure, cross-references, plus optional custom skills bonus |
| Security & Git Hygiene | 14% | Secrets detection, gitignore patterns, dangerous permissions, sensitive file exposure |
| Repository Structure | 8% | README quality, contributing guidance, docs, folder organization, repo map/onboarding surface |
| CI/CD Integration | 14% | Build/test/lint/smoke/proof commands, workflow files, command tiers, local/CI equivalence |
| Development Workflow | 8% | Branch naming, commit standards, PR process, hooks, manual-operation signals, improvement loop |
| Setup & Environment | 10% | Env contract, runtime/tool versions, local infrastructure, setup/diagnostics path, external dependency pressure |
| Proof & Feedback Loops | 14% | Fast checks, boot/health/smoke, supported interactions, observable evidence, reset/fixtures, test mechanisms |
| Adaptability | 8% | Seams, offline testability, side-effect sinks, state/contract checks, architecture boundaries, inner-loop speed |

MCP server setup and custom skills/commands are evaluated as bonuses inside Agent Config Files and Instruction Quality. Their absence is not penalized for repositories that do not need them.

AgentDX also reports:

- **Readiness Level (H0-H5)** — from unknown/no front door through operable, proveable, and compounding workflows
- **Proof Level (L0-L6)** — from claim-only through static checks, runtime interaction, observable consequence, clean rerun, and production/customer evidence
- **Confidence** — high, medium, or low based on direct file evidence and unknowns
- **Command tiers** — bootstrap, diagnostics, boot, health, fast, proof, CI-equivalent, smoke, seed/reset, observe, cleanup
- **Environment and dependency pressure** — env var names only, local substitutes, remote/secret requirements, mutation risk, and proof blockers

## Usage

### Skill vs Agent

AgentDX provides two modes of operation:

| | **agentdx skill** | **agentdx-fix agent** |
|--|-------------------|----------------------|
| **Purpose** | Assess and score your repo | Assess, then fix issues |
| **Mode** | Assessment + report files | Interactive remediation |
| **Output** | Score report in chat; optional `.agentdx/report.md` and `.agentdx/report.json` | Report + generated/fixed files |
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

Request JSON in chat with:
> "Run agentdx scan and output as JSON"

Ask to persist the report when you want `.agentdx/report.json` and `.agentdx/report.md` written for tracking over time.

## Output

AgentDX produces a scored report with per-category findings:

```
## AgentDX Score: 72/100 (Good)
Readiness Level: H3 Operable
Highest Proof Level: L2 Static/Build/Test
Target Next Proof Level: L3 Runtime Interaction
Confidence: Medium

### Category Breakdown
| Category              | Score | Status |
|-----------------------|-------|--------|
| Agent Config Files    | 85    | ✅     |
| Instruction Quality   | 70    | ⚠️     |
| Security & Git Hygiene| 90    | ✅     |
| Repository Structure  | 75    | ⚠️     |
| CI/CD Integration     | 65    | ⚠️     |
| Development Workflow  | 60    | ⚠️     |
| Setup & Environment   | 70    | ⚠️     |
| Proof & Feedback Loops| 55    | ⚠️     |
| Adaptability          | 65    | ⚠️     |

### Findings
#### Agent Config Files (85/100)
- ✅ `.github/copilot-instructions.md` exists and is substantive
- ✅ `.claude/settings.json` committed with proper permissions
- ⚠️ `CLAUDE.md` present but over 400 lines — consider trimming
- ❌ No `AGENTS.md` file found for shared agent instructions

### Detected Platforms
Active agent platforms: Claude Code, GitHub Copilot

### Agent Proof Summary
| Signal | Detected State |
|--------|----------------|
| Readiness Level | H3 — fast local validation is discoverable |
| Highest Proof Level | L2 — lint and unit tests are available |
| Target Next Proof Level | L3 — add a smoke/API workflow command |
| Fast Local Feedback | `npm test` configured |
| CI/Local Equivalence | partial |
| Runtime Smoke Path | missing |

### Top 3 Recommendations
1. Add a smoke command that boots the app and exercises one supported route (+8 points, unlocks L3)
2. Align local `test`/`lint` commands with CI workflow checks (+6 points)
3. Add `.env.example` with required env var names and safe local defaults (+5 points)
```

## Installation

### GitHub Copilot CLI

**Install directly from GitHub:**

```bash
copilot plugin install ra1han/agentdx
```

**Or add the marketplace and install from it:**

```bash
copilot plugin marketplace add ra1han/agentdx
copilot plugin install agentdx@agentdx
```

**Update:**

```bash
copilot plugin update agentdx
```

### OpenAI Codex

**Add the marketplace and install:**

```bash
codex plugin marketplace add ra1han/agentdx
codex plugin install agentdx@agentdx
```

**Update:**

```bash
codex plugin update agentdx
```

**Or load directly during development:**

```bash
codex --plugin-dir ./agentdx
```

### Claude Code

**Add the marketplace and install from it:**

```bash
claude plugin marketplace add ra1han/agentdx
claude plugin install agentdx@agentdx
```

**Update:**

```bash
claude plugin update agentdx
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
