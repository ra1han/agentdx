# Category: Setup & Environment

## Purpose
Assess whether dependencies, runtimes, environment variables, local infrastructure, and external-service pressure are discoverable and safe enough for agents to set up or reason about the repo without tribal knowledge.

## Criteria

### 1. Environment Contract (25% of category score)
Are required environment variables and secrets documented by name without exposing values?

Look for:
- `.env.example`, `.env.template`, `.envrc.example`, or equivalent safe examples
- README, docs, or agent instructions listing required env var names and purpose
- clear separation between required secrets, optional settings, and local defaults
- warnings not to print, commit, or read real secret values

Scoring:
- **Pass**: Required env vars are named, described, and backed by safe examples/defaults where possible
- **Partial**: Some env vars are discoverable, but purpose, required/optional status, or safe defaults are unclear
- **Fail**: Setup depends on undocumented env vars or committed real secret files

Do not read or print values from real `.env`, shell history, keychains, password managers, cloud CLIs, or secret stores.

### 2. Runtime and Tool Version Contract (20% of category score)
Can an agent determine which runtime/tool versions and package managers to use?

Look for:
- `.tool-versions`, `.nvmrc`, `.node-version`, `.python-version`, `global.json`, `.ruby-version`, `rust-toolchain.toml`, `go.mod`, lockfiles
- `devcontainer.json`, Dockerfile, compose files, or setup docs
- package manager lockfiles and install commands

Scoring:
- **Pass**: Runtime versions and package manager are explicit and consistent
- **Partial**: Most tooling can be inferred from manifests, but versions or package manager are ambiguous
- **Fail**: Setup tooling is unclear or contradictory

### 3. Local Infrastructure and Service Substitutes (20% of category score)
Can required infrastructure run locally or through safe substitutes?

Look for:
- Docker Compose, Testcontainers, LocalStack, MinIO, MailHog/Mailpit, WireMock, fake servers, in-memory adapters
- local auth/test users or scoped test bypasses
- sandbox/dry-run modes for external services

Scoring:
- **Pass**: Important services have local/container/fake/sandbox options or are explicitly not required for local work
- **Partial**: Some local substitutes exist, but important services remain remote-only, secret-gated, or unclear
- **Fail**: Meaningful setup/proof requires live remote services with no documented substitute

### 4. Setup, Diagnostics, and Preflight Path (20% of category score)
Is there a low-risk way to check setup before booting or changing state?

Look for:
- `setup`, `bootstrap`, `Diagnostics`, `preflight`, `config --check`, `--dry-run`, or equivalent commands
- documented expected success/failure output
- non-mutating validation of required tools, env vars, and local services

Scoring:
- **Pass**: A clear setup/preflight path exists and checks important prerequisites safely
- **Partial**: Setup commands exist but are incomplete, mutating, or poorly documented
- **Fail**: Agents must discover setup problems only by attempting full boot/test commands

### 5. External Dependency Pressure Inventory (15% of category score)
Does the repo make remote dependency pressure visible?

For each visible dependency, record:
- name and type: database, cache, queue, object store, search, auth, payment, email, SMS, webhook, analytics, model provider, internal service, external API, other
- local option: native, container, fake, in_memory, testcontainers, sandbox, dry_run, none, unknown
- remote required: yes, no, optional, unknown
- secret required: yes, no, optional, unknown
- mutation risk: none, local_only, remote_safe_sandbox, remote_shared, production_risk, unknown
- whether proof is blocked by the dependency

Scoring:
- **Pass**: External dependencies and their local/safe options are explicit
- **Partial**: Dependencies are inferable, but local options, secret requirements, or mutation risk are unclear
- **Fail**: Remote dependencies are required but undocumented or unsafe to exercise

## How to Evaluate

1. Search setup docs, agent instructions, manifests, lockfiles, devcontainer, Docker, and compose files
2. Inventory env var names from safe examples and code references, never secret values
3. Identify external services from SDKs, connection strings, service URLs, docs, and test setup
4. Assess whether a fresh agent could prepare a local environment safely
5. Calculate category score using the weights above