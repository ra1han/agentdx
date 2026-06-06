# Category: MCP Server Setup

## Purpose
Assess whether the repository has MCP (Model Context Protocol) server configurations that extend agent capabilities with project-specific tools. Includes security and hygiene checks.

## Files to Check

| File | Platform | Expected Location |
|------|----------|-------------------|
| `.mcp.json` | Claude Code | Repository root |
| `mcp.json` | Universal | Repository root |
| `.vscode/mcp.json` | VS Code / Copilot | `.vscode/` directory |
| `.cursor/mcp.json` | Cursor | `.cursor/` directory |
| `claude_desktop_config.json` | Claude Desktop | Repository root (reference) |
| `.github/copilot/mcp.json` | GitHub Copilot | `.github/copilot/` directory |

## Criteria

### 1. Configuration Exists (25% of category score)
At least one MCP configuration file is present.

- **Pass**: One or more MCP config files found
- **Partial**: MCP is referenced in documentation but no config file exists
- **Fail**: No MCP configuration or references found

Note: MCP is relatively new. If a project has no need for custom tools, scoring 0 here is acceptable. The orchestrator should note this context in recommendations.

### 2. Servers Defined (25% of category score)
The config file defines at least one MCP server.

- **Pass**: One or more servers with valid transport configuration (stdio, SSE, or streamable HTTP)
- **Partial**: Servers defined but transport is incomplete or unclear
- **Fail**: Empty config or no servers defined

### 3. Server Relevance & Hygiene (25% of category score)
Are the configured servers relevant to this project, and is the configuration clean?

- **Pass**: All servers are clearly relevant to the project's domain (e.g., a web app has GitHub + DB servers, not random unrelated tools); server count is reasonable (1-5 for most projects)
- **Partial**: Some servers seem unrelated to the project; OR too many servers configured (>8 for a simple project)
- **Fail**: Mostly irrelevant servers or obvious copy-paste from another project

Additional checks:
- If many servers are configured but few relate to the repo's domain, suggest pruning
- Each server should have a clear name and description
- Transport should be appropriate (stdio for local tools, HTTP for remote services)

### 4. Security & Configuration Quality (25% of category score)
Are secrets handled properly and configurations secure?

- **Pass**: All credentials use environment variables (e.g., `${POSTGRES_URL}`, `process.env.API_KEY`); no hardcoded secrets, tokens, or connection strings; HTTPS used for remote endpoints
- **Partial**: Some values use env vars but others are hardcoded; OR HTTP used where HTTPS should be
- **Fail**: Hardcoded API keys, passwords, or connection strings in MCP config; wildcard URLs for HTTP servers; or configs that allow arbitrary shell execution without validation

Specific patterns to flag:
- Literal strings matching: API keys (`sk-`, `AKIA`, `ghp_`, `xoxb-`), connection strings with passwords, bearer tokens
- URLs without HTTPS for remote servers
- Server commands that execute arbitrary shell input (e.g., generic `shell` or `exec` servers)
- Missing `args` validation for stdio servers that accept user input

## How to Evaluate

1. Search for all MCP config files in standard locations
2. Parse found configs and validate structure
3. Assess server relevance to the project's domain
4. Check for hardcoded secrets and security issues
5. If no MCP files exist, check README/docs for MCP references
6. Calculate category score using the weights above
