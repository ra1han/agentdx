# Category: Repository Structure

## Purpose
Assess whether the repository is organized and documented in a way that helps agents understand the system, navigate to the right files, and avoid expensive inference during a first session.

## Criteria

### 1. README Quality (25% of category score)
Can an agent understand the project from the README alone?

- **Pass**: README covers: what the project does, how to set it up, how to run it, project structure overview
- **Partial**: README exists but is incomplete (e.g., only describes what it is, not how to use it)
- **Fail**: No README, or README is a template/placeholder

### 2. Contributing Guide (15% of category score)
Is there guidance for contributors (including agent contributors)?

- **Pass**: CONTRIBUTING.md or equivalent section in README with development workflow, PR process, code standards
- **Partial**: Brief contributing notes exist but lack detail
- **Fail**: No contributing guidance

### 3. Documentation Folder (20% of category score)
Is there organized documentation beyond the README?

- **Pass**: `docs/` folder (or equivalent) with meaningful content — architecture docs, API docs, decision records
- **Partial**: Some docs exist but are sparse, outdated, or disorganized
- **Fail**: No documentation beyond README

### 4. Clear Folder Organization (20% of category score)
Is the codebase logically organized so an agent can find things?

- **Pass**: Clear top-level organization (src/, tests/, docs/), not deeply nested (max 4-5 levels), naming is descriptive
- **Partial**: Mostly organized but some unclear directories or overly deep nesting
- **Fail**: Flat structure with many files at root, cryptic folder names, or extreme nesting

### 5. Repo Map and Onboarding Surface (20% of category score)
Can a fresh agent quickly answer what the system is, where the main packages/services live, how to run it, and where current work or known traps are documented?

Look for:
- architecture overview, repo map, package map, service map, or domain map
- first-session / onboarding notes
- links from agent instructions to relevant docs
- ADRs/RFCs/design docs that explain major boundaries
- known-trap or troubleshooting notes

Scoring:
- **Pass**: A current repo map or onboarding path connects README/agent instructions to docs, commands, and major packages/services
- **Partial**: Useful orientation exists but is scattered, stale, or not cross-linked
- **Fail**: A fresh agent must infer topology and entrypoints mostly from file names

## How to Evaluate

1. Read the README.md and assess its completeness
2. Check for CONTRIBUTING.md or contributing section in README
3. Look for a docs/ directory and assess content quality
4. List the top-level directory structure and evaluate organization
5. Check whether the README or agent instructions provide a repo map or link to one
6. Calculate category score using the weights above
