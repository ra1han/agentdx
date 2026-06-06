# Claude Code Agent-Friendliness Audit Plugin

## Executive Summary  
The proliferation of AI coding agents has made **“agent-friendly”** repositories a priority for developers. An agent-friendly repo provides clear instructions, modular skills, reliable tooling, and robust metadata so that a coding AI (like Claude Code) can operate autonomously with high confidence. We propose a Claude Code plugin that scans a project’s files and configuration to detect gaps in agent readiness. It checks for key items (instruction files, skill manifests, MCP servers, tests, CI, dependency locks, etc.) and scores the repo on *agent-friendliness*. This report presents a comprehensive checklist of such items (with their importance, detection methods, severity, and fixes), algorithms and heuristics for automated checks, a scoring rubric, packaging/distribution guidelines, CI/CD integration, and security/privacy considerations. We cite Claude/Anthropic documentation and community best practices throughout. A Mermaid flowchart outlines the analysis pipeline and tables compare checks by complexity, false-positive risk, and remediation effort. Finally, we provide actionable next steps and an implementation roadmap with milestones.

## Agent-Friendly Checklist  
A thorough agent-friendly audit inspects repository structure and agent configuration. Key items to check include:

- **Agent Instructions File (AGENTS.md or CLAUDE.md)** – *Why:* Supplies high-level project goals, setup steps, and conventions for the agent. Without it, an agent lacks project context and may guess incorrectly. *Detect:* Look for `AGENTS.md` or `CLAUDE.md` at repo root. Also check for `.claude/rules/*.md` or similar (e.g. Gemini’s `GEMINI.md`) used by coding assistants. *Severity:* High if missing – agent may miss project conventions. *Remediation:* Add a descriptive file (800+ characters) that explains the project purpose, installation, common commands, style guides, etc..

- **Skill Manifests (SKILL.md)** – *Why:* Skills extend Claude’s knowledge with detailed instructions, docs, or scripts. Each skill (invokable via `/skill-name`) must have its own folder with a `SKILL.md` file. Missing or malformed skill files prevent the agent from loading that context. *Detect:* Scan for a `skills/` directory; ensure each subfolder has a `SKILL.md` with YAML frontmatter (name, description, tags, metadata like `version` or `mcp-server`) and content. For example:
  ```yaml
  metadata:
    author: ProjectHub
    version: 1.0.0
    mcp-server: projecthub
  ```
  The `version` and `mcp-server` fields are encouraged. *Severity:* High if absent – no domain-specific assistance. *Remediation:* Create appropriate `skills/skill-name/SKILL.md` files with clear descriptions, usage examples, and specify any required MCP servers or dependencies.

- **Agent Subagents (agents/)** – *Why:* Subagents are specialized agents (with their own prompt and tools) that Claude can invoke for tasks. A plugin’s `agents/` directory should contain Markdown files (with frontmatter fields like `name`, `description`, `model`, etc.) describing each agent. *Detect:* Look for an `agents/` folder and verify each `.md` file has proper YAML fields. *Severity:* Medium – missing agents means Claude uses only its defaults. *Remediation:* Add agent definitions with clear system prompts and constraints (e.g. set `tools`, `memory`, `permissions`).

- **Hooks (hooks/hooks.json)** – *Why:* Event hooks allow automation (e.g. formatting after edits). A plugin’s `hooks/` directory (or inline `plugin.json`) should configure hooks for lifecycle events. *Detect:* Check for a `hooks/hooks.json` file. *Severity:* Low – missing hooks just means fewer automations. *Remediation:* Add hook configuration JSON if needed, or integrate existing scripts via `claude plugin init --with hooks`.

- **MCP Servers (.mcp.json)** – *Why:* Custom tools (“MCP connectors”) let the agent access external data/services. The plugin should define its MCP servers in a root `.mcp.json` or in the `plugin.json` under `mcpServers`. *Detect:* Search for `.mcp.json` or `mcpServers` in `plugin.json`. *Severity:* Medium – without it, agent can’t use required data sources. *Remediation:* Define each MCP server (e.g. HTTP or stdio protocols) with endpoints and auth in `.mcp.json` (see docs) or set `mcpServers` field.

