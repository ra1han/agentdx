# Category: Agent Config Files

## Purpose
Assess the presence, location, and completeness of agent configuration files in the repository.

## Files to Check

| File | Platform | Expected Location |
|------|----------|-------------------|
| `AGENTS.md` | Universal | Repository root |
| `.github/copilot-instructions.md` | GitHub Copilot | `.github/` directory |
| `CLAUDE.md` | Claude Code | Repository root |
| `.cursorrules` | Cursor | Repository root |
| `.windsurfrules` | Windsurf | Repository root |

## Criteria

### 1. File Existence (40% of category score)
For each file above, check if it exists in the expected location.
- **Pass**: File exists at correct location
- **Partial**: File exists but in wrong location (e.g., `copilot-instructions.md` at root instead of `.github/`)
- **Missing**: File does not exist

Score: (files found / 5) × 100. Award partial credit (half) for mislocated files.

Note: Not every project needs all 5 files. Award full marks if at least 2 platform-specific files exist AND they cover the platforms the project actually uses (check for platform indicators like `.github/workflows/` for Copilot, existence of other `.cursor/` files for Cursor, etc.).

### 2. Minimum Substance (30% of category score)
For each found file, verify it has meaningful content:
- **Pass**: >50 words of substantive content (not boilerplate/placeholders)
- **Partial**: 20–50 words, or contains placeholder text ("TODO", "Add instructions here")
- **Fail**: <20 words or entirely placeholder content

### 3. Key Sections (30% of category score)
For each substantive file, check for coverage of these topics:
- Project overview / what this project is
- Coding conventions / style guidelines
- Tooling / how to build, test, lint
- Constraints / things the agent should NOT do

Scoring:
- **Pass**: 3–4 topics covered
- **Partial**: 1–2 topics covered
- **Fail**: 0 topics covered

## How to Evaluate

1. Use file search to check for each file in the expected location
2. Read found files and assess word count (excluding code blocks for word count)
3. Evaluate whether content covers the key sections listed above
4. Calculate category score using the weights above
