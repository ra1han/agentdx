<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# i am building a plugin for claude code. it will help developers to find gaps in their repo and agent setup to check for agent coding friendliness. for example claude.md or agent.md file, skills, mcp servers etc. do a deep research find what things to check, how to check, how to package the plugin (skills/agents etc)

You can treat this plugin as a “lint + doctor” for agentic repos: it inspects Claude/agent config, scores “agent-friendliness” across a few dimensions, and emits concrete fixes.

Below is a design for what to check, how to check it, and how to package it as a Claude Code skill/agent.

***

## Surfaces your plugin should inspect

At a high level you want to cover five surfaces that matter for Claude Code and other coding agents:[^1][^2]

- Project instructions: `CLAUDE.md`, `AGENTS.md`, `*.md` instruction files.
- Claude config: `.claude/settings.json`, `.claude/settings.local.json`, `.claude/rules/**`, `.claude/skills/**`, `.claude/agents/**`.[^3][^4][^2]
- MCP servers: `.mcp.json` or similar MCP config for external tools.[^5][^6][^7]
- Cross-agent configs: `.github/copilot-instructions.md`, `.cursorrules`, `opencode.json`, etc., to keep repo agent-agnostic.[^4]
- Git hygiene around agent files: what’s committed vs gitignored (`.claude/settings.json` vs `settings.local.json`, memory dirs, etc.).[^8][^4]

Your plugin can walk the repo, detect which of these exist, and then grade each dimension (e.g. 0–5) plus recommended changes.

***

## CLAUDE.md and other context files

### What good CLAUDE.md looks like

Modern guidance around CLAUDE.md is pretty consistent:[^9][^10][^11][^12]

- Keep root `CLAUDE.md` short: under 200–300 lines, ideally under 100.[^10][^12]
- Use it for:
    - Project overview (purpose, stack, structure).[^9][^10]
    - Canonical build/test/deploy commands.[^10]
    - High‑level rules and gotchas that are universally true (“dev DB is on port 5433”, “game logic must live in plugins”).[^13][^4][^10]
- Do **not**:
    - Dump exhaustive code style rules; let the code and linters teach style.[^11][^10]
    - Paste big code samples or auto-generated content (/init output, etc.).[^11][^10]
    - Stuff in volatile/task-specific instructions (those belong in scoped rules or skills).[^2][^10]

Context files like `AGENTS.md` (OpenAI/AGENTS ecosystem) and `.github/copilot-instructions.md` serve a similar role for other tools; they should stay concise and project-level.[^14][^4]

### Checks your plugin can run

For each of `CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`, `.cursorrules`, etc., do:

1. **Existence \& placement**
    - If no `CLAUDE.md` at project root → warn and suggest creating one with minimal template.[^9][^10]
    - If monorepo: check for directory-level `CLAUDE.md` (e.g. `./src/CLAUDE.md`) and recommend for large subtrees.[^4][^9]
2. **Size \& density**
    - Count lines; warn if:
        - `> 300` lines → “too long, likely hurting adherence and context cost”.[^10]
        - `> N` fenced code blocks or large code snippets → advise moving those to reference docs and linking instead.[^11][^10]
    - Detect obvious “/init-generated” or boilerplate text and recommend hand-editing.[^10]
3. **Structure**
    - Check for headings like “Project overview”, “Tech stack”, “Commands” or equivalent; warn if missing (no quick orientation).[^9][^10]
    - Look for explicit commands (regex `npm`, `pnpm`, `pytest`, `docker compose`, etc.); if none found, suggest adding canonical build/test commands.[^10]
4. **Progressive disclosure / pointers**
    - Detect `@FILENAME` references or links to other docs (e.g. `@AGENTS.md`, `docs/*.md`). This is recommended for cross-file context and to avoid duplication.[^15][^14][^9][^10]
    - If the repo has docs (e.g. `docs/` or `README.md`) but CLAUDE.md never links to them, suggest adding pointers instead of re‑stating everything.[^9][^10]
5. **Bloat / mixed concerns**
    - Look for obviously ephemeral notes (“today we’re trying…”) mixed with durable rules; suggest moving durable rules to `.claude/rules` and trimming CLAUDE.md regularly.[^2][^11]
    - Warn if file appears to be a dumping ground (e.g. many ad‑hoc sections, no pruning) — this is explicitly called out as a common failure mode.[^13][^11]
6. **Multi-agent friendliness**
    - If both `CLAUDE.md` and `AGENTS.md` exist, check that one points to the other with `@AGENTS.md` or equivalent rather than duplicating content.[^15][^4]
    - Suggest the “table-of-contents approach”: short root file which links to scoped rules/skills/docs, to keep context compact.[^12][^14]

