# Tech Stack Detection & Expected Instructions

When assessing instruction quality, first detect the repository's tech stack by looking for signature files. Then verify that agent instructions mention the relevant commands and patterns.

## Stack Detection Rules

| Indicator Files | Detected Stack | Expected in Instructions |
|----------------|---------------|--------------------------|
| `package.json`, `node_modules/` | **Node.js** | `npm install`/`yarn`/`pnpm`, `npm run build`, `npm test`, `node` version |
| `tsconfig.json`, `*.ts` | **TypeScript** | `tsc`, type-checking command, strict mode notes |
| `next.config.*` | **Next.js** | `next dev`, `next build`, routing conventions, App vs Pages router |
| `vite.config.*`, `vue.config.*` | **Vite/Vue** | `vite dev`, `vite build`, component conventions |
| `angular.json` | **Angular** | `ng serve`, `ng build`, module/component patterns |
| `requirements.txt`, `pyproject.toml`, `setup.py` | **Python** | `pip install`, virtualenv/venv, `python` version |
| `pytest.ini`, `conftest.py`, `tests/` with `test_*.py` | **pytest** | `pytest`, test conventions, fixtures |
| `manage.py`, `django/` in deps | **Django** | `python manage.py`, migrations, `runserver` |
| `Cargo.toml` | **Rust** | `cargo build`, `cargo test`, `cargo clippy` |
| `go.mod` | **Go** | `go build`, `go test`, `go vet`, module structure |
| `pom.xml`, `build.gradle` | **Java/Kotlin** | `mvn`/`gradle` commands, build lifecycle |
| `Gemfile` | **Ruby** | `bundle install`, `rails`/`rake` commands |
| `Dockerfile`, `docker-compose.yml` | **Docker** | `docker build`, `docker compose up`, container setup |
| `terraform/`, `*.tf` | **Terraform** | `terraform plan`, `terraform apply`, state management |
| `.github/workflows/` | **GitHub Actions** | CI workflow details, how to run checks locally |

## How to Use This

1. **Detect**: Scan for indicator files at repo root and immediate subdirectories
2. **Map**: Identify which stacks are present (repos often have multiple)
3. **Verify**: Check that agent instruction files mention the expected commands/patterns
4. **Report**: Flag missing stack-specific instructions as findings

## Scoring Impact

This feeds into the **Completeness** criterion of Instruction Quality:
- If a stack is detected but its commands are never mentioned in any instruction file → flag as a gap
- If 3+ stacks are detected and all are covered → bonus indicator of thoroughness
- Don't penalize for stacks that are tangential (e.g., Docker in a repo that's primarily a library)

## Examples of Good Stack-Aware Instructions

**Node.js/TypeScript repo:**
```markdown
## Commands
- `npm install` — install dependencies
- `npm run build` — compile TypeScript
- `npm test` — run Jest tests
- `npm run lint` — ESLint check

## Conventions
- Use strict TypeScript (no `any`)
- Components in PascalCase, utils in camelCase
```

**Python/Django repo:**
```markdown
## Commands
- `python -m venv .venv && source .venv/bin/activate`
- `pip install -r requirements.txt`
- `python manage.py migrate`
- `pytest tests/ -v`

## Conventions
- Follow PEP 8, use type hints
- Models in `app/models/`, views in `app/views/`
```
