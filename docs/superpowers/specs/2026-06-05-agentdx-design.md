# AgentDX — Agentic Engineering Readiness Scorer

**Date:** 2026-06-05
**Status:** Approved
**Repository:** ra1han/agentdx

## Overview

AgentDX is a skill-based plugin for GitHub Copilot and Claude Code that assesses how well a repository is set up for agentic engineering workflows. It checks agent configuration files, instruction quality, MCP server setup, custom skills, repository structure, and CI/CD integration, then produces a score (0–100) with actionable findings.

## Goals

- Help developers understand how "agent-ready" their repository is
- Provide a clear, actionable report with specific findings and recommendations
- Work inside existing agent sessions (Copilot and Claude Code) with no external dependencies
- Support full scans and selective category scans

## Non-Goals (v1)

- Automatic remediation (report only, no auto-fix)
- Standalone CLI or CI/CD integration (future consideration)
- Deterministic scoring (LLM-evaluated, may vary slightly between runs)

## Architecture

```
agentdx/
├── skills/
│   ├── agentdx.md               # Main orchestrator skill (full scan or selective)
│   ├── categories/
│   │   ├── agent-configs.md     # Category: config file presence & completeness
│   │   ├── instruction-quality.md # Category: prompt/instruction quality evaluation
│   │   ├── mcp-setup.md        # Category: MCP server configuration
│   │   ├── custom-skills.md    # Category: custom skills/commands setup
│   │   ├── repo-structure.md   # Category: agent-helpful repo organization
│   │   └── ci-cd.md            # Category: CI/CD documentation for agents
│   └── shared/
│       ├── rubric.md           # Scoring rubric & weights
│       └── report-format.md   # Output format template
├── docs/
│   └── superpowers/specs/
├── README.md
└── LICENSE
```

### Flow

1. User invokes the `agentdx` skill (or a specific category)
2. Skill reads `rubric.md` for criteria and weights
3. Runs selected (or all) category checks by reading repo files
4. Each category produces a 0–100 score with individual findings
5. Orchestrator aggregates weighted scores and formats the final report
6. Report is output directly in chat

### Platform Delivery

- **GitHub Copilot:** Installed as a plugin via the superpowers/installed-plugins convention. Skill name `agentdx` with trigger phrases for discovery.
- **Claude Code:** Referenced from project config or installed via Claude's skill mechanism. Same skill content with a thin platform-specific wrapper.
- **Shared content:** Rubric, category checks, and report format are platform-agnostic markdown. Only entry point metadata differs.

## Scoring System

### Score Range

- Overall: 0–100
- Per category: 0–100
- Grade labels: 90+ Excellent, 70–89 Good, 50–69 Fair, <50 Needs Work

### Category Weights

| Category | Weight | Rationale |
|----------|--------|-----------|
| Agent Config Files | 25% | Foundation — without config, agents fly blind |
| Instruction Quality | 25% | Quality of guidance directly impacts agent output |
| MCP Server Setup | 15% | Extends agent capabilities significantly |
| Custom Skills/Commands | 15% | Workflow-specific agent power |
| Repository Structure | 10% | Helps agents navigate and understand context |
| CI/CD Integration | 10% | Enables agents to validate their own work |

### Scoring Granularity

Each category checks multiple criteria. Each criterion scores as:
- ✅ **Pass** — full points
- ⚠️ **Partial** — half points
- ❌ **Missing/Fail** — zero points

## Category Details

### 1. Agent Config Files (25%)

**What it checks:**
- Presence of: `AGENTS.md`, `.github/copilot-instructions.md`, `CLAUDE.md`, `.cursorrules`, `.windsurfrules`
- Minimum substance (>50 words of meaningful content, not boilerplate or placeholder text like "TODO: add instructions")
- Key sections present (project overview, conventions, tooling, constraints)
- Correct file location per platform conventions

### 2. Instruction Quality (25%)

**What it checks:**
- Specificity: concrete examples vs vague guidance
- Actionability: does it tell the agent *what to do*, not just *what the project is*
- Completeness: covers architecture, conventions, tooling, constraints
- Consistency: no contradictions across multiple config files

### 3. MCP Server Setup (15%)

**What it checks:**
- Presence of: `.vscode/mcp.json`, `.cursor/mcp.json`, `claude_desktop_config.json` references, root `mcp.json`
- Servers defined with meaningful descriptions
- Tool descriptions are clear and specific
- Proper configuration (no placeholder values)

### 4. Custom Skills/Commands (15%)

**What it checks:**
- Skill directories or files present
- Skill descriptions are clear and purposeful
- Triggers/invocation patterns well-defined
- Skills cover project-specific workflows

### 5. Repository Structure (10%)

**What it checks:**
- README.md quality (can an agent understand the project from it?)
- CONTRIBUTING.md or equivalent
- docs/ folder with meaningful content
- Clear folder organization (not deeply nested, logically grouped)

### 6. CI/CD Integration (10%)

**What it checks:**
- Build/test/lint commands documented in agent config files
- Commands are specific and runnable (not "run the tests")
- Workflow files present (GitHub Actions, etc.)
- Makefile/package.json/scripts section with discoverable commands

## Output Format

```markdown
## AgentDX Score: 68/100 (Fair)

### Category Breakdown
| Category              | Score | Status |
|-----------------------|-------|--------|
| Agent Config Files    | 80    | ⚠️     |
| Instruction Quality   | 60    | ⚠️     |
| MCP Server Setup      | 90    | ✅     |
| Custom Skills         | 50    | ⚠️     |
| Repository Structure  | 70    | ⚠️     |
| CI/CD Integration     | 40    | ❌     |

### Findings

#### Agent Config Files (80/100)
- ✅ `.github/copilot-instructions.md` exists and is substantive
- ✅ `CLAUDE.md` exists with project-specific guidance
- ⚠️ `AGENTS.md` is present but missing conventions section
- ❌ No `.cursorrules` file found

#### [... other categories ...]

### Top 3 Recommendations
1. [Most impactful improvement]
2. [Second most impactful]
3. [Third most impactful]
```

## Selective Scanning

Users can request:
- **Full scan:** "Check my repo's agent readiness" → runs all 6 categories
- **Single category:** "Check my agent config files" → runs only that category
- **Multiple categories:** "Check my MCP setup and instruction quality" → runs selected categories

When running selectively, the overall score reflects only the selected categories (re-weighted to sum to 100%).

## Design Decisions

1. **Pure prompt-based:** No helper scripts. The host agent (Copilot/Claude) reads files and evaluates against the rubric using its own reasoning. This keeps the plugin simple and portable.
2. **Modular categories:** Each category is a separate file for maintainability and selective invocation.
3. **Shared rubric:** Scoring criteria live in a dedicated file so they're transparent, version-controlled, and editable without touching skill logic.
4. **Report-only:** v1 assesses and reports. No auto-fix to keep scope focused.
5. **LLM-evaluated quality:** Instruction quality and similar subjective checks leverage the host LLM's judgment rather than brittle regex patterns.

## Future Considerations (Out of Scope for v1)

- Auto-remediation mode (generate missing files, improve instructions)
- CLI/CI mode for automated pipeline checks
- Deterministic scoring via custom agent runtime
- Historical tracking (score over time)
- Badge generation for README
- Community-contributed rubric extensions