- **Dependency Manifest (package.json, pyproject.toml, etc.)** – *Why:* A machine-readable dependency list allows the agent to reproduce the environment. *Detect:* Look for `package.json`, `requirements.txt`, `pyproject.toml`, `go.mod`, etc., and corresponding lockfiles. *Severity:* High if missing – agent may install wrong versions or fail to build. *Remediation:* Ensure a proper manifest and lockfile are committed, e.g. `pip freeze > requirements.txt` or include `package-lock.json`/`yarn.lock`.

- **Build/Env Scripts (Makefile, Dockerfile, devcontainer)** – *Why:* Agents benefit from one-step setup (e.g. `make init` or `docker build`). *Detect:* Look for `Makefile`, `Dockerfile`, `devcontainer.json`, etc. *Severity:* Medium – without it, agent may struggle to set up (especially in CI). *Remediation:* Provide a setup script or container definition so the agent can bootstrap the project with a single command.

- **Tests (tests/, __tests__)** – *Why:* Agents rely on feedback to verify changes. A clear test suite (unit or integration) helps the agent validate its actions. *Detect:* Check for common test directories or configurations (e.g. `jest.config.js`, `pytest.ini`). *Severity:* High – without tests, agent edits may go unchecked. *Remediation:* Add a tests folder with runnable tests; document test commands in AGENTS.md or README.

- **CI Configuration (.github/workflows/, .gitlab-ci.yml)** – *Why:* A defined CI pipeline ensures consistent checks and gives agents a known integration process. *Detect:* Look for workflow files (e.g. `.github/workflows/ci.yml`) or CI settings. *Severity:* Medium – missing CI means no automated verification. *Remediation:* Commit a CI workflow that runs tests, linters, etc., on PRs.

- **Linter/Formatter Config (e.g. ESLint, Prettier)** – *Why:* Immediate style feedback helps agents avoid drifting from project conventions. *Detect:* Identify config files (like `.eslintrc`, `.prettierrc`, `pyproject.toml` with black config). *Severity:* Low – code style is best-effort. *Remediation:* Add and enforce a linter/formatter config, run it on pre-commit or CI.

- **Versioning (plugin.json `version` / VCS tags)** – *Why:* Semantic versioning in `plugin.json` or tags ensures reproducible plugin installs. *Detect:* Check `plugin.json` for a `version` field or Git tags. *Severity:* Low – missing version means updates are always new. *Remediation:* Use semantic versions in `plugin.json` so updates only occur when bumped.

- **Documentation (README, CONTRIBUTING, Examples)** – *Why:* Beyond AGENTS.md, a good README and examples (how to use the plugin/agent) guide both humans and agents. *Detect:* Check for README.md, CONTRIBUTING.md, example scripts or notebooks. *Severity:* Medium – lacking docs makes onboarding harder. *Remediation:* Improve README with project overview, usage, examples, and add CONTRIBUTING.md for process guidelines.

- **License** – *Why:* Clarifies how the code (and any agent-generated content) can be used. *Detect:* Look for `LICENSE` or `COPYING`. *Severity:* Medium – no license creates legal uncertainty. *Remediation:* Include a clear open-source license file at repo root.

- **Secrets and Security** – *Why:* Agents should not see passwords or keys. Sensitive files (e.g. `.git-credentials`, AWS creds, `.npmrc`) must be excluded or sanitized. *Detect:* Scan for known credential filenames or secrets (regex for API keys). *Severity:* Critical – leaking secrets can compromise accounts. *Remediation:* Remove secrets from code, use environment variables, and configure a secrets proxy if needed.

- **Telemetry/Monitoring** – *Why:* For production-grade agents, logging and metrics (e.g. via OpenTelemetry) help track performance. *Detect:* Look for telemetry SDK imports or instrumentation setup. *Severity:* Low to Medium. *Remediation:* Integrate a telemetry library if needed (optional, based on use case).

