# Category: Instruction Quality

## Purpose
Evaluate the quality of content in agent configuration files — not just presence, but whether the instructions actually help an agent do good work.

## Prerequisites
This category evaluates files found by the "Agent Config Files" category. If no agent config files exist, this category scores 0.

## Criteria

### 1. Specificity (25% of category score)
Does the file give concrete, specific guidance rather than vague platitudes?

- **Pass**: Contains specific examples, exact file paths, concrete patterns (e.g., "Use snake_case for Python files in src/", "API routes follow REST conventions in routes/api/v1/")
- **Partial**: Mix of specific and vague guidance (e.g., "Follow good coding practices" alongside some specific rules)
- **Fail**: Entirely vague (e.g., "Write clean code", "Follow best practices")

### 2. Actionability (25% of category score)
Does it tell the agent what to DO, not just what the project IS?

- **Pass**: Contains action-oriented instructions (e.g., "Run `npm test` before committing", "Always add types to function parameters", "Use the ErrorBoundary component for new pages")
- **Partial**: Mostly descriptive with some actionable guidance
- **Fail**: Purely descriptive (reads like a README, not instructions)

### 3. Completeness (25% of category score)
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

### 4. Consistency (25% of category score)
If multiple config files exist, do they agree with each other?

- **Pass**: No contradictions across files (or only one config file exists)
- **Partial**: Minor inconsistencies (e.g., different terminology for same concept)
- **Fail**: Direct contradictions (e.g., one file says "use tabs" another says "use spaces")

## How to Evaluate

1. Read all agent config files found in the repository
2. For each criterion, evaluate across ALL config files (not just one)
3. Use your judgment as an experienced developer: would these instructions actually help you work in this codebase?
4. Calculate category score using the weights above
