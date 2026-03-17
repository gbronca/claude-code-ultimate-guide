---
name: rtk-optimizer
description: "Wrap high-verbosity shell commands with RTK to reduce token consumption. Use when running git log, git diff, cargo test, pytest, or other verbose CLI output that wastes context window tokens."
version: 1.0.0
tags: [optimization, tokens, efficiency, git]
---

# RTK Optimizer Skill

Automatically suggest and apply RTK (Rust Token Killer) wrappers for verbose commands, reducing token usage by ~73% on average.

## How It Works

1. **Detect high-verbosity commands** in user requests
2. **Suggest RTK wrapper** with expected savings
3. **Execute with RTK** when user confirms
4. **Track savings** over session via `rtk gain`

## Prerequisites

```bash
rtk --version  # Requires rtk 0.16.0+

# Install if needed:
brew install rtk-ai/tap/rtk    # macOS/Linux
cargo install rtk               # All platforms
```

## Supported Commands

| Command | RTK Equivalent | Reduction |
|---------|---------------|-----------|
| `git log` | `rtk git log` | 92% (13,994 -> 1,076 chars) |
| `git status` | `rtk git status` | 76% |
| `git diff` | `rtk git diff` | 56% (15,815 -> 6,982 chars) |
| `find` | `rtk find` | 76% |
| `cat <large-file>` | `rtk read <file>` | 63% (163K -> 61K chars) |
| `pnpm list` | `rtk pnpm list` | 82% |
| `vitest run` / `pnpm test` | `rtk vitest run` | 90% |
| `cargo test` | `rtk cargo test` | 90% |
| `cargo build` | `rtk cargo build` | 80% |
| `cargo clippy` | `rtk cargo clippy` | 80% |
| `pytest` | `rtk python pytest` | 90% |
| `go test` | `rtk go test` | 90% |
| `gh pr view` | `rtk gh pr view` | 87% |
| `gh pr checks` | `rtk gh pr checks` | 79% |
| `ls` | `rtk ls` | condensed |
| `grep` | `rtk grep` | filtered |

## Usage Pattern

```markdown
# When user requests a verbose command:

1. Acknowledge the request
2. Suggest RTK: "I'll use `rtk git log` to reduce token usage by ~92%"
3. Execute the RTK-wrapped command
4. Report savings: "Saved ~13K tokens (baseline: 14K, RTK: 1K)"
```

## Activation Examples

**User**: "Show me the git history"
**Action**: Detect `git log` -> execute `rtk git log` -> report 92% savings

**User**: "Run the test suite"
**Action**: Detect `cargo test` / `pytest` -> execute `rtk cargo test` -> report 90% savings

## When to Skip RTK

- **Small outputs** (<100 chars): Overhead not worth it
- **Claude built-in tools**: Grep/Read tools are already optimized
- **Interactive commands**: RTK is for batch/non-interactive output only
- **Multiple piped commands**: Wrap the outermost command, not each step

## Error Handling

- If `rtk` is not found, fall back to the raw command and suggest installation
- If RTK output is empty or malformed, re-run without RTK and report the issue
- If RTK version is outdated, warn about potential breaking changes (rapid release cadence)

## Session Tracking

```bash
rtk gain  # Shows cumulative token savings for the session (SQLite-backed)
```

## References

- RTK GitHub: https://github.com/rtk-ai/rtk
- Evaluation: `docs/resource-evaluations/rtk-evaluation.md`
- CLAUDE.md template: `examples/claude-md/rtk-optimized.md`