- **Prompts and Rate Limits** – *Why:* Prebuilt prompt templates (rules, guardrails) can guide the agent, and compliance with API rate-limit policies is required. *Detect:* Check for prompt files (e.g. additional `.md` in `.claude/rules/`) and ensure `ANTHROPIC_API_KEY` is used (not storing credentials). *Severity:* High if policy-violating. *Remediation:* Use dedicated prompt files for rules; do not embed API keys or fake rate-limit mechanisms – rely on Anthropic’s API keys.

Each check above can be implemented via static file search (e.g. patterns like `^AGENTS\.md$`), simple regex (e.g. check `version` in JSON), or parsing manifests. Runtime probes (e.g. launching `claude plugin validate`) can catch schema errors. The following section details heuristics and code snippets for automation.

## Automated Checks and Heuristics  
We propose implementing checks as a combination of static analysis and CLI probes. Examples:

- **File/Pattern Checks:**  
  Use filesystem scans or regex to detect files or content. For instance, in Python:
  ```python
  import os, re
  files = os.listdir(repo_root)
  # Check for instructions file
  if any(name.lower() in ("agents.md","claude.md") for name in files):
      instructions_ok = True
  # Check for SKILL.md in each skills subfolder
  skills_dir = os.path.join(repo_root, "skills")
  for d in os.listdir(skills_dir):
      if not os.path.isfile(os.path.join(skills_dir, d, "SKILL.md")):
          report.issue("Missing SKILL.md in " + d)
  # Check for dependency manifest
  if not os.path.exists(os.path.join(repo_root, "package.json")):
      report.error("No package.json found")
  ```
  Regex can validate content, e.g. ensure version is semantic:  
  ```python
  import json
  plugin = json.load(open("plugin.json"))
  if not re.match(r'^\d+\.\d+\.\d+$', plugin.get("version","")):
      report.warn("plugin.json version is missing or not semver")
  ```

- **Static Analysis:**  
  Run a JSON schema validator on `plugin.json` (using Anthropic’s schema if available) to catch errors. Use linters to flag missing fields (e.g. missing `name` field is required). Example (pseudocode):
  ```yaml
  # JSON Schema validation via cli or library
  schema = fetch("https://json.schemastore.org/claude-code-plugin-manifest.json")
  validate_json("plugin.json", schema)
  ```

- **Runtime CLI Probes:**  
  Use `claude plugin validate` to catch manifest/component issues. Integrate `claude plugin validate ./path --strict` in CI to surface warnings as errors. Example:
  ```bash
  claude plugin validate ./my-plugin --strict
  ```
  This ensures any misspelled fields or structural issues are flagged early.

- **Custom Heuristics:**  
  For credentials, scan common secrets via regex (e.g. AWS keys match `AKIA[0-9A-Z]{16}`) and exclude known false positives. For MCP servers, parse `.mcp.json` or manifest to list endpoints. For tests, detect known test frameworks by folder names or config files.

- **Sample Pseudocode for Scanning:**  
  ```
  function scanRepo(root):
      results = {}
      # Example: Check README length
      readme = open_if_exists(root/"README.md")
      results['README'] = min(len(readme), 800) / 800  # partial credit
      # Check CI config files
      ci_files = glob(root, "{.github/workflows/*.yml, .gitlab-ci.yml}")
      results['CI'] = 1 if ci_files else 0
      # etc.
      return results
  ```
  This modular approach lets each check return a score [0..1] for partial compliance.

The table below compares several key checks by their detection difficulty, false-positive risk, and remediation effort:

| Check                       | Detection Complexity | False-Positives | Remediation Effort |
|-----------------------------|----------------------|-----------------|--------------------|
| **Agents/CLAUDE.md**        | Easy (file existence) | Low             | Low (create file)  |
| **Skills (SKILL.md)**       | Easy (file existence) | Low             | Low (add SKILL.md) |
| **Dependency manifest**     | Easy (file presence)  | Low             | Low (add manifest) |
| **Test suite**              | Medium (folder patterns) | Medium (some projects use unconventional names) | Medium (write tests) |
| **CI configuration**        | Medium (look for known files) | Low        | Medium (add workflow) |
| **Linter config**           | Easy (file presence)  | Medium (false+ if config not used) | Medium (setup config) |
| **License file**            | Easy (file presence)  | Low             | Low (add LICENSE)  |
| **Secrets (e.g. .env)**     | Hard (pattern match)  | High (pattern matches can be false) | High (remove/rotate secrets) |

## Scoring Model and Rubric  
We assign each check a weight (reflecting importance) and compute a composite score. For example, *critical* items (tests, CI, instructions file) might be weighted higher (e.g. 20% each), while *nice-to-have* items (linter, telemetry) get lower weight. Each check returns a 0–1 pass score; the overall score is a weighted average (scaled to 100). For instance:

- **Scoring rubric example:**  
  - *>=90*: **Excellent** (very agent-friendly)  
  - *70–89*: **Good** (minor gaps)  
  - *50–69*: **Fair** (several issues)  
  - *<50*: **Poor** (needs significant improvements)

*Sample output:* A project scores 78/100 (Good). It had tests, CI, and a README, but missing `AGENTS.md` and had no linter config. The plugin report lists failed checks with severities and suggestions.

## Packaging Guidelines  
A Claude Code plugin is a self-contained directory structured as follows:

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json        # (optional) manifest metadata
├── skills/
│   ├── skill1/
│   │   └── SKILL.md       # Skill file with frontmatter
│   └── skill2/…
├── commands/              # Flat `.md` skills (legacy)
├── agents/                # agent definitions (`name:`, `model:`, etc.)
├── hooks/
│   └── hooks.json         # Hook configurations
├── .mcp.json              # MCP server definitions
├── .lsp.json              # LSP server configurations
├── output-styles/
├── themes/
├── scripts/
├── settings.json          # plugin default settings
├── LICENSE
└── CHANGELOG.md
```

Key points:
- **Manifest (`plugin.json`):** Defines plugin `name`, `version`, `description`, `author`, `repository`, `license`, and component paths. E.g.:  
  ```json
  {
    "name": "my-plugin",
    "displayName": "My Plugin",
    "version": "1.0.0",
    "description": "Custom tools for our workflow",
    "author": {"name":"Dev Team","email":"dev@example.com"},
    "repository": "https://github.com/me/my-plugin",
    "license": "MIT",
    "skills": "./skills/",
    "mcpServers": "./mcp.json"
  }
  ```
- **Versioning:** Update `version` in `plugin.json` to pin releases. If omitted, Claude uses the Git commit SHA.
- **Dependencies:** List any Node/Python deps in plugin’s `package.json` or scripts. The manifest `dependencies` field can declare package names/versions.
- **Distribution:** Plugins can be published to a marketplace or installed locally. Developers often host on GitHub (add marketplace catalog entry) or share via shared folders. Use `claude plugin init` to scaffold and `claude plugin install`/`uninstall` for deployment.
- **Examples:** Include example usage in README or as sample skills. Ensure any example `.md` is placed under `skills/` or `commands/`.
  
Follow [Anthropic’s plugin docs] for details on schema and layout. 

## Integration into Development Workflow  
To incorporate the audit plugin into CI/CD and local dev:

- **CLI and Git Hooks:**  
  - Use `claude plugin init` to scaffold new plugins with sample files.  
  - Integrate `claude plugin validate ./<plugin>` (and `--strict`) in your CI pipeline to catch manifest or component issues.  
  - Run static scans (our plugin) as part of CI tests. For example, add a job that executes `agent-friendliness-check` and fails on critical issues.

- **Development Tools:**  
  - Local testing: Run the plugin in development mode with `claude --plugin-dir` to see it load in Claude Code sessions. Use interactive prompts or the Agent SDK to query plugin behavior.  
  - We can implement a “test harness” that launches the agent on sample tasks and verifies outputs against expectations (for skill workflows).  
  - Example CI workflow (GitHub Actions) might include steps: checkout, install Claude CLI, run `claude plugin validate`, run our audit script, report results.

- **Automation:**  
  - Provide a GitHub Action or CLI tool (`claude-plugin-validate`, see [24]) that encapsulates all checks. 
  - Schedule periodic “agent-readiness” scans for long-lived projects.

## Security, Privacy, and Operational Considerations  
When running an agent or its audit:

- **MCP Servers and Network:**  
  - MCP servers should run locally or be clearly configured. The agent’s ability to call out is defined in `.mcp.json`. Limit network access as needed (using Claude Code’s permission rules or sandboxing).  
  - If your skill uses HTTP, ensure it cannot exfiltrate data – consider using Claude Code’s HTTP proxy or sandbox to enforce domain allowlists.

- **Secrets and Credentials:**  
  - Never hardcode API keys in the code or plugin. Use environment variables (e.g. `ANTHROPIC_API_KEY`) for authentication. Check code for accidental leaks (e.g. AWS keys in `.env`).  
  - Anthropic forbids offering unauthorized login or custom rate-limit schemes. Ensure the agent uses API keys via the official SDK/auth methods, not user logins.

- **Permissions:**  
  - Configure Claude Code’s permission system to **least-privilege** – explicitly allow needed commands or tools, and prompt/block dangerous actions.  
  - Use the sandbox runtime mode to restrict filesystem/network scope if processing untrusted repositories.

- **Data Privacy:**  
  - Avoid feeding sensitive customer data into the agent context. If inspecting the codebase, mount it read-only and exclude proprietary config (e.g. database credentials).
  - For telemetry, be transparent: only collect usage metrics if permitted, and anonymize any logs from agent actions.

## Analysis Pipeline (Mermaid Diagram)  
The following flowchart illustrates the plugin’s analysis process, from loading a repo to generating a friendliness report:

```mermaid
flowchart TB
  A[Load Repository] --> B[Static Checks: Files & Config]
  B --> C{Evaluate Components}
  C --> D[Instructions & Docs (AGENTS.md, README)]
  C --> E[Skills & Agents (SKILL.md, subagents)]
  C --> F[Infrastructure (CI, Tests, Env)]
  C --> G[Security (Secrets, Licenses)]
  D & E & F & G --> H[Compute Weighted Score]
  H --> I[Generate Summary & Recommendations]
