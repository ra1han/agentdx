---
name: project-summary
description: Generate a detailed, evidence-based summary of a software project — its purpose, component responsibilities, data flow, architecture, tech stack, and CI/CD configuration. Use when asked to summarize, document, explain, onboard onto, map, or produce an overview/architecture document for a codebase or repository.
---

# Project Summary: Codebase Overview Generator

Produce a clear, accurate, evidence-based summary of a software project so a new engineer or agent can understand what it does, how it is built, and how the pieces fit together. Ground every claim in files that actually exist; distinguish what the code demonstrates from what documentation merely asserts.

## 1. Resolve the Scope

Use the current workspace unless the user names a different repository or path. Before scanning, determine:

- **Depth:** Produce a full summary by default (all sections). For a focused request, generate only the named sections (for example, "just the architecture and data flow").
- **Audience:** Default to a new-engineer onboarding audience. Adjust emphasis when the user names a different audience (executive overview, security review, migration planning).
- **Output:** Display the summary in chat by default. Write a file only when the user explicitly asks; then create `.agentdx/project-summary.md` (creating the `.agentdx/` directory if needed) and state that it is being created or replaced before writing.

Available sections:

1. Purpose & Overview
2. Tech Stack
3. Architecture
4. Component Responsibilities
5. Data Flow
6. External Integrations & Dependencies
7. CI/CD & Deployment
8. Entry Points & Local Development

## 2. Load the Report Template and References

Read [templates/summary-template.md](templates/summary-template.md) before composing the summary. Read [references/analysis-guide.md](references/analysis-guide.md) for how to detect and describe each section.

Reuse the stack-detection table in the sibling skill's `shared/tech-stacks.md` when it is available in the same plugin; otherwise rely on the signature-file table in the analysis guide.

## 3. Gather Evidence Safely

Inspect tracked files, manifests, entry points, configuration, source directories, tests, CI definitions, and documentation. Use narrow searches first and read representative files rather than indiscriminately loading generated, vendored, binary, or dependency directories.

- Start from the repository map: `README`, `package.json`/`pyproject.toml`/`go.mod`/`pom.xml`/`Cargo.toml`/etc., top-level folders, and CI workflow files.
- Identify entry points (`main`, `index`, `app`, server bootstrap, CLI command definitions) to anchor the data-flow narrative.
- Follow imports and directory boundaries to infer component responsibilities and architectural layers.
- Read CI/CD workflow files and deployment manifests (`.github/workflows/`, `Dockerfile`, `docker-compose.yml`, IaC files, `azure.yaml`, `Procfile`, Helm charts) to describe build, test, and release steps.

Do not read or print values from real `.env` files, keychains, credential stores, or secret managers. Report only a variable's name, source file, and inferred purpose. Do not run bootstrap, build, test, start, deploy, or migration commands unless the user asks or a command is clearly non-mutating and safe.

Ground every non-trivial statement in a cited file path. When evidence is missing or ambiguous, say so explicitly rather than inventing detail — mark it `unknown` or `inferred` instead of presenting a guess as fact.

## 4. Synthesize the Summary

For each in-scope section, follow the guidance in the analysis guide:

- **Purpose & Overview:** State what the project does and the problem it solves, drawn from README, package metadata, and the dominant domain vocabulary in the code.
- **Tech Stack:** List languages, frameworks, runtimes, databases, and major libraries with the manifest entries that prove them and their versions where declared.
- **Architecture:** Describe the high-level shape (monolith, layered, microservices, client/server, library, CLI, event-driven) and the main boundaries. Include a Mermaid diagram when the structure warrants one.
- **Component Responsibilities:** Summarize each significant module, package, or service — its responsibility and its key collaborators.
- **Data Flow:** Trace how a representative request or job moves through the system from entry point to persistence or external effect and back. Use a Mermaid sequence or flow diagram when it clarifies the path.
- **External Integrations & Dependencies:** Note databases, queues, third-party APIs, auth providers, and cloud services, with the configuration or client code that evidences them.
- **CI/CD & Deployment:** Describe workflow triggers, build/test/lint/deploy stages, environments, and the deployment target with the workflow or manifest paths.
- **Entry Points & Local Development:** List how to run the project locally and the primary entry points, citing scripts and documented commands. Mark commands as documented rather than verified unless the session actually ran them.

## 5. Produce the Report

Follow [templates/summary-template.md](templates/summary-template.md). Keep the summary proportionate to the project: a small library needs a short summary; a large multi-service system warrants component and data-flow detail. Prefer accurate, concise prose and diagrams over exhaustive file listings.

Display the full Markdown summary in chat. If the user explicitly requests persisted output, write `.agentdx/project-summary.md` (creating the `.agentdx/` directory if needed), state that the file is being created or replaced, and never include secret values.
