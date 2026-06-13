# Category: Adaptability

## Purpose
Assess whether the repo can be safely and cheaply changed to create stronger harnesses, local substitutes, deterministic checks, and observable proof loops.

## Criteria

### 1. Seams, Substitution, and Dependency Inversion (20% of category score)
Can behavior be replaced for tests or local proof without editing production paths in place?

Look for:
- interfaces, ports/adapters, dependency injection, provider abstractions
- local adapters for external systems
- test doubles, fakes, stubs, spies, mocks, contract tests
- boundaries between domain logic and infrastructure

Scoring:
- **Pass**: Clear seams and substitutes exist for important dependencies
- **Partial**: Some seams exist, but important paths remain tightly coupled or hard to substitute
- **Fail**: Behavior is coupled directly to infrastructure or remote services

### 2. Hermetic and Offline Testability (15% of category score)
Can useful tests run without live remote services or shared state?

Look for:
- unit lanes separate from integration/E2E lanes
- in-memory implementations, fakes, local containers, contract tests
- markers or docs distinguishing tests that require network/secrets

Scoring:
- **Pass**: A useful offline/hermetic lane exists and remote tests are clearly separated
- **Partial**: Some offline tests exist, but important feedback still requires remote services or secrets
- **Fail**: Meaningful test feedback depends on live remote services or shared state

### 3. Side-Effect Isolation and External-Effect Sinks (15% of category score)
Can risky side effects be captured safely?

Look for:
- local sinks for email, SMS, push, payments, webhooks, analytics, notifications, object storage, search indexing, queues, events, model calls, third-party APIs
- replayable cassettes, fake servers, sandbox modes, dry-run modes, payload assertions
- controls preventing accidental production calls

Scoring:
- **Pass**: Risky side effects have safe local/sandbox/fake/sink paths
- **Partial**: Some side effects are isolated, but important ones remain remote or ambiguous
- **Fail**: Side-effecting paths can hit shared or production-like systems by default

### 4. State Evolution and Contract Verification (15% of category score)
Can state/schema changes be verified safely?

Look for:
- migration tooling, rollback/roll-forward checks, schema snapshots, contract/versioning checks
- event schema migration, file format migration, cache invalidation tests, object-store fixture paths
- commands that verify observable consequences after state changes

Scoring:
- **Pass**: State and contract evolution have checks and consequence verification
- **Partial**: Some migration/contract tooling exists, but rollback, fixtures, or verification are incomplete
- **Fail**: State/schema changes rely mostly on manual review or production discovery

### 5. Architecture Boundary Enforceability (15% of category score)
Are important boundaries encoded as checks rather than prose?

Look for:
- dependency-cruiser, ArchUnit, eslint boundary rules, Semgrep, CodeQL, Roslyn analyzers, module visibility constraints, package-level tests
- rules for layer direction, domain/infrastructure separation, import restrictions, tenant/auth/security boundaries

Scoring:
- **Pass**: Important architecture boundaries are enforced by deterministic checks
- **Partial**: Some boundary checks exist, but key rules remain prose-only
- **Fail**: Architecture constraints are absent or only documented in prose

### 6. Complexity, Navigability, and Inner-Loop Speed (20% of category score)
Can agents keep changes local and get feedback quickly?

Look for:
- focused modules, tests near code, clear package/domain boundaries
- complexity/file-size/duplication/dead-code rules
- fast test lanes, watch mode, build cache, isolated package tests
- deterministic clocks, IDs, ports, teardown, and retry/flake controls

Scoring:
- **Pass**: Code is navigable, change locality is good, and fast repeatable feedback exists
- **Partial**: Some large/hotspot files, slow loops, or scattered concepts create friction
- **Fail**: Changes commonly require broad edits or slow/inconsistent feedback loops

## How to Evaluate

1. Inspect package/module layout, tests, adapters, and dependency boundaries using safe static reads
2. Identify substitutable seams and test mechanisms that could be reused by agent proof commands
3. Look for side-effect sinks, contract checks, architecture rules, complexity rules, and fast lanes
4. Mark unknown rather than fail when safe static inspection cannot determine coupling or complexity
5. Calculate category score using the weights above