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
| Agent Config Files | 12% | agent-configs |
| Instruction Quality | 12% | instruction-quality |
| Security & Git Hygiene | 14% | security |
| Repository Structure | 8% | repo-structure |
| CI/CD Integration | 14% | ci-cd |
| Development Workflow | 8% | development-workflow |
| Setup & Environment | 10% | setup-environment |
| Proof & Feedback Loops | 14% | proof-feedback |
| Adaptability | 8% | adaptability |

## Scoring Granularity

Each category contains multiple criteria. Score each criterion as:
- **Pass** (full points for that criterion)
- **Partial** (half points) — present but incomplete or low quality
- **Missing/Fail** (zero points)

Category score = (points earned / total possible points) × 100

Overall score = sum of (category score × category weight)

## Readiness Levels

Report an Agent Readiness Level alongside the 0-100 score. This is an interpretive summary, not a separately weighted category.

| Level | Meaning |
|-------|---------|
| H0 Unknown | No reliable agent-facing command, setup, or validation surface was detected. |
| H1 Front Door Exists | A command entrypoint, instructions, or task runner exists, but commands are generic, incomplete, or mostly inferred. |
| H2 Assessed | Repo type, setup contract, commands, CI, services, and major proof gaps are mapped with file evidence. |
| H3 Operable | At least one local setup, Diagnostics, boot, smoke, or fast validation path is configured and usable by an agent. |
| H4 Proveable | A meaningful scenario can be exercised with a deterministic verdict and an observable consequence. |
| H5 Compounding | The repo records friction, proof artifacts, known difficulties, and harness improvements so agent workflows improve over time. |

Choose the highest level supported by evidence. Do not report H3 or higher solely from instructions that claim commands exist; command surfaces must be discoverable in files or recorded outputs.

## Proof Levels

Report the highest proof level detected for local or documented agent work, plus the most useful next proof level to target.

| Level | Meaning |
|-------|---------|
| L0 Claim | Guidance or human claim only; no runnable evidence. |
| L1 Command Output | A command can produce output, but it does not establish a deterministic verdict. |
| L2 Static/Build/Test | Build, lint, typecheck, unit, schema, security, architecture, or isolated tests can pass/fail deterministically. |
| L3 Runtime Interaction | A product, API, UI, CLI, worker, queue, MCP, or system path can be exercised. |
| L4 Interaction + Consequence | Runtime interaction plus verification of state, file, database, message, event, cache, artifact, log, trace, screenshot, or side-effect sink. |
| L5 Clean Rerun | The proof can be repeated from a clean or reset state with recorded steps. |
| L6 Production/Customer Evidence | Telemetry, SLOs, incident-free release evidence, or user/customer outcome evidence supports the claim. |

Static assessment alone should not claim L3 or above unless recorded evidence already exists and is cited.

## Confidence

Report confidence separately from the score:

| Confidence | Criteria |
|------------|----------|
| High | Most categories have direct file evidence; topology, commands, and proof gaps are clear. |
| Medium | Core evidence exists but some command, dependency, or test surfaces are inferred or not deeply inspected. |
| Low | Repo is sparse, inaccessible, generated, or dominated by unknown setup/proof surfaces. |

## Recommendation Prioritization

Prioritize recommendations that convert agent guidance or manual process into deterministic feedback:

1. executable commands, checks, fixtures, fakes, sinks, diagnostics, schema checks, smoke paths, or architecture rules;
2. local/CI command alignment and fast validation lanes;
3. environment examples, setup contracts, and documentation when orientation itself is the missing surface.

When both documentation and an executable check could solve the same proof gap, recommend the executable check first and explain the proof level it unlocks.

## Selective Scanning

When only some categories are selected, re-weight the selected categories proportionally so they sum to 100%. The overall score reflects only the scanned categories.

## Contextual Scoring Notes

- **Agent Config Files**: MCP configuration is scored as an informational bonus (+10 max). Projects without MCP are not penalized.
- **Instruction Quality**: Custom skills/commands are scored as an informational bonus (+10 max). Projects without custom skills are not penalized.
- **Security**: If no `.claude/` directory exists, score only the secrets and sensitive files criteria (gitignore checks become N/A).
- **Instruction Quality**: Depends on Agent Config Files — if no config files exist, this category automatically scores 0.
- **Setup & Environment**: Remote-only services are not automatically failures when the repo clearly documents safe sandboxes, fakes, dry-runs, or required secrets by name.
- **Proof & Feedback Loops**: Static assessment must not claim runtime proof unless a recorded command, test, workflow, artifact, or documented run evidence supports it.
- **Adaptability**: Score unknown rather than fail when language/tooling support makes coupling or complexity hard to inspect in a safe static scan.
