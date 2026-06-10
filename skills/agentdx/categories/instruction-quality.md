# Category: Instruction Quality

## Purpose
Evaluate the quality of content in agent configuration files — not just presence, but whether the instructions actually help an agent do good work. Includes heuristics for brevity, structure, and cross-file coherence.

## Prerequisites
This category evaluates files found by the "Agent Config Files" category. If no agent config files exist, this category scores 0.

For tool-scoped scans, evaluate only the files that Agent Config Files marked in scope for the requested platform. Do not read, score, compare, or recommend fixes for non-target platform files. For example, when the target platform is GitHub Copilot, ignore `CLAUDE.md` and `.claude/**` entirely; do not count their content, length, contradictions, or cross-references.

## Criteria

### 1. Specificity (20% of category score)
Does the file give concrete, specific guidance rather than vague platitudes?

- **Pass**: Contains specific examples, exact file paths, concrete patterns (e.g., "Use snake_case for Python files in src/", "API routes follow REST conventions in routes/api/v1/")
- **Partial**: Mix of specific and vague guidance (e.g., "Follow good coding practices" alongside some specific rules)
- **Fail**: Entirely vague (e.g., "Write clean code", "Follow best practices")

### 2. Actionability (20% of category score)
Does it tell the agent what to DO, not just what the project IS?

- **Pass**: Contains action-oriented instructions (e.g., "Run `npm test` before committing", "Always add types to function parameters", "Use the ErrorBoundary component for new pages")
- **Partial**: Mostly descriptive with some actionable guidance
- **Fail**: Purely descriptive (reads like a README, not instructions)

### 3. Completeness (20% of category score)
Does it cover the key areas an agent needs to work effectively?

Check for coverage of:
- Architecture / project structure explanation
- Coding conventions (naming, patterns, style)
- Tooling commands (build, test, lint, format)
- Constraints / prohibitions (what NOT to do)
- Dependencies / key libraries and their usage patterns

**Tech-stack-aware checking**: Detect the repository's tech stack (see `skills/agentdx/shared/tech-stacks.md`) and verify that instruction files mention the expected commands and patterns for each detected stack. For example:
- Node.js repo without `npm`/`yarn`/`pnpm` commands mentioned → gap
- Python repo without `pip`/`pytest`/`venv` mentioned → gap
- Go repo without `go build`/`go test` mentioned → gap

Scoring:
- **Pass**: 4–5 areas covered AND stack-specific commands are present for detected tech
- **Partial**: 2–3 areas covered; OR areas covered but stack-specific commands missing
- **Fail**: 0–1 areas covered

### 4. Brevity & Focus (20% of category score)
Is the file concise and focused, or bloated with low-signal content?

- **Pass**: Under 200 lines for the primary in-scope instruction file (for example, root `CLAUDE.md` in Claude scans or `.github/copilot-instructions.md` in Copilot scans), uses progressive disclosure (links to docs/skills for details), no large embedded code blocks
- **Partial**: 200–400 lines, some bloat but still navigable; OR contains large code samples that could be moved to reference files
- **Fail**: Over 400 lines; dump of everything including volatile/task-specific notes, /init-generated boilerplate, or entire code samples

Additional checks:
- Count total lines of each instruction file
- Detect fenced code blocks >20 lines (suggest moving to reference docs)
- Look for patterns indicating auto-generated/boilerplate text: "This file was generated", "TODO: customize", excessive template markers
- Check for volatile/ephemeral notes mixed with durable rules ("today we're trying…", "for this sprint…")

### 5. Structure & Cross-References (20% of category score)
Is the file well-organized with clear navigation and proper linking?

- **Pass**: Has clear headings (e.g., "## Project Overview", "## Commands", "## Conventions"); uses `@FILENAME` references or links to other docs/skills for progressive disclosure; if both CLAUDE.md and AGENTS.md exist, they reference each other
- **Partial**: Some headings but disorganized; OR files exist side-by-side without referencing each other
- **Fail**: Wall of text with no headings; multiple instruction files that duplicate content without cross-referencing

Additional checks:
- Look for heading patterns: `## Project`, `## Commands`, `## Stack`, `## Architecture`
- Detect `@FILENAME` references or markdown links to other repo docs
- If both `CLAUDE.md` and `AGENTS.md` exist in a cross-platform or Claude scan, check that one references the other (e.g., `@AGENTS.md` or `[AGENTS.md](./AGENTS.md)`). Do not apply this check in Copilot or Codex scans when `CLAUDE.md` is outside the target platform scope.
- If repo has `docs/` folder but instruction files never link to it, suggest adding pointers
- Detect the "table-of-contents approach": short root file linking to scoped rules/skills

### 6. Consistency (Bonus — deduct up to 10 points if failing)
If multiple config files exist, do they contradict each other?

- **No issue**: No contradictions across files (or only one config file exists)
- **Minor**: Different terminology for same concept (deduct 5 points)
- **Major**: Direct contradictions (e.g., one file says "use tabs" another says "use spaces") (deduct 10 points)

### 7. Skill & Command Quality (Bonus — up to +10 points)
If the repository defines custom skills, commands, or prompt templates, assess their quality.

Locations to check:
- `skills/` or `.claude/skills/` — SKILL.md files
- `commands/` or `.claude/commands/` — command files
- `.copilot/` or `.github/copilot/` — Copilot customizations
- `prompts/` or `.prompts/` — prompt templates

Scoring:
- **+10**: Skills exist with YAML frontmatter (`description` field), clear purpose, project-specific workflows (not generic), and are referenced from instruction files or have clear auto-invocation triggers
- **+5**: Skills exist but lack frontmatter, are generic ("write a test"), are orphaned (unreferenced), or are excessively long (>300 lines)
- **+0**: No custom skills/commands — acceptable, no penalty

Additional checks:
- Skills with no `description` frontmatter AND no references from instruction files are undiscoverable — flag these
- Skills >300 lines: suggest splitting or moving reference content to docs
- Generic skills that duplicate agent built-in capabilities add noise — flag these

## How to Evaluate

1. Read all in-scope agent config files found in the repository
2. For each criterion, evaluate across all in-scope config files (not just one)
3. Count lines and detect structural elements (headings, code blocks, references)
4. Check cross-file coherence only among in-scope files (do files reference each other, or duplicate/contradict?)
5. Search for custom skills/commands relevant to the target platform and assess quality if present
6. Use your judgment as an experienced developer: would these instructions actually help you work in this codebase?
7. Calculate category score: sum criteria 1–5 (each 20%), then apply consistency penalty and skill bonus
8. Cap final score at 0 minimum (consistency penalty cannot make score negative); cap at 100 maximum
