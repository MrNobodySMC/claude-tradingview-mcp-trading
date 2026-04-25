# Claude Code + Tradingview = Automated Trading and Journaling

## What you'll be able to do

- Connect Claude to your TradingView chart and execute trades automatically
- Run a safety check — every condition in your strategy must pass before a trade goes through
- Log every trade automatically to `trades.csv` or my Trading jounal (link in X BIO) — date, price, fees, net amount, ready for your accountant.

---

## A guided and automated installation

Copy the entire contents of [`prompts/automated-one-shot.md`](prompts/automated-one-shot.md) and paste it into your Claude Code terminal. Claude walks you through every step interactively — it pauses when it needs something from you and handles everything else automatically.

---

## Prerequisites

- **TradingView MCP** set up — see [docs/setup-macos.md](docs/setup-macos.md) (Mac), [docs/setup-windows.md](docs/setup-windows.md) (Windows), or [docs/setup-linux.md](docs/setup-linux.md) (Linux)
- **Claude Code** installed and running
- **Node.js 18+** — check with `node --version`
- **An exchange account** — guides below

---

## Exchange API Key Guides

Two rules that apply to every exchange — **withdrawals OFF, IP whitelist ON**.

| Exchange | Guide |
|----------|-------|
| Tradovate | [docs/exchanges/tradovate.md](docs/exchanges/tradovate.md) |
| BitGet | [docs/exchanges/bitget.md](docs/exchanges/bitget.md) |
| Binance | [docs/exchanges/binance.md](docs/exchanges/binance.md) |
| Bybit | [docs/exchanges/bybit.md](docs/exchanges/bybit.md) |
| OKX | [docs/exchanges/okx.md](docs/exchanges/okx.md) |
| Coinbase Advanced | [docs/exchanges/coinbase.md](docs/exchanges/coinbase.md) |
| Kraken | [docs/exchanges/kraken.md](docs/exchanges/kraken.md) |
| KuCoin | [docs/exchanges/kucoin.md](docs/exchanges/kucoin.md) |
| Gate.io | [docs/exchanges/gateio.md](docs/exchanges/gateio.md) |
| MEXC | [docs/exchanges/mexc.md](docs/exchanges/mexc.md) |
| Bitfinex | [docs/exchanges/bitfinex.md](docs/exchanges/bitfinex.md) |

---

## Files

| File | What it does |
|------|-------------|
| `rules.json` | Your strategy — indicators, entry rules, risk rules |
| `.env` | Your exchange credentials (gitignored — never commits) |
| `prompts/automated-one-shot.md` | **The one-shot prompt — paste this to start** |
| `safety-check-log.json` | Auto-generated log of every trade decision |
| `trades.csv` | Tax-ready trade record — auto-written on every execution |
| `docs/setup-macos.md` | macOS-specific MCP setup |
| `docs/setup-windows.md` | Windows-specific MCP setup |
| `docs/setup-linux.md` | Linux-specific MCP setup |

---

## Trading Journal

Connect Claude to your Notion workspace so it can log trades, notes, and observations directly to your journal.

In Claude Code, go to **Settings → Connectors** and connect your Notion account. Once connected, Claude can read and write to any page or database in your workspace — use it to log every trade with context, not just the numbers.

---

## Resources

how to get tradovate api access: https://youtu.be/P6cJF6Q2Y24?si=kmwJCInoiZ3Q2JXI
