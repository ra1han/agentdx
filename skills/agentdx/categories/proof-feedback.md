# Category: Proof & Feedback Loops

## Purpose
Assess whether the repo gives agents deterministic back pressure: fast checks, runtime interactions, observable consequences, reset paths, and evidence artifacts that prove changes rather than merely documenting intent.

## Criteria

### 1. Fast Deterministic Sensors (20% of category score)
Can an agent get quick deterministic feedback after an edit?

Look for:
- compile, typecheck, lint, format, unit, schema, security, dependency, architecture, accessibility, visual, coverage, or mutation checks
- fast lanes distinct from slow integration/E2E lanes
- clear expected duration or scope where documented

Scoring:
- **Pass**: Fast local checks exist and produce actionable pass/fail output
- **Partial**: Checks exist but are slow, incomplete, flaky, poorly scoped, or hard to discover
- **Fail**: No deterministic local feedback surface is discoverable

### 2. Boot, Health, and Smoke Paths (15% of category score)
Can the system start and prove basic readiness where relevant?

Look for:
- `dev`, `start`, `up`, `boot`, `health`, `ready`, `smoke`, or equivalent commands
- health endpoints, readiness probes, startup logs, wait-for-ready scripts
- documented ports and expected success output

Scoring:
- **Pass**: Relevant boot/health/smoke path exists and is documented or scripted
- **Partial**: Boot path exists but readiness or smoke validation is unclear
- **Fail**: Runtime repos lack a discoverable boot/readiness path
- **N/A**: Pure libraries, static configs, or docs-only repos with no runtime surface

### 3. Supported Interaction Surfaces (15% of category score)
Can an agent exercise real behavior through supported surfaces?

Look for:
- API routes, OpenAPI/GraphQL/protobuf schemas, CLI commands, UI E2E flows, worker entrypoints, queue consumers, cron jobs, webhooks, MCP tools
- scripts/tests that hit supported endpoints or workflows
- local test users or safe auth bypasses scoped to test/local environments

Scoring:
- **Pass**: Meaningful behavior can be exercised through supported interfaces
- **Partial**: Interaction surfaces exist but require private shortcuts, remote-only services, or unclear setup
- **Fail**: Behavior can only be inferred statically or exercised manually without support

### 4. Observable Consequences and Evidence Artifacts (20% of category score)
Does the repo make outcomes inspectable after an interaction?

Look for:
- state, file, database row, message, event, cache entry, object, search index, generated artifact, log, trace, screenshot, video, HAR, JSON report, diagnostic endpoint
- `--json`, `--output`, `--verbose`, `--trace`, `--explain`, or artifact path options
- clear artifact retention or output directories

Scoring:
- **Pass**: Interactions produce inspectable evidence with deterministic verdicts
- **Partial**: Some artifacts/logs exist, but verdict extraction or artifact location is unclear
- **Fail**: Agents must rely on visual/manual inspection or claims without evidence artifacts

### 5. State, Fixture, Reset, and Clean Rerun (15% of category score)
Can proof start from known state and be rerun cleanly?

Look for:
- `seed`, `fixture`, `factory`, `snapshot`, `restore`, `reset`, `cleanup`, `teardown`, migrations, rollback, idempotent setup
- fixtures for users, tenants, permissions, domain objects, files, messages, events, UI storage, generated artifacts, or local models
- deterministic clocks, IDs, ports, teardown, and flake controls

Scoring:
- **Pass**: Known-state setup and cleanup make meaningful proof reproducible
- **Partial**: Some fixture/reset support exists but is incomplete or limited to tests
- **Fail**: State must be created manually or proof cannot be rerun from a known state

### 6. Test Mechanisms as Harness Affordances (15% of category score)
Do tests expose reusable mechanisms for agent proof loops?

Inventory mechanisms:
- mocks, fakes, stubs, spies
- sinks for email/SMS/payment/webhook/API/model/queue/object-store side effects
- contract tests, Testcontainers, in-memory adapters, fake servers
- factories, fixtures, snapshots, reset helpers

Scoring:
- **Pass**: Tests demonstrate reusable fakes/sinks/fixtures/containers or interaction proof that can be wrapped by agent commands
- **Partial**: Tests have useful mechanisms but they are isolated, brittle, or undocumented
- **Fail**: Tests provide little reusable proof machinery, or no tests are discoverable

Prefer fakes and sinks over brittle mocks when recommending improvements. A mock asserts an interaction; a fake or sink captures a consequence an agent can inspect.

## Proof Level Mapping

Use evidence from this category to report the highest proof level:
- L0: instructions or claims only
- L1: command output without deterministic verdict
- L2: static/build/test checks
- L3: runtime interaction
- L4: runtime interaction plus observable consequence
- L5: clean reproducible rerun
- L6: production/customer evidence

Do not claim L3+ from static assessment unless recorded evidence exists in tests, scripts, CI artifacts, docs, or prior run reports.

## How to Evaluate

1. Search manifests, task runners, CI, tests, docs, and agent instructions for validation commands
2. Inspect tests as reconnaissance for setup, substitution, fixtures, side-effect capture, and consequence verification
3. Classify proof surfaces as static/build, runtime, consequence, external-effect, observability, human/inferential, or production/customer
4. Select 1-3 representative scenario probes for the repo topology and estimate the highest plausible proof level today
5. Calculate category score using the weights above