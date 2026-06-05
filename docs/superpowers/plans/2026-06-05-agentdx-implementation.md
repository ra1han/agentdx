# AgentDX Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create the AgentDX skill files that assess repository agentic engineering readiness and produce a scored report.

**Architecture:** Modular prompt-based skill with an orchestrator (`agentdx/SKILL.md`) that references category-specific check files and a shared rubric. No code — pure markdown/prompt content that runs inside Copilot or Claude Code sessions.

**Tech Stack:** Markdown skill files (no runtime dependencies)

---

## File Structure

```
skills/
├── agentdx.md               # Orchestrator: entry point, selective/full scan logic, report assembly
├── categories/
│   ├── agent-configs.md     # Check: agent config file presence & completeness
│   ├── instruction-quality.md # Check: prompt/instruction quality evaluation
│   ├── mcp-setup.md        # Check: MCP server configuration
│   ├── custom-skills.md    # Check: custom skills/commands setup
│   ├── repo-structure.md   # Check: agent-helpful repo organization
│   └── ci-cd.md            # Check: CI/CD documentation for agents
└── shared/
    ├── rubric.md           # Scoring criteria, weights, grade labels
    └── report-format.md   # Output template
```

---

### Task 1: Shared Rubric

**Files:**
- Create: `skills/shared/rubric.md`

- [ ] **Step 1: Create rubric file**

Create `skills/shared/rubric.md` with scoring weights, criteria definitions, and grade labels:

```markdown
# AgentDX Scoring Rubric

## Grade Labels

| Score Range | Grade |
|-------------|-------|
| 90–100 | Excellent |
| 70–89 | Good |
| 50–69 | Fair |
| 0–49 | Needs Work |

## Category Weights

| Category | Weight | Key |
|----------|--------|-----|
| Agent Config Files | 25% | agent-configs |
| Instruction Quality | 25% | instruction-quality |
| MCP Server Setup | 15% | mcp-setup |
| Custom Skills/Commands | 15% | custom-skills |
| Repository Structure | 10% | repo-structure |
| CI/CD Integration | 10% | ci-cd |

## Scoring Granularity

Each category contains multiple criteria. Score each criterion as:
- **Pass** (full points for that criterion)
- **Partial** (half points) — present but incomplete or low quality
- **Missing/Fail** (zero points)

Category score = (points earned / total possible points) × 100

Overall score = sum of (category score × category weight)

## Selective Scanning

When only some categories are selected, re-weight the selected categories proportionally so they sum to 100%. The overall score reflects only the scanned categories.
```

- [ ] **Step 2: Commit**

```bash
git add skills/shared/rubric.md
git commit -m "feat: add scoring rubric with weights and grade labels"
```

---

### Task 2: Report Format Template

**Files:**
- Create: `skills/shared/report-format.md`

- [ ] **Step 1: Create report format file**

Create `skills/shared/report-format.md`:

```markdown
# AgentDX Report Format

When presenting results, use this exact structure:

## Header

```
## AgentDX Score: {overall_score}/100 ({grade_label})
```

## Category Breakdown Table

```
### Category Breakdown
| Category              | Score | Status |
|-----------------------|-------|--------|
| {category_name}       | {score} | {status_emoji} |
```

Status emoji rules:
- Score >= 80: ✅
- Score 50–79: ⚠️
- Score < 50: ❌

## Findings Section

For each category, list individual findings:

```
#### {Category Name} ({score}/100)
- ✅ {passing criterion description}
- ⚠️ {partial criterion description}
- ❌ {missing/failing criterion description}
```

## Recommendations

End with the top 3 most impactful recommendations, ordered by potential score improvement:

```
### Top 3 Recommendations
1. {highest impact action}
2. {second highest impact action}
3. {third highest impact action}
```

## Selective Scan Note

When running a selective scan, add after the header:
```
> Scanned categories: {list}. Score reflects only selected categories.
```
```

- [ ] **Step 2: Commit**

```bash
git add skills/shared/report-format.md
git commit -m "feat: add report format template"
```

---

### Task 3: Category — Agent Config Files

**Files:**
- Create: `skills/categories/agent-configs.md`

- [ ] **Step 1: Create category file**

Create `skills/categories/agent-configs.md`:

```markdown
# Category: Agent Config Files

## Purpose
Assess the presence, location, and completeness of agent configuration files in the repository.

## Files to Check

| File | Platform | Expected Location |
|------|----------|-------------------|
| `AGENTS.md` | Universal | Repository root |
| `.github/copilot-instructions.md` | GitHub Copilot | `.github/` directory |
| `CLAUDE.md` | Claude Code | Repository root |
| `.cursorrules` | Cursor | Repository root |
| `.windsurfrules` | Windsurf | Repository root |

## Criteria

### 1. File Existence (40% of category score)
For each file above, check if it exists in the expected location.
- **Pass**: File exists at correct location
- **Partial**: File exists but in wrong location (e.g., `copilot-instructions.md` at root instead of `.github/`)
- **Missing**: File does not exist

Score: (files found / 5) × 100. Award partial credit (half) for mislocated files.

Note: Not every project needs all 5 files. Award full marks if at least 2 platform-specific files exist AND they cover the platforms the project actually uses (check for platform indicators like `.github/workflows/` for Copilot, existence of other `.cursor/` files for Cursor, etc.).

### 2. Minimum Substance (30% of category score)
For each found file, verify it has meaningful content:
- **Pass**: >50 words of substantive content (not boilerplate/placeholders)
- **Partial**: 20–50 words, or contains placeholder text ("TODO", "Add instructions here")
- **Fail**: <20 words or entirely placeholder content

### 3. Key Sections (30% of category score)
For each substantive file, check for coverage of these topics:
- Project overview / what this project is
- Coding conventions / style guidelines
- Tooling / how to build, test, lint
- Constraints / things the agent should NOT do

Scoring:
- **Pass**: 3–4 topics covered
- **Partial**: 1–2 topics covered
- **Fail**: 0 topics covered

## How to Evaluate

1. Use file search to check for each file in the expected location
2. Read found files and assess word count (excluding code blocks for word count)
3. Evaluate whether content covers the key sections listed above
4. Calculate category score using the weights above
```

- [ ] **Step 2: Commit**

```bash
git add skills/categories/agent-configs.md
git commit -m "feat: add agent-configs category check"
```

---

### Task 4: Category — Instruction Quality

**Files:**
- Create: `skills/categories/instruction-quality.md`

- [ ] **Step 1: Create category file**

Create `skills/categories/instruction-quality.md`:

```markdown
# Category: Instruction Quality

## Purpose
Evaluate the quality of content in agent configuration files — not just presence, but whether the instructions actually help an agent do good work.

## Prerequisites
This category evaluates files found by the "Agent Config Files" category. If no agent config files exist, this category scores 0.

## Criteria

### 1. Specificity (25% of category score)
Does the file give concrete, specific guidance rather than vague platitudes?

- **Pass**: Contains specific examples, exact file paths, concrete patterns (e.g., "Use snake_case for Python files in src/", "API routes follow REST conventions in routes/api/v1/")
- **Partial**: Mix of specific and vague guidance (e.g., "Follow good coding practices" alongside some specific rules)
- **Fail**: Entirely vague (e.g., "Write clean code", "Follow best practices")

### 2. Actionability (25% of category score)
Does it tell the agent what to DO, not just what the project IS?

- **Pass**: Contains action-oriented instructions (e.g., "Run `npm test` before committing", "Always add types to function parameters", "Use the ErrorBoundary component for new pages")
- **Partial**: Mostly descriptive with some actionable guidance
- **Fail**: Purely descriptive (reads like a README, not instructions)

### 3. Completeness (25% of category score)
Does it cover the key areas an agent needs to work effectively?

Check for coverage of:
- Architecture / project structure explanation
- Coding conventions (naming, patterns, style)
- Tooling commands (build, test, lint, format)
- Constraints / prohibitions (what NOT to do)
- Dependencies / key libraries and their usage patterns

Scoring:
- **Pass**: 4–5 areas covered
- **Partial**: 2–3 areas covered
- **Fail**: 0–1 areas covered

### 4. Consistency (25% of category score)
If multiple config files exist, do they agree with each other?

- **Pass**: No contradictions across files (or only one config file exists)
- **Partial**: Minor inconsistencies (e.g., different terminology for same concept)
- **Fail**: Direct contradictions (e.g., one file says "use tabs" another says "use spaces")

## How to Evaluate

1. Read all agent config files found in the repository
2. For each criterion, evaluate across ALL config files (not just one)
3. Use your judgment as an experienced developer: would these instructions actually help you work in this codebase?
4. Calculate category score using the weights above
```

- [ ] **Step 2: Commit**

```bash
git add skills/categories/instruction-quality.md
git commit -m "feat: add instruction-quality category check"
```

---

### Task 5: Category — MCP Server Setup

**Files:**
- Create: `skills/categories/mcp-setup.md`

- [ ] **Step 1: Create category file**

Create `skills/categories/mcp-setup.md`:

```markdown
# Category: MCP Server Setup

## Purpose
Assess whether the repository has MCP (Model Context Protocol) server configurations that extend agent capabilities with project-specific tools.

## Files to Check

| File | Platform | Expected Location |
|------|----------|-------------------|
| `mcp.json` | Universal | Repository root |
| `.vscode/mcp.json` | VS Code / Copilot | `.vscode/` directory |
| `.cursor/mcp.json` | Cursor | `.cursor/` directory |
| `claude_desktop_config.json` | Claude Desktop | Repository root (reference) |
| `.github/copilot/mcp.json` | GitHub Copilot | `.github/copilot/` directory |

## Criteria

### 1. Configuration Exists (30% of category score)
At least one MCP configuration file is present.

- **Pass**: One or more MCP config files found
- **Partial**: MCP is referenced in documentation but no config file exists
- **Fail**: No MCP configuration or references found

Note: MCP is relatively new. If a project has no need for custom tools, scoring 0 here is acceptable. The orchestrator should note this context in recommendations.

### 2. Servers Defined (30% of category score)
The config file defines at least one MCP server.

- **Pass**: One or more servers with valid transport configuration (stdio, SSE, or streamable HTTP)
- **Partial**: Server defined but missing transport or connection details
- **Fail**: Empty config or no servers defined

### 3. Tool Descriptions (20% of category score)
If servers define tools, do they have clear descriptions?

- **Pass**: Tools have descriptions that explain what they do and when to use them
- **Partial**: Tools exist but descriptions are minimal or generic
- **Fail**: Tools have no descriptions, or no tools are defined
- **N/A**: If no tools are explicitly listed (some servers expose tools dynamically), score as Pass

### 4. No Placeholder Values (20% of category score)
Configuration contains real values, not placeholders.

- **Pass**: All values are real (URLs, paths, env var references)
- **Partial**: Some placeholder values but structure is correct
- **Fail**: Config is mostly placeholder/example content

## How to Evaluate

1. Search for MCP config files in expected locations
2. If found, read and evaluate the JSON structure
3. Check for server definitions, tool descriptions, and placeholder content
4. If no MCP files exist, check README/docs for MCP references
5. Calculate category score using the weights above
```

- [ ] **Step 2: Commit**

```bash
git add skills/categories/mcp-setup.md
git commit -m "feat: add mcp-setup category check"
```

---

### Task 6: Category — Custom Skills/Commands

**Files:**
- Create: `skills/categories/custom-skills.md`

- [ ] **Step 1: Create category file**

Create `skills/categories/custom-skills.md`:

```markdown
# Category: Custom Skills/Commands

## Purpose
Assess whether the repository defines custom skills, commands, or workflows that enhance agent capabilities for project-specific tasks.

## What to Look For

Custom skills can take many forms:
- Skill markdown files (in a `skills/` or `.copilot/skills/` directory)
- Custom slash commands (defined in agent config or dedicated files)
- Workflow definitions (multi-step procedures documented for agent use)
- Prompt templates (reusable prompts for common tasks)

## Directories/Files to Check

| Pattern | Description |
|---------|-------------|
| `skills/` or `**/skills/*.md` | Skill definition files |
| `.copilot/` | Copilot-specific customizations |
| `.github/copilot/` | GitHub Copilot configurations |
| `prompts/` or `.prompts/` | Prompt templates |
| Custom commands in agent config files | Inline command definitions |

## Criteria

### 1. Skills/Commands Exist (35% of category score)
At least one custom skill, command, or workflow is defined.

- **Pass**: One or more custom skills/commands found
- **Partial**: Skills are referenced in docs but files are missing or empty
- **Fail**: No custom skills, commands, or workflows found

Note: Not every project needs custom skills. If the project is small or simple, 0 here may be appropriate. The orchestrator should contextualize.

### 2. Clear Descriptions (25% of category score)
Each skill/command has a description explaining its purpose.

- **Pass**: All skills have clear descriptions of what they do and when to use them
- **Partial**: Some skills lack descriptions or have vague ones
- **Fail**: No descriptions on any skills

### 3. Well-Defined Triggers (20% of category score)
Skills have clear invocation patterns (how does the user/agent trigger them?).

- **Pass**: Trigger phrases, command names, or invocation instructions are explicit
- **Partial**: Some skills have triggers, others are unclear
- **Fail**: No clear way to invoke the skills

### 4. Project-Specific Value (20% of category score)
Skills address actual project workflows, not generic tasks.

- **Pass**: Skills cover project-specific workflows (e.g., "deploy to staging", "run migration", "create new API endpoint following our pattern")
- **Partial**: Mix of generic and project-specific skills
- **Fail**: Only generic skills (e.g., "write a test") or no skills

## How to Evaluate

1. Search for skill directories and files using the patterns above
2. Check agent config files for inline command definitions
3. Read found skills and evaluate against criteria
4. Calculate category score using the weights above
```

- [ ] **Step 2: Commit**

```bash
git add skills/categories/custom-skills.md
git commit -m "feat: add custom-skills category check"
```

---

### Task 7: Category — Repository Structure

**Files:**
- Create: `skills/categories/repo-structure.md`

- [ ] **Step 1: Create category file**

Create `skills/categories/repo-structure.md`:

```markdown
# Category: Repository Structure

## Purpose
Assess whether the repository is organized in a way that helps agents understand and navigate the codebase effectively.

## Criteria

### 1. README Quality (30% of category score)
Can an agent understand the project from the README alone?

- **Pass**: README covers: what the project does, how to set it up, how to run it, project structure overview
- **Partial**: README exists but is incomplete (e.g., only describes what it is, not how to use it)
- **Fail**: No README, or README is a template/placeholder

### 2. Contributing Guide (20% of category score)
Is there guidance for contributors (including agent contributors)?

- **Pass**: CONTRIBUTING.md or equivalent section in README with development workflow, PR process, code standards
- **Partial**: Brief contributing notes exist but lack detail
- **Fail**: No contributing guidance

### 3. Documentation Folder (25% of category score)
Is there organized documentation beyond the README?

- **Pass**: `docs/` folder (or equivalent) with meaningful content — architecture docs, API docs, decision records
- **Partial**: Some docs exist but are sparse, outdated, or disorganized
- **Fail**: No documentation beyond README

### 4. Clear Folder Organization (25% of category score)
Is the codebase logically organized so an agent can find things?

- **Pass**: Clear top-level organization (src/, tests/, docs/), not deeply nested (max 4-5 levels), naming is descriptive
- **Partial**: Mostly organized but some unclear directories or overly deep nesting
- **Fail**: Flat structure with many files at root, cryptic folder names, or extreme nesting

## How to Evaluate

1. Read the README.md and assess its completeness
2. Check for CONTRIBUTING.md or contributing section in README
3. Look for a docs/ directory and assess content quality
4. List the top-level directory structure and evaluate organization
5. Calculate category score using the weights above
```

- [ ] **Step 2: Commit**

```bash
git add skills/categories/repo-structure.md
git commit -m "feat: add repo-structure category check"
```

---

### Task 8: Category — CI/CD Integration

**Files:**
- Create: `skills/categories/ci-cd.md`

- [ ] **Step 1: Create category file**

Create `skills/categories/ci-cd.md`:

```markdown
# Category: CI/CD Integration

## Purpose
Assess whether build, test, and lint commands are documented and discoverable so agents can validate their own work.

## Criteria

### 1. Commands in Agent Config (35% of category score)
Are build/test/lint commands explicitly documented in agent configuration files?

- **Pass**: Agent config files contain specific, runnable commands (e.g., "Run `npm run test` to run tests", "Lint with `ruff check .`")
- **Partial**: Commands are mentioned but vague (e.g., "run the tests", "use the linter")
- **Fail**: No commands documented in agent config files

### 2. Commands Are Specific and Runnable (25% of category score)
Can an agent copy-paste the commands and run them?

- **Pass**: Commands include exact invocation, any required flags, and expected behavior (e.g., "`pytest tests/ -v` — runs all tests, expect ~30s")
- **Partial**: Commands are specific but missing context (e.g., `npm test` without noting it requires `npm install` first)
- **Fail**: Commands are vague or missing

### 3. Workflow Files Present (20% of category score)
Does the repo have CI/CD workflow definitions?

- **Pass**: `.github/workflows/`, `Jenkinsfile`, `.gitlab-ci.yml`, or equivalent exists with meaningful jobs
- **Partial**: Workflow file exists but is minimal or only does one thing
- **Fail**: No CI/CD workflow files

### 4. Discoverable Scripts (20% of category score)
Are build/test/lint scripts easy to find via standard patterns?

Check for:
- `package.json` scripts section (Node.js)
- `Makefile` with standard targets (build, test, lint)
- `pyproject.toml` with scripts/tool configuration
- `Taskfile.yml`, `justfile`, or similar task runners

Scoring:
- **Pass**: Standard script runner with build/test/lint targets defined
- **Partial**: Scripts exist but only cover some tasks, or non-standard runner
- **Fail**: No discoverable script definitions

## How to Evaluate

1. Read agent config files and search for command references
2. Check for CI/CD workflow files in standard locations
3. Look for package.json/Makefile/pyproject.toml script sections
4. Assess whether an agent could independently run build/test/lint
5. Calculate category score using the weights above
```

- [ ] **Step 2: Commit**

```bash
git add skills/categories/ci-cd.md
git commit -m "feat: add ci-cd category check"
```

---

### Task 9: Main Orchestrator Skill

**Files:**
- Create: `skills/agentdx.md`

- [ ] **Step 1: Create the orchestrator skill**

Create `skills/agentdx.md` — this is the main entry point that ties everything together:

```markdown
# AgentDX: Agentic Engineering Readiness Assessment

Assess how well a repository is set up for agentic engineering workflows. Produces a score (0–100) with actionable findings.

**Trigger phrases:** "check agent readiness", "agentdx scan", "assess my repo for agents", "agentdx score", "how agent-friendly is this repo"

## Instructions

When invoked, perform the following steps:

### 1. Determine Scan Scope

Ask the user (or infer from their request) which categories to scan:
- **Full scan** (default): All 6 categories
- **Selective scan**: Only specified categories

Available categories:
1. Agent Config Files
2. Instruction Quality
3. MCP Server Setup
4. Custom Skills/Commands
5. Repository Structure
6. CI/CD Integration

### 2. Load Rubric

Read `skills/shared/rubric.md` for scoring weights and grade labels.

### 3. Execute Category Checks

For each selected category, read the corresponding category file and evaluate the repository:

| Category | File |
|----------|------|
| Agent Config Files | `skills/categories/agent-configs.md` |
| Instruction Quality | `skills/categories/instruction-quality.md` |
| MCP Server Setup | `skills/categories/mcp-setup.md` |
| Custom Skills/Commands | `skills/categories/custom-skills.md` |
| Repository Structure | `skills/categories/repo-structure.md` |
| CI/CD Integration | `skills/categories/ci-cd.md` |

For each category:
1. Read the category file to understand what to check and how to score
2. Use your tools (file search, file reading) to examine the target repository
3. Evaluate each criterion as Pass/Partial/Fail
4. Calculate the category score (0–100)
5. Record individual findings with status emojis (✅ ⚠️ ❌)

### 4. Calculate Overall Score

- If full scan: Use weights from rubric directly
- If selective: Re-weight selected categories proportionally to sum to 100%

Overall score = sum of (category_score × normalized_weight)

### 5. Format Report

Read `skills/shared/report-format.md` and produce the report following that template exactly.

Include:
- Overall score with grade label
- Category breakdown table
- Per-category findings
- Top 3 recommendations (prioritized by potential score improvement)

### 6. Present Results

Output the report directly in chat. Do not write it to a file unless the user asks.

## Important Notes

- This is an assessment tool. Do NOT offer to fix issues unless the user explicitly asks.
- Be fair in scoring: not every project needs every feature. Context matters.
- For MCP and Custom Skills categories: if a project is simple enough that these don't apply, note this and give a neutral score rather than penalizing.
- Instruction Quality depends on Agent Config Files — if no config files exist, Instruction Quality automatically scores 0.
- Be specific in findings. "Missing X" is better than "could be improved."
```

- [ ] **Step 2: Commit**

```bash
git add skills/agentdx.md
git commit -m "feat: add agentdx orchestrator skill"
```

---

### Task 10: README

**Files:**
- Create: `README.md` (replace default)

- [ ] **Step 1: Create README**

Create `README.md`:

```markdown
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

### GitHub Copilot (Superpowers)

Clone or add as a plugin:
```bash
git clone https://github.com/ra1han/agentdx.git ~/.copilot/installed-plugins/agentdx
```

### Claude Code

Reference the skill path in your project configuration or symlink the skills directory.

## License

MIT
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "feat: add project README with usage and installation docs"
```

---

### Task 11: Final Verification

- [ ] **Step 1: Verify all files exist**

Run: `find skills/ -type f` (or equivalent) and confirm all expected files are present:
- `skills/agentdx.md`
- `skills/shared/rubric.md`
- `skills/shared/report-format.md`
- `skills/categories/agent-configs.md`
- `skills/categories/instruction-quality.md`
- `skills/categories/mcp-setup.md`
- `skills/categories/custom-skills.md`
- `skills/categories/repo-structure.md`
- `skills/categories/ci-cd.md`
- `README.md`

- [ ] **Step 2: Verify git status is clean**

Run: `git status`
Expected: nothing to commit, working tree clean