```

## Next Steps & Roadmap  
To implement this plugin, we recommend the following phases:

- **Phase 1 (1–2 weeks):** Finalize the comprehensive checklist and weighting scheme. Prototype static file scans (using Python or Node.js). Validate basic checks (e.g. presence of `AGENTS.md`, `SKILL.md`).  
- **Phase 2 (2–4 weeks):** Develop the plugin CLI or script. Integrate `claude plugin validate` and add custom checks. Assemble score computation. Create unit tests for the checker using sample repos.  
- **Phase 3 (1 month):** Package the plugin (following [plugin guidelines]) and test in real workflows. Integrate into CI pipelines (e.g. GitHub Actions). Gather feedback and refine heuristics/thresholds.  
- **Phase 4:** Publish documentation and a marketplace listing. Explore automated remediation suggestions (e.g. generate a template AGENTS.md). Continuously update weights based on empirical agent performance.

**Action Items:** Build an initial version that prints missing items with severity. Iteratively expand with regex rules (for regex-based detection, e.g. version strings, secret patterns) and optional runtime probes (e.g. launching a dummy agent to ensure skill loading).  

**Conclusion:** By systematically verifying each of the above elements, a developer can ensure their repository is optimized for AI agents like Claude Code. This audit plugin will help teams surface missing pieces, improve consistency, and quantitatively score their codebase for agent-friendliness.

**Sources:** Anthropic’s Claude Code documentation and SDK guides, the “Agent Friendly Code” methodology, and community tutorials on Claude Code plugins informed these recommendations. These references, along with recognized security best-practices, guided the design of checks and mitigations.