---
name: ccboard
description: "Launch and navigate the ccboard TUI/Web dashboard for Claude Code. Use when monitoring token usage, tracking costs, browsing sessions, or checking MCP server status across projects."
version: 0.1.0
category: monitoring
keywords: [dashboard, tui, mcp, sessions, costs, analytics]
tags: [dashboard, tui, monitoring, claude-code, costs]
---

# ccboard - Claude Code Dashboard

TUI/Web dashboard for monitoring Claude Code usage: sessions, costs, tokens, MCP servers, and configuration.

## Prerequisites

- Rust 1.70+ and Cargo
- Claude Code installed (reads from `~/.claude/`)

```bash
# Install
cargo install ccboard
# Or via Claude Code command
/ccboard-install
```

## Commands

| Command | Description | Shortcut |
|---------|-------------|----------|
| `/dashboard` | Launch TUI dashboard | `ccboard` |
| `/mcp-status` | Open MCP servers tab | Press `8` |
| `/costs` | Open costs analysis | Press `6` |
| `/sessions` | Browse sessions | Press `2` |
| `/ccboard-web` | Launch web UI | `ccboard web` |
| `/ccboard-install` | Install/update ccboard | - |

## Tabs Overview

| Tab | Key | What It Shows |
|-----|-----|---------------|
| Dashboard | `1` | Token stats, cache ratio, 7-day sparkline, model gauges |
| Sessions | `2` | Project tree + session list, search with `/`, edit with `e` |
| Config | `3` | Cascading settings: Global / Project / Local / Merged |
| Hooks | `4` | Event-based hooks, script preview, match patterns |
| Agents | `5` | Agents, commands, skills with frontmatter extraction |
| Costs | `6` | Overview, by-model breakdown, daily trend |
| History | `7` | Full-text search across all sessions |
| MCP | `8` | Server status (Running/Stopped), details, quick actions |

## Navigation

| Keys | Action |
|------|--------|
| `1-8` | Jump to tab |
| `Tab` / `Shift+Tab` | Navigate tabs |
| `h/j/k/l` or arrows | Navigate within tab |
| `Enter` | View details / Focus pane |
| `e` | Edit file in `$EDITOR` |
| `o` | Reveal file in finder |
| `/` | Search (Sessions/History) |
| `F5` | Refresh data |
| `q` | Quit |

## Usage Examples

### Daily Monitoring

```bash
/dashboard
# Press '1' for overview, '6' for costs, '7' for history
```

### MCP Troubleshooting

```bash
/mcp-status
# Check server status (green = running)
# Press 'e' to edit config, 'r' to refresh status
```

### Session Analysis

```bash
/sessions
# Press '/' to search by project, model, or message content
# Press 'e' on a session to view full JSONL
```

## Web Interface

```bash
/ccboard-web                  # Launch at http://localhost:3333
ccboard web --port 8080       # Custom port
ccboard both --port 3333      # TUI + Web simultaneously
```

## Validation

After launching, verify ccboard is working:

1. Run `/dashboard` and confirm token stats load on tab `1`
2. Press `2` and verify sessions are listed
3. Press `6` and confirm cost data appears
4. If no data: check `ls ~/.claude/` and `cat ~/.claude/stats-cache.json`

## Troubleshooting

- **ccboard not found**: Run `/ccboard-install` or `cargo install ccboard`
- **No data visible**: Verify `~/.claude/` exists and contains `stats-cache.json`
- **MCP shows "Unknown"**: Status detection requires Unix; Windows shows "Unknown" by default
- **File watcher issues**: Check file permissions on `~/.claude/`, restart ccboard