Implementation-wise, this is simple text + Markdown parsing; you can get 80% just with line counts, regex on headings, fenced code blocks, and presence of `@` references.

***

## .claude settings, rules, skills, subagents

Claude Code has a three-level settings hierarchy plus project-local config and content-addressable skills/agents:[^16][^3][^8][^12][^4][^2]

- `~/.claude/settings.json`: global user settings and tool permissions.[^16][^8]
- `.claude/settings.json`: project-level shared settings (should be committed).[^8][^4]
- `.claude/settings.local.json`: per-user overrides (should be gitignored).[^4][^16][^8]
- `.claude/rules/*.md`: scoped, path-aware rules files (lazy-loaded).[^12][^2][^4]
- `.claude/skills/**/SKILL.md`: reusable workflows and procedures (skills).[^17][^18][^2]
- `.claude/agents/**`: subagents / personas with their own context windows.[^2][^12]


### Checks for settings \& hierarchy

- Verify `.claude/settings.json` exists in the repo if the project clearly uses Claude Code (e.g. `CLAUDE.md` present), and recommend committing it as part of project config.[^8][^12][^4]
- Parse `.gitignore`:
    - Ensure `.claude/settings.local.json` and `.claude/memory/` are ignored to avoid personal state in git.[^4][^8]
    - Ensure `.claude/settings.json` is *not* ignored.[^8][^4]
- Optionally validate `settings.json` shape against the community JSON schemas and examples (permissions, model, enabled plugins, etc.).[^19][^16][^12]

You can also sanity-check tool permissions: warn if they allow dangerous commands like `Bash(git push:*)` that are widely recommended to be denied.[^19]

### Checks for rules

- Inspect `.claude/rules/*.md`:
    - Check for YAML frontmatter with `paths:` so rules are lazy-loaded only when touching matching files; without frontmatter they behave like global CLAUDE.md and inflate context.[^12][^4]
    - Encourage moving narrow, path-specific rules out of root CLAUDE.md into rules files to keep CLAUDE.md short.[^2][^12]
- Warn if there are no rules files but the repo obviously has multiple domains (e.g. front-end, back-end, infra) — suggest splitting rules by path.[^1][^2]


### Checks for skills

A skill is essentially a markdown file with structured instructions representing a reusable workflow.[^18][^17][^2]

Your plugin can:

- Detect skills as `.claude/skills/**/SKILL.md` and auto‑document them in CLAUDE.md or AGENTS.md if they aren’t referenced anywhere.[^17][^2]
- For each skill, run basic linting:
    - Presence of a clear purpose section and step-by-step procedure.[^18][^17]
    - Reasonable length (e.g. < 200–300 lines).[^18][^10]
    - Avoid huge embedded reference text that would be better as a doc file.[^17][^10]
- Suggest moving long “how to deploy”, “how to run docs refresh” workflows out of CLAUDE.md into skills.[^12][^2][^10]


### Checks for subagents

Subagents live under `.claude/agents/` and are meant for isolated research/review contexts rather than general agents.[^2][^12]

- If `.claude/agents/` exists but there is only one extremely generic agent, suggest more focused agents (e.g. “docs refresher”, “dependency auditor”) aligned with the skills you detect.[^12][^2]
- If the repo has a lot of noisy research tasks (docs folders, huge monorepo) but no subagents, recommend creating them; best practice is to put noisy research into subagents instead of bloating main context.[^2][^12]

***

## MCP servers and external tools

MCP connects Claude (or other clients) to external systems (GitHub, DBs, Sentry, HTTP APIs, etc.). Config is usually via project- or user-level `mcp.json` with server definitions.[^6][^20][^7][^5][^2]

### What to check

- **Presence \& location**
    - Look for `.mcp.json` or equivalent in project root or `.claude/`.[^20][^5][^2]
    - If the project clearly talks about external tools (GitHub, Postgres, Slack, etc.) but no MCP config, suggest candidate MCP servers from the public registry.[^5][^6][^20]
- **Server definitions**
    - Parse the config and ensure each server has:
        - A clear name and description.[^7][^5]
        - Reasonable transport (stdio or HTTP) and non‑wildcard URLs for HTTP servers.[^7][^5]
        - Parameters referencing environment variables rather than hard‑coded secrets (e.g. `POSTGRES_URL` instead of literal DSN).[^21][^6]
    - If many servers are configured but few are relevant to this repo (e.g. random S3 or Slack servers in a small toy app), suggest pruning; best practice is “add only servers you actually use.”[^20][^2]
