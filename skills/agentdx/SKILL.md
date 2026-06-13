---
name: agentdx
description: "Assess repository agentic engineering readiness. Scores agent configs, instruction quality, security, repo structure, setup, CI/CD, workflow, proof loops, and adaptability (0-100). Supports tool-scoped scans for Copilot, Claude, and Codex."
---

# AgentDX: Agentic Engineering Readiness Assessment

Assess how well a repository is set up for agentic engineering workflows. Produces a score (0-100), readiness level, proof level, confidence, and actionable findings.

**Trigger phrases:** "check agent readiness", "agentdx scan", "assess my repo for agents", "agentdx score", "how agent-friendly is this repo", "agentdx scan for copilot", "agentdx scan for claude"

## Instructions

When invoked, perform the following steps:

### 1. Determine Scan Scope

Ask the user (or infer from their request) which categories to scan:
- **Full scan** (default): All 9 categories
- **Selective scan**: Only specified categories

Also infer whether the request names a target tool/platform. Examples:
- "run agentdx scan for copilot" → target platform: GitHub Copilot
- "scan for claude" or "claude code readiness" → target platform: Claude Code
- "scan for codex" or "codex readiness" → target platform: OpenAI Codex

If no tool/platform is named, run a cross-platform scan. If a tool/platform is named, run a tool-scoped scan:
- Evaluate only issues relevant to that platform plus universal repository hygiene.
- Include universal/shared files such as `AGENTS.md`, `README.md`, `docs/`, CI, and general secret hygiene when the category uses them.
- Exclude non-target platform files from scoring, findings, and recommendations. For example, a Copilot scan MUST NOT inspect or penalize `CLAUDE.md`, `.claude/**`, `.codex-plugin/**`, or `.agents/**`; a Claude scan MUST NOT penalize missing `.github/copilot-instructions.md`, `.github/instructions/**`, `.codex-plugin/**`, or `.agents/**`; a Codex scan MUST NOT penalize missing `CLAUDE.md`, `.claude/**`, `.github/copilot-instructions.md`, or `.github/instructions/**`.
- Do not mention missing files for non-target platforms in the top recommendations.

Available categories:
1. Agent Config Files
2. Instruction Quality
3. Security & Git Hygiene
4. Repository Structure
5. CI/CD Integration
6. Development Workflow
7. Setup & Environment
8. Proof & Feedback Loops
9. Adaptability

### 2. Load Rubric

Read `skills/agentdx/shared/rubric.md` for scoring weights and grade labels.

### 3. Execute Category Checks

For each selected category, read the corresponding category file and evaluate the repository:

| Category | File |
|----------|------|
| Agent Config Files | `skills/agentdx/categories/agent-configs.md` |
| Instruction Quality | `skills/agentdx/categories/instruction-quality.md` |
| Security & Git Hygiene | `skills/agentdx/categories/security.md` |
| Repository Structure | `skills/agentdx/categories/repo-structure.md` |
| CI/CD Integration | `skills/agentdx/categories/ci-cd.md` |
| Development Workflow | `skills/agentdx/categories/development-workflow.md` |
| Setup & Environment | `skills/agentdx/categories/setup-environment.md` |
| Proof & Feedback Loops | `skills/agentdx/categories/proof-feedback.md` |
| Adaptability | `skills/agentdx/categories/adaptability.md` |

For each category:
1. Read the category file to understand what to check and how to score
2. Apply the target platform filter before reading or scoring platform-specific files
3. Detect the repository's tech stack using `skills/agentdx/shared/tech-stacks.md` as reference
4. Use your tools (file search, file reading) to examine the target repository
5. Evaluate each criterion as Pass/Partial/Fail
6. Calculate the category score (0–100)
7. Record individual findings with status emojis (✅ ⚠️ ❌)

Also capture cross-cutting evidence while scanning:
- `command_tiers`: bootstrap, doctor, boot, health, fast, quick, proof, ci_equivalent, smoke, seed_or_reset, observe, cleanup, retro_or_improve
- `ci_local_equivalence`: identical, equivalent, partial, divergent, local_only, ci_only, unknown
- `environment_variables`: names only, source files, required/optional/unknown, secret-like, example/default availability
- `external_dependencies`: dependency type, local option, remote/secret requirement, mutation risk, proof blockage
- `test_mechanisms`: mock, fake, sink, stub, contract, testcontainer, in_memory, fixture, factory, snapshot, reset
- `proof_surfaces`: static/build, runtime, consequence, external-effect, observability, human/inferential, production/customer

Do not print secret values. Inspect only safe example values such as `.env.example`; for real `.env` files, report names or presence only.

### 4. Calculate Overall Score

- If full scan: Use weights from rubric directly
- If selective: Re-weight selected categories proportionally to sum to 100%

Overall score = sum of (category_score × normalized_weight)

Then derive report-level signals from the category evidence:
- **Agent Readiness Level**: H0-H5 from `shared/rubric.md`
- **Highest Proof Level**: L0-L6 from `shared/rubric.md`
- **Target Next Proof Level**: the most valuable next proof level unlocked by the top recommendations
- **Confidence**: High, Medium, or Low from evidence coverage and unknowns

### 5. Format Report

Read `skills/agentdx/shared/report-format.md` and produce the report following that template exactly.

Include:
- Overall score with grade label
- Readiness level, highest proof level, target next proof level, and confidence
- Target platform when the scan is tool-scoped
- Category breakdown table
- Per-category findings
- Command tier summary, CI/local equivalence, environment/dependency pressure, and proof surface summary when evidence exists
- Top 3 recommendations (prioritized by potential score improvement)

### 6. Present Results

Output the report directly in chat AND save it to the repository:

1. **Chat output**: Always display the full report in the conversation
2. **File output**: Save the results to `.agentdx/` directory:
   - `.agentdx/report.md` — human-readable markdown report
   - `.agentdx/report.json` — machine-readable JSON (using the JSON schema from report-format.md)

The JSON file should include a `timestamp` field (ISO 8601) so teams can track score changes over time.

If `.agentdx/` directory doesn't exist, create it. If previous reports exist, overwrite them (latest scan only).

Suggest the user add `.agentdx/` to `.gitignore` if they don't want reports committed, or commit them for team visibility/CI tracking.

## Important Notes

- This is an assessment tool. Do NOT offer to fix issues unless the user explicitly asks.
- Be fair in scoring: not every project needs every feature. Context matters.
- Guidance is orientation, not proof. Prefer recommendations that encode important behavior as executable checks, fixtures, fakes, sinks, diagnostics, schemas, smoke paths, or architecture rules.
- Do not over-penalize desktop, mobile, hardware, brownfield, or library topologies for lacking web-style boot or UI smoke paths. Mark topology-intrinsic manual signals as informational.
- MCP and custom skills are scored as bonuses within Agent Config Files and Instruction Quality — their absence does not penalize.
- Instruction Quality depends on Agent Config Files — if no config files exist, Instruction Quality automatically scores 0.
- Tool-scoped scans must not score or recommend fixes for non-target platform files. A Copilot scan should ignore Claude-specific files entirely, including `CLAUDE.md` and `.claude/**`.
- Security: if no `.claude/` directory exists, only score secrets and sensitive files criteria.
- Be specific in findings. "Missing X" is better than "could be improved."
- If the user asks for JSON output, use the JSON format defined in the report format file.
- If the user wants fixes applied, suggest using the `agentdx-fix` agent instead.
