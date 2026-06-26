# AgentDX Report Format

When presenting results, use this exact structure:

## Header

```
## AgentDX Score: {overall_score}/100 ({grade_label})
Readiness Level: {H0-H5 level}
Highest Proof Level: {L0-L6 level}
Target Next Proof Level: {L0-L6 level}
Confidence: {High|Medium|Low}
```

For tool-scoped scans, add immediately after the header:
```
> Target platform: {platform_name}
```

## Category Breakdown Table

```
### Category Breakdown
| Category              | Score | Status |
|-----------------------|-------|--------|
| {category_name}       | {score} | {status_emoji} |
```

Status emoji rules:
- Score >= 80: ✅
- Score 50–79: ⚠️
- Score < 50: ❌

## Findings Section

For each category, list individual findings:

```
#### {Category Name} ({score}/100)
- ✅ {passing criterion description}
- ⚠️ {partial criterion description}
- ❌ {missing/failing criterion description}
```

## Agent Proof Summary

After findings, include a concise proof-oriented summary when evidence exists:

```
### Agent Proof Summary
| Signal | Detected State |
|--------|----------------|
| Readiness Level | {H0-H5 with short reason} |
| Highest Proof Level | {L0-L6 with evidence} |
| Target Next Proof Level | {L0-L6 with recommendation link} |
| Fast Local Feedback | {command/status or missing} |
| CI/Local Equivalence | {identical/equivalent/partial/divergent/one-sided/unknown} |
| Runtime Smoke Path | {command/status/N/A} |
| Observable Evidence | {artifacts/logs/reports/state checks or missing} |
| Reset/Cleanup Path | {command/status/N/A} |
```

Do not claim runtime proof from static intent alone. If evidence is inferred, say so.

## Environment and Dependency Pressure

Include this section when setup or external dependency evidence is found:

```
### Environment & Dependency Pressure
| Item | Type | Local Option | Secret Required | Remote Required | Proof Impact |
|------|------|--------------|-----------------|-----------------|--------------|
| {name} | {env/dependency type} | {native/container/fake/in_memory/testcontainers/sandbox/dry_run/none/unknown} | {yes/no/optional/unknown} | {yes/no/optional/unknown} | {blocks/partial/no/unknown} |
```

For environment variables, report names only. Never include secret values.

## Command Tiers

Include this section when commands are discovered:

```
### Command Tiers
| Tier | Command | Status | Notes |
|------|---------|--------|-------|
| bootstrap | `{command}` | verified/configured_unverified/candidate_unverified/not_applicable/unknown | {notes} |
```

Use these tiers when relevant: bootstrap, setup_services, Diagnostics, boot, health, fast, quick, proof, ci_equivalent, smoke, seed_or_reset, observe, cleanup, retro_or_improve.

## Test and Proof Mechanisms

Include this section when test or harness mechanisms are discovered:

```
### Test & Proof Mechanisms
| Mechanism | Where Found | Reusable Agent Affordance |
|-----------|-------------|---------------------------|
| fake/sink/stub/mock/contract/testcontainer/in_memory/fixture/factory/reset | {path or evidence} | {how an agent can use or wrap it} |
```

Prefer fakes, sinks, fixtures, and deterministic checks in recommendations when they would prove behavior better than more prose.

## Platform Detection (Informational)

After findings, report detected platforms:

```
### Detected Platforms
Active agent platforms: {list of detected platforms, e.g., "Claude Code, GitHub Copilot"}
```

## Recommendations

End with the top 3 most impactful recommendations, ordered by potential score improvement:

```
### Top 3 Recommendations
1. {highest impact action} (+{estimated score improvement} points)
2. {second highest impact action} (+{estimated score improvement} points)
3. {third highest impact action} (+{estimated score improvement} points)
```

## Selective Scan Note

When running a selective scan, add after the header:
```
> Scanned categories: {list}. Score reflects only selected categories.
```

## JSON Output Mode

The JSON report is always saved to `.agentdx/report.json`. It is also displayed in chat if the user requests JSON output (e.g., "output as JSON", "give me JSON", "machine-readable").