- **Security and robustness**
    - Warn on:
        - Commands that execute arbitrary shell from inputs without validation (if visible in the server implementation tree).[^21][^5]
        - Servers that have no clearly-scoped capabilities (e.g. generic `shell` server allowing arbitrary commands) — point to MCP security best practices about constraining tool capabilities.[^6][^21]

Your plugin doesn’t need to fully understand server code; just reading the config and doing simple heuristics is enough for hints.

***

## Cross‑agent friendliness (Copilot, Cursor, OpenCode, etc.)

Teams often use multiple coding agents, which introduces a zoo of config files. A “friendly” repo:[^4]

- Has both `CLAUDE.md` and `AGENTS.md`/`.github/copilot-instructions.md` if multiple tools are used, with consistent guidance.[^14][^4]
- Uses a shared philosophy/structure but tool‑specific files just adapt the syntax.[^15][^4]
- Commits team-shared configs (e.g. `.github/copilot-instructions.md`, `.cursorrules`) and ignores local state.[^22][^23][^4]

Your plugin can:

- Detect presence of these files and report:
    - “You’re using Claude only,” “Claude + Copilot”, “Claude + Cursor”, etc., based on which files exist.[^23][^24][^22][^4]
    - Inconsistent or missing cross‑references (e.g. repo has `AGENTS.md` but `CLAUDE.md` never mentions it; recommend `@AGENTS.md`.)[^15][^4]
- Optionally suggest adopting the convention table seen in reference material so humans (and tools) know what config is for which agent.[^4]

***

## Heuristics and implementation details

### Static analysis strategy

Given you’re running inside Claude Code, you can rely on the built‑in tools:

- Use `Read`/`Search` or `Bash(find …)` to discover candidate files.[^19][^16]
- Parse Markdown:
    - Count headings, fenced code blocks, total lines.
    - Extract simple section titles.
- Parse JSON (`.claude/settings*.json`, `.mcp.json`).

Output a structured JSON report like:

```json
{
  "claude_md": { "present": true, "lines": 320, "issues": ["too_long", "no_commands_section"] },
  "rules": { "count": 0, "issues": ["no_path_scoped_rules"] },
  "skills": { "count": 3, "unreferenced": ["deploy-checklist"] },
  "mcp": { "servers": 5, "issues": ["unused_servers", "hardcoded_urls"] },
  "cross_agents": { "configs": ["CLAUDE.md", "AGENTS.md"], "issues": ["no_reference_between_files"] }
}
```

Then have the skill re‑render this into a human‑readable report with explanations and inline code suggestions.

### Scoring

For UX, give each dimension a grade (0–5) and one‑line “what to fix next”:

- CLAUDE.md: 2/5 — “Trim from 430 to <200 lines; move ‘Deployment checklist’ to a `.claude/skills/deploy/SKILL.md`.”[^10][^12][^2]
- Rules: 1/5 — “Introduce `.claude/rules/backend.md` scoped to `paths: ["backend/**"]` for API-specific guidance.”[^12][^4][^2]
- MCP: 3/5 — “Prune unused GitHub/S3 servers; ensure all URLs are HTTPS and secrets are via env vars.”[^5][^6][^21]
- Cross‑agent: 4/5 — “Add `@AGENTS.md` reference to CLAUDE.md so Claude can load those instructions efficiently.”[^15][^4]

***

## Packaging as a Claude Code plugin

In 2026, the most idiomatic packaging is “a skill with optional helper scripts,” not a heavy MCP server.[^17][^18][^2]

### Core design: one primary skill

Create a skill, e.g. `.claude/skills/repo-agent-linter/SKILL.md`, that:

- Describes its purpose: “Audit this repo’s Claude/agent setup and suggest improvements.”
- States when to use it (e.g. when user asks about setup, or you explicitly call it).
- Defines the workflow:

1. Run a scan script (or direct `Bash`/`Read`) to collect repo facts.
2. Build a JSON summary of findings.
3. Turn findings into a human‑readable report with concrete diffs/snippets.
- Specifies “degrees of freedom” (e.g. avoid auto‑editing; only propose patches unless user confirms).[^18]

You can optionally include a tiny CLI (Python/Node) in the repo, but you can also just use Claude Code tools; a lot of examples emphasize that skills can be pure markdown plus built‑in tools.[^17][^18][^2]

### Optional helper pieces

