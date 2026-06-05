# AI-Ready: Agentic Engineering Readiness Assessment

Assess how well a repository is set up for agentic engineering workflows. Produces a score (0–100) with actionable findings.

**Trigger phrases:** "check agent readiness", "ai-ready scan", "assess my repo for agents", "agentdx score", "how agent-friendly is this repo"

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
