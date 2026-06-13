# Category: CI/CD Integration

## Purpose
Assess whether build, test, lint, smoke, and proof commands are documented, discoverable, locally runnable, and equivalent to CI so agents can validate their own work with trustworthy feedback.

## Criteria

### 1. Commands in Agent Config (15% of category score)
Are build/test/lint commands explicitly documented in agent configuration files?

- **Pass**: Agent config files contain specific, runnable commands (e.g., "Run `npm run test` to run tests", "Lint with `ruff check .`")
- **Partial**: Commands are mentioned but vague (e.g., "run the tests", "use the linter")
- **Fail**: No commands documented in agent config files

### 2. Commands Are Specific and Runnable (15% of category score)
Can an agent copy-paste the commands and run them?

- **Pass**: Commands include exact invocation, any required flags, and expected behavior (e.g., "`pytest tests/ -v` — runs all tests, expect ~30s")
- **Partial**: Commands are specific but missing context (e.g., `npm test` without noting it requires `npm install` first)
- **Fail**: Commands are vague or missing

### 3. Workflow Files Present (15% of category score)
Does the repo have CI/CD workflow definitions?

- **Pass**: `.github/workflows/`, `Jenkinsfile`, `.gitlab-ci.yml`, or equivalent exists with meaningful jobs
- **Partial**: Workflow file exists but is minimal or only does one thing
- **Fail**: No CI/CD workflow files

### 4. Discoverable Scripts (15% of category score)
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

### 5. Command Tier Coverage (20% of category score)
Can an agent find the right command for each major validation tier?

Check for these tiers where relevant:
- `bootstrap` / setup dependencies
- `doctor` / preflight configuration check
- `boot` / local start
- `health` / readiness verification
- `fast` or `quick` validation lane
- `proof` / full verification lane
- `ci_equivalent` / local command matching CI
- `smoke` / basic runtime behavior check
- `seed_or_reset` / known state setup
- `observe` / diagnostics or artifact inspection
- `cleanup` / teardown

Scoring:
- **Pass**: Most relevant tiers have explicit commands or intentionally documented N/A status
- **Partial**: Some tiers exist, but important boot, fast, proof, or CI-equivalent paths are missing or inferred
- **Fail**: Only ad-hoc raw tool commands exist, or no tiered validation path is discoverable

Do not require every tier for every repo. Libraries may not need boot/health; static sites may not need seed/reset; CLI tools may use help/version/smoke commands instead of web health checks.

### 6. Local/CI Equivalence (20% of category score)
Can an agent trust local feedback before CI runs?

For each meaningful check, compare local and CI commands:
- `identical`: same command and scope
- `equivalent`: different wrapper, same checks
- `partial`: local runs a subset
- `divergent`: local and CI run meaningfully different checks
- `local_only`: no CI equivalent
- `ci_only`: no local equivalent
- `unknown`: insufficient evidence

Scoring:
- **Pass**: Core build/test/lint/typecheck/security checks are identical or equivalent locally and in CI
- **Partial**: Some local/CI equivalence exists, but important checks are partial, divergent, or one-sided
- **Fail**: CI checks are not reproducible locally, or no CI/local relationship is discoverable

## How to Evaluate

1. Read agent config files and search for command references
2. Check for CI/CD workflow files in standard locations
3. Look for package.json/Makefile/pyproject.toml script sections
4. Classify discovered commands into tiers and mark each as verified, configured_unverified, candidate_unverified, not_applicable, or unknown
5. Compare local commands to CI workflow commands for equivalence
6. Assess whether an agent could independently run build/test/lint/smoke/proof checks
7. Calculate category score using the weights above
