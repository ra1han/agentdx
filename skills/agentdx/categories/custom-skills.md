# Category: Custom Skills/Commands

## Purpose
Assess whether the repository defines custom skills, commands, or workflows that enhance agent capabilities for project-specific tasks.

## What to Look For

Custom skills can take many forms:
- Skill markdown files (in a `skills/` or `.copilot/skills/` directory)
- Custom slash commands (defined in agent config or dedicated files)
- Workflow definitions (multi-step procedures documented for agent use)
- Prompt templates (reusable prompts for common tasks)

## Directories/Files to Check

| Pattern | Description |
|---------|-------------|
| `skills/` or `**/skills/*.md` | Skill definition files |
| `.copilot/` | Copilot-specific customizations |
| `.github/copilot/` | GitHub Copilot configurations |
| `prompts/` or `.prompts/` | Prompt templates |
| Custom commands in agent config files | Inline command definitions |

## Criteria

### 1. Skills/Commands Exist (35% of category score)
At least one custom skill, command, or workflow is defined.

- **Pass**: One or more custom skills/commands found
- **Partial**: Skills are referenced in docs but files are missing or empty
- **Fail**: No custom skills, commands, or workflows found

Note: Not every project needs custom skills. If the project is small or simple, 0 here may be appropriate. The orchestrator should contextualize.

### 2. Clear Descriptions (25% of category score)
Each skill/command has a description explaining its purpose.

- **Pass**: All skills have clear descriptions of what they do and when to use them
- **Partial**: Some skills lack descriptions or have vague ones
- **Fail**: No descriptions on any skills

### 3. Well-Defined Triggers (20% of category score)
Skills have clear invocation patterns (how does the user/agent trigger them?).

- **Pass**: Trigger phrases, command names, or invocation instructions are explicit
- **Partial**: Some skills have triggers, others are unclear
- **Fail**: No clear way to invoke the skills

### 4. Project-Specific Value (20% of category score)
Skills address actual project workflows, not generic tasks.

- **Pass**: Skills cover project-specific workflows (e.g., "deploy to staging", "run migration", "create new API endpoint following our pattern")
- **Partial**: Mix of generic and project-specific skills
- **Fail**: Only generic skills (e.g., "write a test") or no skills

## How to Evaluate

1. Search for skill directories and files using the patterns above
2. Check agent config files for inline command definitions
3. Read found skills and evaluate against criteria
4. Calculate category score using the weights above