```json
{
  "timestamp": "2026-06-06T18:00:00Z",
  "target_platform": "GitHub Copilot",
  "score": 72,
  "grade": "Good",
  "readiness_level": {
    "level": "H3",
    "label": "Operable",
    "rationale": "Fast local validation and CI-equivalent checks are discoverable."
  },
  "proof_level": {
    "highest": "L2",
    "highest_label": "Static/Build/Test",
    "target_next": "L3",
    "target_next_label": "Runtime Interaction",
    "rationale": "Unit and lint checks exist, but no smoke path was found."
  },
  "confidence": "Medium",
  "categories": {
    "agent-configs": { "score": 85, "status": "pass", "findings": [...] },
    "instruction-quality": { "score": 70, "status": "warning", "findings": [...] },
    "security": { "score": 90, "status": "pass", "findings": [...] },
    "repo-structure": { "score": 75, "status": "warning", "findings": [...] },
    "ci-cd": { "score": 65, "status": "warning", "findings": [...] },
    "development-workflow": { "score": 60, "status": "warning", "findings": [...] },
    "setup-environment": { "score": 70, "status": "warning", "findings": [...] },
    "proof-feedback": { "score": 55, "status": "warning", "findings": [...] },
    "adaptability": { "score": 65, "status": "warning", "findings": [...] }
  },
  "command_tiers": [
    {
      "tier": "fast",
      "command": "npm test",
      "status": "configured_unverified",
      "local_available": true,
      "ci_available": true,
      "requires_services": false,
      "requires_secrets": false,
      "mutates_state": false,
      "proof_level": "L2",
      "evidence": "package.json scripts and .github/workflows/ci.yml"
    }
  ],
  "ci_local_equivalence": [
    {
      "check": "tests",
      "local_command": "npm test",
      "ci_command": "npm test",
      "equivalence": "identical"
    }
  ],
  "environment_variables": [
    {
      "name": "DATABASE_URL",
      "required": "yes",
      "purpose_inferred": "database connection",
      "source_files": [".env.example"],
      "example_present": true,
      "safe_default_present": false,
      "secret_like": true,
      "local_dev_notes": "Name only; value omitted."
    }
  ],
  "external_dependencies": [
    {
      "name": "PostgreSQL",
      "type": "database",
      "where_detected": ["compose.yaml"],
      "local_option": "container",
      "remote_required": "no",
      "secret_required": "optional",
      "mutation_risk": "local_only",
      "proof_blocked_by_dependency": "no",
      "recommended_affordance": "Expose seed/reset command if absent."
    }
  ],
  "proof_surfaces": [
    {
      "name": "unit tests",
      "category": "static/build",
      "command_or_check": "npm test",
      "local_available": true,
      "ci_available": true,
      "requires_services": false,
      "requires_secrets": false,
      "mutates_state": false,
      "output_artifact": null,
      "proof_level": "L2",
      "status": "configured_unverified",
      "evidence": "package.json scripts"
    }
  ],
  "test_mechanisms": [
    {
      "mechanism": "fake",
      "where_found": "tests/fakes/",
      "reusable_agent_affordance": "Can be wrapped by smoke/proof command for local dependency substitution."
    }
  ],
  "platforms_detected": ["Claude Code", "GitHub Copilot"],
  "recommendations": [
    { "action": "...", "impact_points": 8, "unblocks_proof_level": "L3", "encoding_type": "smoke" },
    { "action": "...", "impact_points": 5 },
    { "action": "...", "impact_points": 3 }
  ]
}
```

Each finding in the `findings` array should be:
```json
{ "status": "pass|partial|fail", "criterion": "...", "detail": "..." }
```

Optional finding fields:
```json
{
  "status": "pass|partial|fail",
  "criterion": "...",
  "detail": "...",
  "evidence": ["README.md", "package.json"],
  "proof_level": "L0|L1|L2|L3|L4|L5|L6",
  "confidence": "high|medium|low"
}
```
