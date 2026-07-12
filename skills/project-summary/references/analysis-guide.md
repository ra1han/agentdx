# Analysis Guide: Detecting and Describing Each Section

This guide explains how to gather evidence for every section of a project summary. Ground each statement in a cited file path. When evidence is missing, mark the item `unknown` or `inferred` rather than guessing.

## Purpose & Overview

Sources, in priority order:
1. `README` (root and `docs/`) — title, description, "About", "Features" sections.
2. Package metadata `description`/`summary` fields (`package.json`, `pyproject.toml`, `Cargo.toml`, `pom.xml`).
3. Repository/homepage links and badges.
4. Dominant domain vocabulary — recurring nouns in module, model, route, and table names.

Describe what the project does, who it is for, and the problem it solves. If the README and code disagree, note the discrepancy.

## Tech Stack

Detect from signature files:

| Indicator Files | Stack / Runtime |
|-----------------|-----------------|
| `package.json`, `node_modules/` | Node.js |
| `tsconfig.json`, `*.ts` | TypeScript |
| `next.config.*` | Next.js |
| `vite.config.*`, `vue.config.*` | Vite / Vue |
| `angular.json` | Angular |
| `requirements.txt`, `pyproject.toml`, `setup.py` | Python |
| `manage.py` | Django |
| `Cargo.toml` | Rust |
| `go.mod` | Go |
| `pom.xml`, `build.gradle` | Java / Kotlin |
| `Gemfile` | Ruby / Rails |
| `Dockerfile`, `docker-compose.yml` | Docker |
| `*.tf`, `terraform/` | Terraform |
| `.github/workflows/` | GitHub Actions |

Report languages, frameworks, runtimes, databases, and major libraries. Include declared versions where present (engines fields, language version pins, lockfile versions). List databases and message brokers found in dependencies or connection configuration.

## Architecture

Infer the high-level shape from directory layout, deployment manifests, and entry points:

- **Monolith / layered:** single deployable, folders like `controllers`/`services`/`repositories` or `routes`/`models`.
- **Microservices:** multiple service folders each with its own manifest and Dockerfile, or a `services/` root.
- **Client/server:** separate `frontend`/`backend` (or `web`/`api`) trees.
- **Library / package:** publishable manifest, public API surface, no server bootstrap.
- **CLI:** command/subcommand definitions and a bin entry.
- **Event-driven / serverless:** function handlers, queue/topic triggers, `azure.yaml`/SAM/serverless config.

Name the primary boundaries and how they communicate (HTTP, gRPC, queue, direct import). Add a Mermaid `graph`/`flowchart` diagram when the structure has three or more interacting parts.

## Component Responsibilities

Walk significant top-level and second-level directories. For each meaningful module, package, or service, capture:
- its single-sentence responsibility;
- its key collaborators (what it calls and what calls it), inferred from imports;
- notable patterns (repository, factory, middleware, adapter).

Skip generated, vendored, and dependency directories. Group trivial folders rather than listing each file.

## Data Flow

Anchor on an entry point (`main`, `index`, `app`, server bootstrap, route registration, CLI command, or queue handler). Trace one representative operation end to end:

1. Entry / trigger (HTTP route, CLI command, scheduled or event trigger).
2. Validation / auth / middleware.
3. Business logic / service layer.
4. Data access / persistence.
5. External calls (APIs, queues, cache).
6. Response / side effect / output.

Use a Mermaid `sequenceDiagram` or `flowchart` when it clarifies the path. Prefer one clear representative flow over exhaustively documenting every route.

## External Integrations & Dependencies

Identify databases, caches, message brokers, third-party APIs, auth providers, and cloud services. Evidence sources: client library imports, connection/config code, environment variable names, and infrastructure manifests. Report environment variables by **name only** with an inferred purpose — never read or print secret values.

## CI/CD & Deployment

Read workflow and deployment files:
- `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml`.
- `Dockerfile`, `docker-compose.yml`, Helm charts, Kubernetes manifests.
- IaC (`*.tf`, Bicep, ARM), `azure.yaml`, `Procfile`, `vercel.json`, `netlify.toml`.

Describe: triggers (push, PR, tag, schedule), stages (build, test, lint, security scan, deploy), target environments, and deployment target (registry, cloud platform, hosting). Cite the file and job for each claim. Mark commands as documented rather than verified unless the session ran them.

## Entry Points & Local Development

List how to start and work on the project locally: install, build, test, and run commands from `package.json` scripts, `Makefile`, `pyproject.toml`, task runners, and README instructions. Identify the main runtime entry points. Present commands as documented; do not claim they were verified unless you actually ran them safely.
