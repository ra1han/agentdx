# Changelog

All notable changes to AgentDX will be documented in this file.

## [1.1.0] - 2026-06-06

### Added
- **Security & Git Hygiene category** — new category scanning for hardcoded secrets, gitignore patterns for agent files, dangerous permissions, and sensitive file exposure
- **Setup Doctor agent** (`agents/agentdx-fix.md`) — subagent that runs the assessment and offers to fix issues
- **JSON output mode** — request machine-readable output for CI integration
- **Platform detection** — informational reporting of which agent platforms are in use
- **Claude Code ecosystem checks** — `.claude/settings.json`, `.claude/rules/`, `.claude/agents/`, hooks detection

### Enhanced
- **Agent Config Files** — added Claude Code ecosystem criterion (settings, rules, skills, agents, hooks); added platform detection
- **Instruction Quality** — added Brevity & Focus criterion (line limits, bloat detection); added Structure & Cross-References criterion (heading detection, `@FILENAME` references, cross-file linking)
- **MCP Server Setup** — added Server Relevance & Hygiene criterion; added Security & Configuration Quality criterion (hardcoded secrets, HTTPS, wildcard detection)
- **Custom Skills/Commands** — added YAML frontmatter validation; skill length limits; unreferenced skills detection
- **Report format** — added platform detection section; estimated score improvement in recommendations; JSON output schema

### Changed
- Rebalanced category weights: Agent Config (25%→20%), Instruction Quality (25%→20%), Security (new, 15%), Repo Structure (10%→8%), CI/CD (10%→7%)
- Instruction Quality consistency check changed from 25% criterion to bonus/penalty system (deduct up to 10 points)

## [1.0.0] - 2026-06-05

### Added
- Initial release with 6 assessment categories
- Scoring rubric (0-100 with grade labels)
- Selective scanning support
- Report format with findings and recommendations
- Plugin packaging for GitHub Copilot CLI and Claude Code
