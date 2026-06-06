# Category: Instruction Quality

## Purpose
Evaluate the quality of content in agent configuration files — not just presence, but whether the instructions actually help an agent do good work. Includes heuristics for brevity, structure, and cross-file coherence.

## Prerequisites
This category evaluates files found by the "Agent Config Files" category. If no agent config files exist, this category scores 0.

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

Scoring:
- **Pass**: 4–5 areas covered
- **Partial**: 2–3 areas covered
- **Fail**: 0–1 areas covered

### 4. Brevity & Focus (20% of category score)
Is the file concise and focused, or bloated with low-signal content?

- **Pass**: Under 200 lines (root CLAUDE.md), uses progressive disclosure (links to docs/skills for details), no large embedded code blocks
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
- If both `CLAUDE.md` and `AGENTS.md` exist, check that one references the other (e.g., `@AGENTS.md` or `[AGENTS.md](./AGENTS.md)`)
- If repo has `docs/` folder but instruction files never link to it, suggest adding pointers
- Detect the "table-of-contents approach": short root file linking to scoped rules/skills

### 6. Consistency (Bonus — deduct up to 10 points if failing)
If multiple config files exist, do they contradict each other?

- **No issue**: No contradictions across files (or only one config file exists)
- **Minor**: Different terminology for same concept (deduct 5 points)
- **Major**: Direct contradictions (e.g., one file says "use tabs" another says "use spaces") (deduct 10 points)

## How to Evaluate

1. Read all agent config files found in the repository
2. For each criterion, evaluate across ALL config files (not just one)
3. Count lines and detect structural elements (headings, code blocks, references)
4. Check cross-file coherence (do files reference each other, or duplicate/contradict?)
5. Use your judgment as an experienced developer: would these instructions actually help you work in this codebase?
6. Calculate category score: sum criteria 1–5 (each 20%), then apply consistency penalty if applicable
7. Cap final score at 0 minimum (consistency penalty cannot make score negative)
