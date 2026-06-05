# Category: MCP Server Setup

## Purpose
Assess whether the repository has MCP (Model Context Protocol) server configurations that extend agent capabilities with project-specific tools.

## Files to Check

| File | Platform | Expected Location |
|------|----------|-------------------|
| `mcp.json` | Universal | Repository root |
| `.vscode/mcp.json` | VS Code / Copilot | `.vscode/` directory |
| `.cursor/mcp.json` | Cursor | `.cursor/` directory |
| `claude_desktop_config.json` | Claude Desktop | Repository root (reference) |
| `.github/copilot/mcp.json` | GitHub Copilot | `.github/copilot/` directory |

## Criteria

### 1. Configuration Exists (30% of category score)
At least one MCP configuration file is present.

- **Pass**: One or more MCP config files found
- **Partial**: MCP is referenced in documentation but no config file exists
- **Fail**: No MCP configuration or references found

Note: MCP is relatively new. If a project has no need for custom tools, scoring 0 here is acceptable. The orchestrator should note this context in recommendations.

### 2. Servers Defined (30% of category score)
The config file defines at least one MCP server.

- **Pass**: One or more servers with valid transport configuration (stdio, SSE, or streamable HTTP)
- **Partial**: Server defined but missing transport or connection details
- **Fail**: Empty config or no servers defined

### 3. Tool Descriptions (20% of category score)
If servers define tools, do they have clear descriptions?

- **Pass**: Tools have descriptions that explain what they do and when to use them
- **Partial**: Tools exist but descriptions are minimal or generic
- **Fail**: Tools have no descriptions, or no tools are defined
- **N/A**: If no tools are explicitly listed (some servers expose tools dynamically), score as Pass

### 4. No Placeholder Values (20% of category score)
Configuration contains real values, not placeholders.

- **Pass**: All values are real (URLs, paths, env var references)
- **Partial**: Some placeholder values but structure is correct
- **Fail**: Config is mostly placeholder/example content

## How to Evaluate

1. Search for MCP config files in expected locations
2. If found, read and evaluate the JSON structure
3. Check for server definitions, tool descriptions, and placeholder content
4. If no MCP files exist, check README/docs for MCP references
5. Calculate category score using the weights above
