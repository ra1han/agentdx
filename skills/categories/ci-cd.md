# Category: CI/CD Integration

## Purpose
Assess whether build, test, and lint commands are documented and discoverable so agents can validate their own work.

## Criteria

### 1. Commands in Agent Config (35% of category score)
Are build/test/lint commands explicitly documented in agent configuration files?

- **Pass**: Agent config files contain specific, runnable commands (e.g., "Run `npm run test` to run tests", "Lint with `ruff check .`")
- **Partial**: Commands are mentioned but vague (e.g., "run the tests", "use the linter")
- **Fail**: No commands documented in agent config files

### 2. Commands Are Specific and Runnable (25% of category score)
Can an agent copy-paste the commands and run them?

- **Pass**: Commands include exact invocation, any required flags, and expected behavior (e.g., "`pytest tests/ -v` — runs all tests, expect ~30s")
- **Partial**: Commands are specific but missing context (e.g., `npm test` without noting it requires `npm install` first)
- **Fail**: Commands are vague or missing

### 3. Workflow Files Present (20% of category score)
Does the repo have CI/CD workflow definitions?

- **Pass**: `.github/workflows/`, `Jenkinsfile`, `.gitlab-ci.yml`, or equivalent exists with meaningful jobs
- **Partial**: Workflow file exists but is minimal or only does one thing
- **Fail**: No CI/CD workflow files

### 4. Discoverable Scripts (20% of category score)
Are build/test/lint scripts easy to find via standard patterns?

Check for:
- `package.json` scripts section (Node.js)
- `Makefile` with standard targets (build, test, lint)
- `pyproject.toml` with scripts/tool configuration
- `Taskfile.yml`, `justfile`, or similar task runners

Scoring:
- **Pass**: Standard script runner with build/test/lint targets defined
- **Partial**: Scripts exist but only cover some tasks, or non-standard runner
- **Fail**: No discoverable script definitions

## How to Evaluate

1. Read agent config files and search for command references
2. Check for CI/CD workflow files in standard locations
3. Look for package.json/Makefile/pyproject.toml script sections
4. Assess whether an agent could independently run build/test/lint
5. Calculate category score using the weights above
