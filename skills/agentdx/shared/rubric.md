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
| Security & Git Hygiene | 20% | security |
| Repository Structure | 10% | repo-structure |
| CI/CD Integration | 10% | ci-cd |
| Development Workflow | 10% | development-workflow |

## Scoring Granularity

Each category contains multiple criteria. Score each criterion as:
- **Pass** (full points for that criterion)
- **Partial** (half points) — present but incomplete or low quality
- **Missing/Fail** (zero points)

Category score = (points earned / total possible points) × 100

Overall score = sum of (category score × category weight)

## Selective Scanning

When only some categories are selected, re-weight the selected categories proportionally so they sum to 100%. The overall score reflects only the scanned categories.

## Contextual Scoring Notes

- **Agent Config Files**: MCP configuration is scored as an informational bonus (+10 max). Projects without MCP are not penalized.
- **Instruction Quality**: Custom skills/commands are scored as an informational bonus (+10 max). Projects without custom skills are not penalized.
- **Security**: If no `.claude/` directory exists, score only the secrets and sensitive files criteria (gitignore checks become N/A).
- **Instruction Quality**: Depends on Agent Config Files — if no config files exist, this category automatically scores 0.