- **Subagent**: `.claude/agents/setup-doctor/` that uses this skill by default and is positioned as a “Claude Doctor” for configuration.[^2][^12]
- **Hook**: a PreSession or slash command hook that suggests running the audit if the repo lacks CLAUDE.md or `.claude/settings.json`.[^12][^2]
- **MCP server (later)**: If you want to share this beyond Claude, you could expose the same logic as an MCP server that takes a repo path and returns the JSON report; but for Claude Code usage alone, a skill + Bash is enough.[^6][^7][^5]

***

## Suggested file layout and UX

A lightweight but idiomatic layout, borrowing from reference agentic setups:[^3][^1][^2][^12]

```text
repo/
  CLAUDE.md
  AGENTS.md               # optional, for other tools
  .claude/
    settings.json
    rules/
      backend.md
      frontend.md
    skills/
      repo-agent-linter/
        SKILL.md
    agents/
      setup-doctor/
        AGENT.md          # or equivalent agent descriptor
  .mcp.json               # if applicable
  .github/
    copilot-instructions.md
  .cursorrules
```

Typical UX:

- User types `/doctor` or “run repo agent setup audit”.
- Claude invokes `repo-agent-linter` skill.
- Skill:
    - Scans repo.
    - Produces JSON and human summary.
    - Offers to:
        - Generate or trim `CLAUDE.md`.
        - Create `.claude/rules/*` and `.claude/skills/*` skeletons.
        - Suggest MCP servers and add stub `.mcp.json`.
        - Update `.gitignore` to handle `.claude/*` correctly.

This gives you an opinionated, automatable checklist grounded in current best practices for CLAUDE.md, skills, MCP, and multi‑agent setups.[^9][^4][^10][^2][^12]
<span style="display:none">[^25][^26][^27][^28][^29]</span>

<div align="center">⁂</div>

[^1]: https://wal.sh/research/agentic-workflow-2026/index.html?w=0

[^2]: https://okhlopkov.com/claude-code-setup-mcp-hooks-skills-2026/

[^3]: https://skillspool.org/en/skills/radesjardins-rad-claude-skills-plugins-rad-agentic-company-builder-skills-scaffold-project-skill-md

[^4]: https://courses.taltech.akaver.com/agentic-software-development/lectures/misc

[^5]: https://github.com/modelcontextprotocol/servers

[^6]: https://www.anthropic.com/news/model-context-protocol

[^7]: https://github.com/modelcontextprotocol/modelcontextprotocol

[^8]: https://agentfactory.panaversity.org/docs/General-Agents-Foundations/general-agents/settings-hierarchy

[^9]: https://www.claudedirectory.org/blog/claude-md-guide

[^10]: https://www.buildcamp.io/guides/the-ultimate-guide-to-claudemd

[^11]: https://www.reddit.com/r/ClaudeCode/comments/1riwy13/claudemd_best_practices/

[^12]: https://github.com/shanraisshan/claude-code-best-practice/blob/main/CLAUDE.md

[^13]: https://www.reddit.com/r/ClaudeAI/comments/1r7mvja/new_research_agentsmd_files_reduce_coding_agent/

[^14]: https://news.ycombinator.com/item?id=46098838

[^15]: https://www.reddit.com/r/ClaudeCode/comments/1r9zx34/pointing_claudemd_to_agentsmd/

[^16]: https://www.reddit.com/r/ClaudeAI/comments/1l24a93/claude_code_settingsjson/

[^17]: https://github.com/alirezarezvani/claude-skills

[^18]: https://www.youtube.com/watch?v=Ik-Xbz2hvM0\&vl=en-US

[^19]: https://github.com/ZacheryGlass/.claude/blob/master/settings.json

[^20]: https://www.firecrawl.dev/blog/best-mcp-servers-for-cursor

[^21]: https://github.com/lirantal/awesome-mcp-best-practices/issues

[^22]: https://github.com/CodySwannGT/ai-coding-assistants-setup/blob/main/docs/GITHUB_SETUP.md

[^23]: https://github.com/TuanTTranCS/setup-coding-assistants

[^24]: https://developer.microsoft.com/blog/announcing-awesome-copilot-mcp-server

[^25]: https://github.com/anthropics/claude-code-action/blob/main/docs/setup.md

[^26]: https://www.reddit.com/r/mcp/comments/1l71sk3/best_practices_for_developers_looking_to_leverage/

[^27]: https://www.youtube.com/watch?v=RhTiAOGwbYE

[^28]: https://github.com/anthropics/claude-code/issues/238

[^29]: https://github.com/features/copilot

