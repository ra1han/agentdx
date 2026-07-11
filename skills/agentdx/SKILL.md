---
name: agentdx
description: Assess a repository's readiness for AI coding agents and developer automation. Use when asked to audit, score, review, or improve an agent-friendly repository setup, including agent instructions and configuration, safe environment setup, CI/local validation parity, proof loops, and maintainable development workflows. Supports full, category-scoped, and platform-scoped assessments for GitHub Copilot, Claude Code, and OpenAI Codex.
---

# AgentDX: Agentic Engineering Readiness Assessment

Assess a repository with direct evidence and produce an actionable 0–100 readiness report. Distinguish configured capabilities from capabilities that were actually exercised.

## 1. Resolve the Scope

Use the current workspace unless the user names a different repository or path. Before scanning, determine:

- **Categories:** Scan all nine categories by default. For a selective request, scan only the named categories.
- **Platform:** Use a cross-platform scan by default. When the user names GitHub Copilot, Claude Code, or OpenAI Codex, restrict platform-specific evaluation to that platform and universal repository hygiene.
- **Mode:** Assess read-only by default. Save a report or apply fixes only when the user explicitly asks.

For a platform-scoped scan, do not inspect, score, compare, or recommend files belonging only to another platform. Always keep `AGENTS.md`, general documentation, CI, setup contracts, and secret hygiene in scope where relevant.

For a cross-platform scan, treat platform-specific files as applicable only when the platform is explicitly requested or directly evidenced by its configuration. Do not infer GitHub Copilot usage from GitHub Actions alone, and do not penalize a repository for unused platform ecosystems.

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

## 2. Load the Relevant Rubric

Read [shared/rubric.md](shared/rubric.md), then read one category file for every selected category:

| Category | File |
|---|---|
| Agent Config Files | [categories/agent-configs.md](categories/agent-configs.md) |
| Instruction Quality | [categories/instruction-quality.md](categories/instruction-quality.md) |
| Security & Git Hygiene | [categories/security.md](categories/security.md) |
| Repository Structure | [categories/repo-structure.md](categories/repo-structure.md) |
| CI/CD Integration | [categories/ci-cd.md](categories/ci-cd.md) |
| Development Workflow | [categories/development-workflow.md](categories/development-workflow.md) |
| Setup & Environment | [categories/setup-environment.md](categories/setup-environment.md) |
| Proof & Feedback Loops | [categories/proof-feedback.md](categories/proof-feedback.md) |
| Adaptability | [categories/adaptability.md](categories/adaptability.md) |

Read [shared/tech-stacks.md](shared/tech-stacks.md) only when evaluating instruction coverage or command expectations. Read [shared/report-format.md](shared/report-format.md) before composing the report.

## 3. Gather Evidence Safely

Inspect tracked files, manifests, task runners, CI definitions, test layout, safe environment examples, and repository documentation. Use narrow searches first; inspect representative files rather than indiscriminately loading generated, vendored, binary, or dependency directories.

Do not read or print values from real `.env` files, keychains, credential stores, shell histories, or secret-management tools. Report only a variable's name, source, and inferred requirement. Treat secret-like matches as leads until their tracked-file context confirms a real exposure; exclude clearly fake fixtures and documentation examples.

Do not run bootstrap, build, test, start, deploy, seed, reset, or cleanup commands during an assessment unless the user asks or a command is clearly non-mutating and safe to run. Record command evidence precisely:

- **verified:** the current session ran the command successfully (or observed a deterministic failure).
- **configured_unverified:** a command is declared in a manifest, workflow, or documentation but was not run.
- **candidate_unverified:** a plausible command is inferred from project files but not declared as a supported workflow.
- **not_applicable:** the tier does not fit the repository topology.
- **unknown:** evidence is insufficient.

Never present configured or inferred commands as verified. Cite the file path, manifest entry, workflow job, or command output that supports every non-trivial finding.

For each selected category, apply the platform filter before opening platform-specific files; evaluate each criterion as Pass, Partial, Fail, or N/A; and record concise findings with a status emoji. Use N/A only for topology- or platform-inapplicable criteria, then reweight the remaining applicable criteria. Do not use N/A merely because evidence was not collected.

Capture these cross-cutting inventories when evidence exists:

- `command_tiers`: `bootstrap`, `setup_services`, `diagnostics`, `boot`, `health`, `fast`, `quick`, `proof`, `ci_equivalent`, `smoke`, `seed_or_reset`, `observe`, `cleanup`, `retro_or_improve`
- `ci_local_equivalence`: `identical`, `equivalent`, `partial`, `divergent`, `local_only`, `ci_only`, or `unknown`
- `environment_variables`: names only; source files; required, optional, or unknown; secret-like; example/default availability
- `external_dependencies`: type, local option, remote/secret requirement, mutation risk, and proof blockage
- `test_mechanisms`: mock, fake, sink, stub, contract, testcontainer, in-memory, fixture, factory, snapshot, or reset
- `proof_surfaces`: static/build, runtime, consequence, external-effect, observability, human/inferential, or production/customer

## 4. Calculate Scores and Signals

- For a full scan, use the weights in `shared/rubric.md`.
- For a selective scan, proportionally reweight only the selected categories to 100%.
- Keep category scores at their calculated precision, calculate the weighted overall score, then round only the final score to the nearest whole number.
- Derive the readiness level, highest proof level, target next proof level, and confidence from cited evidence. A target next proof level must be the next useful level unlocked by a concrete recommendation; do not claim runtime proof from static intent alone.

Score the repository as it is, not against an assumed web-application topology. Libraries, CLIs, desktop/mobile applications, hardware projects, documentation repositories, and brownfield systems may have valid N/A tiers. Unknown evidence should reduce confidence or earn only the criterion's justified partial credit, not become a fabricated pass.

## 5. Produce the Report

Follow [shared/report-format.md](shared/report-format.md). Include:

- overall score, grade, readiness/proof levels, target next proof level, and confidence;
- target platform and selective-scan scope when applicable;
- category scores, criteria findings, and evidence paths;
- command tiers, CI/local equivalence, environment/dependency pressure, and proof mechanisms when evidence exists; and
- three prioritized recommendations tied to observed gaps, including the expected proof-level improvement where relevant.

Recommendations must be proportionate and executable. Prefer a deterministic check, fixture, fake, sink, diagnostic, smoke path, or architecture rule over prose when that creates stronger evidence. Do not recommend configuration for an unobserved platform or introduce a task runner, service, dependency, or destructive operation without a clear need.

Display the full Markdown report in chat. If the user explicitly requests persisted output, write `.agentdx/report.md` and `.agentdx/report.json` using the JSON structure in `shared/report-format.md`; state that these files are being created or replaced before writing them. Include an ISO 8601 `timestamp` and never put secret values in either report.

## 6. Hand Off Remediation

This assessment does not modify repository files by default. If the user asks to improve the repository, use the `agentdx-fix` remediation agent or follow its workflow: propose evidence-backed, low-risk fixes, obtain the required approval for consequential changes, apply the smallest coherent set, and run relevant validation.
