# AgentDX Report Format

When presenting results, use this exact structure:

## Header

```
## AgentDX Score: {overall_score}/100 ({grade_label})
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
  "score": 72,
  "grade": "Good",
  "categories": {
    "agent-configs": { "score": 85, "status": "pass", "findings": [...] },
    "instruction-quality": { "score": 70, "status": "warning", "findings": [...] },
    "mcp-setup": { "score": 50, "status": "warning", "findings": [...] },
    "custom-skills": { "score": 60, "status": "warning", "findings": [...] },
    "security": { "score": 90, "status": "pass", "findings": [...] },
    "repo-structure": { "score": 75, "status": "warning", "findings": [...] },
    "ci-cd": { "score": 65, "status": "warning", "findings": [...] }
  },
  "platforms_detected": ["Claude Code", "GitHub Copilot"],
  "recommendations": [
    { "action": "...", "impact_points": 8 },
    { "action": "...", "impact_points": 5 },
    { "action": "...", "impact_points": 3 }
  ]
}
```

Each finding in the `findings` array should be:
```json
{ "status": "pass|partial|fail", "criterion": "...", "detail": "..." }
```
