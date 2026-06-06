# Category: Custom Skills/Commands

## Purpose
Assess whether the repository defines custom skills, commands, or workflows that enhance agent capabilities for project-specific tasks. Includes quality checks for structure, size, and discoverability.

## What to Look For

Custom skills can take many forms:
- Skill markdown files (in a `skills/` or `.claude/skills/` directory)
- Custom slash commands (defined in agent config or dedicated files)
- Workflow definitions (multi-step procedures documented for agent use)
- Prompt templates (reusable prompts for common tasks)

## Directories/Files to Check

| Pattern | Description |
|---------|-------------|
| `skills/` or `.claude/skills/` | Skill definition files (SKILL.md) |
| `commands/` or `.claude/commands/` | Flat markdown command files (legacy) |
| `.copilot/` | Copilot-specific customizations |
| `.github/copilot/` | GitHub Copilot configurations |
| `prompts/` or `.prompts/` | Prompt templates |
| Custom commands in agent config files | Inline command definitions |

## Criteria

### 1. Skills/Commands Exist (30% of category score)
At least one custom skill, command, or workflow is defined.

- **Pass**: One or more custom skills/commands found with proper structure
- **Partial**: Skills are referenced in docs but files are missing or empty
- **Fail**: No custom skills, commands, or workflows found

Note: Not every project needs custom skills. If the project is small or simple, 0 here may be appropriate. The orchestrator should contextualize.

### 2. Skill Quality & Structure (25% of category score)
Each skill follows best practices for structure and content.

- **Pass**: Skills have YAML frontmatter (with `description` at minimum), clear purpose section, step-by-step procedure, and are under 300 lines
- **Partial**: Skills exist but lack frontmatter, have vague descriptions, or are excessively long (>300 lines)
- **Fail**: No descriptions on any skills, or skills are just raw text dumps

Specific checks:
- Presence of YAML frontmatter with `description` field (required for Claude Code to auto-invoke)
- Skill length: warn if >300 lines (suggest splitting or moving reference content to docs)
- Large embedded code blocks (>20 lines): suggest moving to reference files
- Clear invocation trigger (description tells Claude when to use it)

### 3. Discoverability & References (25% of category score)
Are skills referenced and discoverable from instruction files?

- **Pass**: Skills are referenced from CLAUDE.md, AGENTS.md, or other instruction files; OR skill descriptions are clear enough for auto-invocation
- **Partial**: Some skills are referenced but others are orphaned (exist but never mentioned anywhere)
- **Fail**: Skills exist but are completely unreferenced — no instruction file mentions them and descriptions are too vague for auto-discovery

Additional checks:
- List all skills and check if each is referenced in CLAUDE.md, AGENTS.md, or README
- If a skill has no references AND no clear `description` in frontmatter, flag as "unreferenced"
- Suggest moving long procedures from CLAUDE.md into dedicated skills

### 4. Project-Specific Value (20% of category score)
Skills address actual project workflows, not generic tasks.

- **Pass**: Skills cover project-specific workflows (e.g., "deploy to staging", "run migration", "create new API endpoint following our pattern")
- **Partial**: Mix of generic and project-specific skills
- **Fail**: Only generic skills (e.g., "write a test") or no skills

## How to Evaluate

1. Search for skill directories and files using the patterns above
2. For each skill found, check for YAML frontmatter and measure length
3. Check agent config files for inline command definitions
4. Cross-reference: are skills mentioned in instruction files?
5. Evaluate whether skills are project-specific or generic
6. Calculate category score using the weights above
