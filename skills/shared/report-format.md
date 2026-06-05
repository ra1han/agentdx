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

## Recommendations

End with the top 3 most impactful recommendations, ordered by potential score improvement:

```
### Top 3 Recommendations
1. {highest impact action}
2. {second highest impact action}
3. {third highest impact action}
```

## Selective Scan Note

When running a selective scan, add after the header:
```
> Scanned categories: {list}. Score reflects only selected categories.
```
