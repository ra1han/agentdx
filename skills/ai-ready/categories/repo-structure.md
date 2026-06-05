# Category: Repository Structure

## Purpose
Assess whether the repository is organized in a way that helps agents understand and navigate the codebase effectively.

## Criteria

### 1. README Quality (30% of category score)
Can an agent understand the project from the README alone?

- **Pass**: README covers: what the project does, how to set it up, how to run it, project structure overview
- **Partial**: README exists but is incomplete (e.g., only describes what it is, not how to use it)
- **Fail**: No README, or README is a template/placeholder

### 2. Contributing Guide (20% of category score)
Is there guidance for contributors (including agent contributors)?

- **Pass**: CONTRIBUTING.md or equivalent section in README with development workflow, PR process, code standards
- **Partial**: Brief contributing notes exist but lack detail
- **Fail**: No contributing guidance

### 3. Documentation Folder (25% of category score)
Is there organized documentation beyond the README?

- **Pass**: `docs/` folder (or equivalent) with meaningful content — architecture docs, API docs, decision records
- **Partial**: Some docs exist but are sparse, outdated, or disorganized
- **Fail**: No documentation beyond README

### 4. Clear Folder Organization (25% of category score)
Is the codebase logically organized so an agent can find things?

- **Pass**: Clear top-level organization (src/, tests/, docs/), not deeply nested (max 4-5 levels), naming is descriptive
- **Partial**: Mostly organized but some unclear directories or overly deep nesting
- **Fail**: Flat structure with many files at root, cryptic folder names, or extreme nesting

## How to Evaluate

1. Read the README.md and assess its completeness
2. Check for CONTRIBUTING.md or contributing section in README
3. Look for a docs/ directory and assess content quality
4. List the top-level directory structure and evaluate organization
5. Calculate category score using the weights above
