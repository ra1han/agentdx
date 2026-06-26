# Changelog

All notable changes to AgentDX will be documented in this file.

## [1.2.0] - 2026-06-14

### Added
- **Setup & Environment category** — scores env contracts, runtime/tool versions, local infrastructure, setup/Diagnostics paths, and external dependency pressure
- **Proof & Feedback Loops category** — scores deterministic sensors, boot/health/smoke paths, supported interactions, observable evidence, reset/fixtures, and reusable test mechanisms
- **Adaptability category** — scores seams, offline testability, side-effect sinks, state/contract verification, architecture boundaries, and inner-loop speed
- **Readiness levels (H0-H5)** — report-level summary of whether a repo is unknown, has a front door, is assessed, operable, proveable, or compounding
- **Proof levels (L0-L6)** — report-level summary of the strongest evidence a repo can provide for agent work
- **Command tier inventory** — bootstrap, Diagnostics, boot, health, fast, proof, CI-equivalent, smoke, seed/reset, observe, cleanup, and improvement-loop commands
- **Environment and dependency inventories** — machine-readable report fields for env var names, local substitutes, remote/secret requirements, mutation risk, and proof blockers

### Enhanced
- **CI/CD Integration** — now evaluates command tier coverage and local/CI equivalence in addition to documented commands and workflow files
- **Development Workflow** — now evaluates compounding improvement loops and manual/IDE-only operation signals
- **Repository Structure** — now evaluates repo maps and first-session onboarding surfaces
- **Report format** — now includes readiness, proof, confidence, command tiers, CI/local equivalence, dependency pressure, proof surfaces, and test mechanisms
- **Recommendations** — now prefer executable checks, fixtures, fakes, sinks, diagnostics, schemas, smoke paths, and architecture rules over prose-only guidance when proof is the gap

### Changed
- Rebalanced category weights across nine categories: Agent Config (12%), Instruction Quality (12%), Security (14%), Repo Structure (8%), CI/CD (14%), Development Workflow (8%), Setup & Environment (10%), Proof & Feedback Loops (14%), Adaptability (8%)

## [1.1.0] - 2026-06-06

### Added
- **Security & Git Hygiene category** — new category scanning for hardcoded secrets, gitignore patterns for agent files, dangerous permissions, and sensitive file exposure
- **Setup Diagnostics agent** (`agents/agentdx-fix.md`) — subagent that runs the assessment and offers to fix issues
- **JSON output mode** — request machine-readable output for CI integration
- **Platform detection** — informational reporting of which agent platforms are in use
- **Claude Code ecosystem checks** — `.claude/settings.json`, `.claude/rules/`, `.claude/agents/`, hooks detection

### Enhanced
- **Agent Config Files** — added Claude Code ecosystem criterion (settings, rules, skills, agents, hooks); added platform detection
- **Instruction Quality** — added Brevity & Focus criterion (line limits, bloat detection); added Structure & Cross-References criterion (heading detection, `@FILENAME` references, cross-file linking)
- **MCP bonus checks** — added Server Relevance & Hygiene checks and Security & Configuration Quality checks (hardcoded secrets, HTTPS, wildcard detection) inside Agent Config Files
- **Custom Skills/Commands bonus checks** — added YAML frontmatter validation, skill length limits, and unreferenced skills detection inside Instruction Quality
- **Report format** — added platform detection section; estimated score improvement in recommendations; JSON output schema

### Changed
- Rebalanced category weights: Agent Config (25%), Instruction Quality (25%), Security & Git Hygiene (20%), Repo Structure (10%), CI/CD Integration (10%), Development Workflow (10%)
- Instruction Quality consistency check changed from 25% criterion to bonus/penalty system (deduct up to 10 points)

## [1.0.0] - 2026-06-05

### Added
- Initial release with 6 assessment categories
- Scoring rubric (0-100 with grade labels)
- Selective scanning support
- Report format with findings and recommendations
- Plugin packaging for GitHub Copilot CLI and Claude Code
