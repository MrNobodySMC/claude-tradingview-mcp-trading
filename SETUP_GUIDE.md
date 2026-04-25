# Setup Guide for Claude Code

This file is a step-by-step guide for Claude Code (or any LLM agent) to install and configure the TradingView MCP server. If a user asks you to install or set up this project, follow these steps exactly.

## Step 1: Clone and Install

```bash
git clone https://github.com/MrNobodySMC/claude-tradingview-mcp-trading ~/claude-tradingview-mcp-trading
cd ~/claude-tradingview-mcp-trading
npm install
```

If the user specifies a different install path, use that instead of `~/claude-tradingview-mcp-trading`.

## Step 2: Set Up Rules

Copy the example rules file and open it for the user to fill in:

```bash
cp ~/claude-tradingview-mcp-trading/rules.example.json ~/claude-tradingview-mcp-trading/rules.json
```

Tell the user: "Open `rules.json` and fill in your watchlist (the symbols you trade), your bias criteria (what makes something bullish/bearish for you), and your risk rules. This is what the morning brief uses every day."

## Step 3: Add to MCP Config

Register the server with Claude Code using the CLI:

```bash
claude mcp add --scope user tradingview -- node ~/claude-tradingview-mcp-trading/src/server.js
```

Verify it registered:

```bash
claude mcp list
```

## Step 4: Launch TradingView Desktop

TradingView Desktop must be running with Chrome DevTools Protocol enabled. Both flags are required — without `--remote-allow-origins=*` the connection will be rejected with a 403 error.

**Auto-detect and launch (recommended):**
After the MCP server is connected, use the `tv_launch` tool — it auto-detects TradingView on Mac, Windows, and Linux.

**Manual launch by platform:**

Mac:
```bash
open -a TradingView --args --remote-debugging-port=9222 --remote-allow-origins=*
```

Windows:
```powershell
& "$env:LOCALAPPDATA\TradingView\TradingView.exe" --remote-debugging-port=9222 --remote-allow-origins=*
```

Linux:
```bash
tradingview --remote-debugging-port=9222 --remote-allow-origins=*
```

## Step 5: Restart Claude Code

The MCP server only loads when Claude Code starts. After adding the config:

1. Exit Claude Code (Ctrl+C)
2. Relaunch Claude Code
3. The tradingview MCP server should connect automatically

## Step 6: Verify Connection

Use the `tv_health_check` tool. Expected response:

```json
{
  "success": true,
  "cdp_connected": true,
  "chart_symbol": "...",
  "api_available": true
}
```

If `cdp_connected: false`, TradingView is not running with both required flags.

## Step 7: Run Your First Morning Brief

Ask Claude: *"Run morning_brief and give me my session bias"*

Claude will scan your watchlist, read your indicators, apply your `rules.json` criteria, and print your bias for each symbol.

To save it: *"Save this brief using session_save"*

To retrieve tomorrow: *"Get yesterday's session using session_get"*

## Step 8: Install CLI (Optional)

To use the `tv` CLI command globally:

```bash
cd ~/claude-tradingview-mcp-trading
npm link
```

Then `tv status`, `tv quote`, `tv pine compile`, etc. work from anywhere.

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `cdp_connected: false` | Launch TradingView with both `--remote-debugging-port=9222` and `--remote-allow-origins=*` |
| `ECONNREFUSED` | TradingView isn't running or port 9222 is blocked |
| MCP server not showing in Claude Code | Run `claude mcp list` to verify, restart Claude Code |
| `tv` command not found | Run `npm link` from the project directory |
| Tools return stale data | TradingView may still be loading — wait a few seconds |
| Pine Editor tools fail | Open the Pine Editor panel first (`ui_open_panel pine-editor open`) |

## What to Read Next

- `rules.json` — Your personal trading rules (fill this in before using morning_brief)
- `CLAUDE.md` — Decision tree for which tool to use when (auto-loaded by Claude Code)
- `docs/setup-macos.md` / `docs/setup-windows.md` / `docs/setup-linux.md` — OS-specific setup
