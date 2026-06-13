# Category: Development Workflow

## Purpose
Assess whether git workflow conventions, local gates, manual-operation signals, and improvement loops are documented and discoverable so agents can follow team practices and convert friction into better future feedback.

## Criteria

### 1. Branch Naming Convention (20% of category score)
Is there a documented branching strategy agents can follow?

- **Pass**: Branch naming convention is documented in agent config, CONTRIBUTING.md, or README (e.g., `feature/`, `fix/`, `chore/` prefixes; reference to Git Flow or trunk-based)
- **Partial**: Branches exist with clear patterns (inferable from history) but convention is not explicitly documented
- **Fail**: No documented or inferable branching convention

Where to check:
- Agent config files (CLAUDE.md, .claude/rules/, copilot-instructions.md)
- CONTRIBUTING.md
- README.md
- `.github/` templates or branch protection rules

### 2. Commit Message Standards (20% of category score)
Are commit message conventions defined so agents produce well-formed commits?

- **Pass**: Commit convention is explicitly documented (e.g., Conventional Commits, Angular format, ticket prefix requirement) in agent instructions or contributing guide
- **Partial**: A commit message linter/hook exists (commitlint, commitizen) but convention isn't documented in agent-readable files
- **Fail**: No commit convention documented or enforceable

Patterns to look for:
- `commitlint.config.js`, `.commitlintrc`, `.czrc`, `.cz.json`
- Documented format in CONTRIBUTING.md or agent config (e.g., `feat:`, `fix:`, `JIRA-123:`)
- Git hooks referencing commit-msg validation

### 3. PR/MR Templates and Process (20% of category score)
Is the pull request workflow documented for agent contributors?

- **Pass**: PR template exists (`.github/pull_request_template.md` or `.github/PULL_REQUEST_TEMPLATE/`) AND review process is described in contributing docs or agent instructions
- **Partial**: PR template exists but no documented review process, OR process is documented but no template
- **Fail**: No PR template and no documented process

Where to check:
- `.github/pull_request_template.md`
- `.github/PULL_REQUEST_TEMPLATE/` directory
- `.gitlab/merge_request_templates/`
- CONTRIBUTING.md sections on PRs/reviews
- Agent config files mentioning PR workflow

### 4. Git Hooks and Automation (15% of category score)
Are pre-commit hooks or git automation configured so agents get immediate feedback?

- **Pass**: Git hooks are configured via a standard tool (husky, pre-commit, lefthook) with at minimum lint or format checks
- **Partial**: Hooks exist but are minimal (only one check) or use a non-standard/manual setup
- **Fail**: No git hooks configured

### 5. Compounding Improvement Loop (15% of category score)
Does the repo capture recurring agent/human friction and turn it into durable improvements?

Look for:
- known difficulties, troubleshooting, or friction ledger docs
- proof records or run history
- changelogs for harness/dev commands
- retrospectives or contribution notes that feed back into tools
- instructions that tell contributors when to add a check, fixture, script, or doc after repeated friction

Scoring:
- **Pass**: There is a clear workflow for capturing friction and encoding improvements into commands, checks, docs, or templates
- **Partial**: Friction/known-issue notes exist but are not tied to follow-up improvements
- **Fail**: No evidence that workflow friction becomes durable repo improvements

### 6. Manual and IDE-Only Signals (10% of category score)
Are important operations available outside a person's IDE or manual click path, or at least clearly identified?

Look for:
- IDE-only run/debug instructions without command-line equivalents
- click-ops deployment steps
- undocumented manual QA or release steps
- desktop/mobile/hardware interactions that are intrinsic to the repo topology

Scoring:
- **Pass**: Important manual operations have CLI/script equivalents, or topology-intrinsic manual steps are documented with safe boundaries
- **Partial**: Some important steps are manual or IDE-only, but they are documented well enough for an agent to avoid unsafe assumptions
- **Fail**: Critical validation, release, or setup steps are manual/IDE-only and undocumented

Do not over-penalize legitimate desktop, mobile, hardware, or brownfield topologies. Mark intrinsic manual signals as informational when they are not fixable repo gaps.

Where to check:
- `.husky/` directory
- `.pre-commit-config.yaml`
- `lefthook.yml`
- `package.json` → `husky` or `lint-staged` config
- `.git/hooks/` (manual hooks — harder for agents to discover)

## How to Evaluate

1. Search agent config files and contributing docs for branching/commit/PR guidance
2. Check for commit linter config files
3. Look for PR templates in standard locations
4. Check for git hook tooling (husky, pre-commit, lefthook)
5. Look for friction capture, proof records, known difficulties, and harness/dev command improvement notes
6. Identify manual or IDE-only operations and decide whether they are fixable gaps or topology-intrinsic signals
7. Assess whether an agent could independently follow the team's git workflow
8. Calculate category score using the weights above
