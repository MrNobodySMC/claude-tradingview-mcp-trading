# Claude Code + TradingView = Automated systems and Journaling

## What you'll be able to do

- Connect Claude to your live TradingView chart via the MCP server
- Read indicators, price levels, OHLCV data, and Pine Script output directly from your chart
- Execute Claude commands that control TradingView — change symbols, timeframes, draw levels, set alerts
- Log trades and analysis to your Notion trading journal

---

## Requirements

- **Claude Code** installed — [code.claude.com](https://code.claude.com/docs/en/overview)
- **TradingView Desktop** installed — [tradingview.com/desktop](https://www.tradingview.com/desktop/)
- **Node.js 18+** — check with `node --version`

---

## Setup

Copy the entire contents of [`prompts/automated-one-shot.md`](prompts/automated-one-shot.md) and paste it into your Claude Code terminal. Claude walks you through every step interactively.

Or follow the manual guide for your OS:

- [macOS](docs/setup-macos.md)
- [Windows](docs/setup-windows.md)
- [Linux](docs/setup-linux.md)

Verify with `tv_health_check` — should return `cdp_connected: true`.

---

## Trading Journal

Connect Claude to your Notion workspace so it can log trades, notes, and observations directly to your journal.

On the Claude web app (claude.ai), go to **Settings → Connectors** and connect your Notion account. Once connected, Claude can read and write to any page or database in your workspace.

---

## Files

| File | What it does |
|------|-------------|
| `src/server.js` | MCP server — the bridge between Claude and TradingView |
| `CLAUDE.md` | Decision tree for which tool to use when (auto-loaded by Claude Code) |
| `docs/setup-macos.md` | macOS-specific setup |
| `docs/setup-windows.md` | Windows-specific setup |
| `docs/setup-linux.md` | Linux-specific setup |

---

## Resources

how to get tradovate api access: https://youtu.be/P6cJF6Q2Y24?si=kmwJCInoiZ3Q2JXI
