---
name: audit-agents-skills
description: "Audit Claude Code agents, skills, and commands for quality and production readiness. Use when evaluating skill quality, checking production readiness scores, or comparing agents against best-practice templates."
allowed-tools: Read, Grep, Glob, Bash, Write
context: inherit
agent: specialist
version: 1.0.0
tags: [quality, audit, agents, skills, validation, production-readiness]
---

# Audit Agents/Skills/Commands

Score Claude Code agents, skills, and commands across 16 weighted criteria. Outputs production readiness grades (A-F) with actionable fix suggestions.

## Modes

| Mode | Usage | Output |
|------|-------|--------|
| **Quick Audit** | Top-5 critical criteria only | Fast pass/fail (3-5 min for 20 files) |
| **Full Audit** | All 16 criteria per file | Detailed scores + recommendations (10-15 min) |
| **Comparative** | Full + benchmark vs templates | Analysis + gap identification (15-20 min) |

**Default**: Full Audit (recommended for first run)

## Workflow

### Phase 1: Discovery

Scan and classify files from:
```
.claude/agents/       .claude/skills/       .claude/commands/
examples/agents/      examples/skills/      examples/commands/
```

**Checkpoint**: Confirm file count and types before proceeding to scoring.

### Phase 2: Scoring

For each file:
1. Parse YAML frontmatter
2. Extract content sections
3. Run detection patterns (regex, keyword search)
4. Calculate score: `(points / max_points) x 100`
5. Assign grade: A (90-100%), B (80-89%), C (70-79%), D (60-69%), F (<60%)

**Checkpoint**: Verify at least one file scores successfully before batch processing.

**Grade threshold**: 80% (Grade B) = minimum for production deployment.

### Phase 3: Comparative Analysis (Comparative Mode Only)

1. Match each project file to closest reference template
2. Compare scores per criterion
3. Flag gaps >10 points

### Phase 4: Report Generation

Output both `audit-report.md` (human-readable) and `audit-report.json` (programmatic):

```json
{
  "summary": {
    "overall_score": 82.5,
    "overall_grade": "B",
    "total_files": 15,
    "production_ready_count": 10
  },
  "files": [
    {
      "path": ".claude/agents/debugging-specialist.md",
      "score": 78.1,
      "grade": "C",
      "failed_criteria": [
        { "id": "A2.4", "name": "Anti-hallucination measures", "points_lost": 2 }
      ]
    }
  ]
}
```

**Checkpoint**: Verify report file is written and contains all scanned files.

### Phase 5: Fix Suggestions (Optional)

For each failing criterion, generate actionable fix with the specific section to add and detection keywords to verify the fix.

## Scoring Criteria

### Agents (32 points max)

| Category | Weight | Max Points | Key Criteria |
|----------|--------|------------|--------------|
| Identity | 3x | 12 | Clear name, description with triggers, role defined |
| Prompt Quality | 2x | 8 | 3+ examples, anti-hallucination measures |
| Validation | 1x | 4 | Error handling, no hardcoded paths |
| Design | 2x | 8 | Single responsibility, integration documented |

### Skills (32 points max)

| Category | Weight | Max Points | Key Criteria |
|----------|--------|------------|--------------|
| Structure | 3x | 12 | Valid SKILL.md, valid name, methodology section |
| Content | 2x | 8 | Clear triggers, usage examples |
| Technical | 1x | 4 | No hardcoded paths, token budget |
| Design | 2x | 8 | Modular, references other skills |

### Commands (20 points max)

| Category | Weight | Max Points | Key Criteria |
|----------|--------|------------|--------------|
| Structure | 3x | 12 | Valid frontmatter, argument hint, step-by-step |
| Quality | 2x | 8 | Error handling, mentions failure modes |

## Usage

```bash
# Full audit (default)
Use skill: audit-agents-skills

# Specify path
Use skill: audit-agents-skills for ~/projects/my-app

# Quick audit
Use skill: audit-agents-skills with mode=quick

# Comparative with benchmarks
Use skill: audit-agents-skills with mode=comparative

# Generate fixes
Use skill: audit-agents-skills with fixes=true

# JSON output for CI/CD
Use skill: audit-agents-skills with format=json output=audit.json
```

## Integration with CI/CD

### Pre-commit Hook

```bash
#!/bin/bash
changed_files=$(git diff --cached --name-only | grep -E "^\.claude/(agents|skills|commands)/")
if [ -n "$changed_files" ]; then
    echo "Running quick audit on changed files..."
    # Exit with 1 if any file scores <80%
fi
```

### GitHub Actions

```yaml
name: Audit Agents/Skills
on: [pull_request]
jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run quality audit
        run: |
          # Run audit skill, parse JSON, fail if overall_score < 80
```

## Related

- **Command version**: `.claude/commands/audit-agents-skills.md` (quick checks, dev workflow)
- **Reference templates**: `examples/agents/`, `examples/skills/`, `examples/commands/`
