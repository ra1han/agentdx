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
| Agent Config Files | 20% | agent-configs |
| Instruction Quality | 20% | instruction-quality |
| MCP Server Setup | 15% | mcp-setup |
| Custom Skills/Commands | 15% | custom-skills |
| Security & Git Hygiene | 15% | security |
| Repository Structure | 8% | repo-structure |
| CI/CD Integration | 7% | ci-cd |

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

- **MCP and Custom Skills**: If a project is simple enough that these don't apply, note this context and give a neutral score (50) rather than penalizing with 0.
- **Security**: If no `.claude/` directory exists, score only the secrets and sensitive files criteria (gitignore checks become N/A).
- **Instruction Quality**: Depends on Agent Config Files — if no config files exist, this category automatically scores 0.
