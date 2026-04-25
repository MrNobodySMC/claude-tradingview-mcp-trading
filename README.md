# Claude Code + Tradingview = Automated Trading and Journaling

## What you'll be able to do

- Connect Claude to your TradingView chart and execute trades automatically
- Run a safety check — every condition in your strategy must pass before a trade goes through
- Deploy to the cloud and run 24/7 on a schedule, even when your laptop is closed
- Log every trade automatically to `trades.csv` — date, price, fees, net amount, ready for your accountant

---

## A guided and automated installation

I have created an entire prompt for Claude Code to install and setup everything for you, go here: [automated-one-shot.md](prompts/automated-one-shot.md)

---

## Getting Started

### Step 1 — Paste the one-shot prompt into Claude Code

Copy the entire contents of [`prompts/02-one-shot-trade.md`](prompts/02-one-shot-trade.md) and paste it into your Claude Code terminal.

That's it. Claude acts as your onboarding agent — it clones the repo, walks you through connecting your exchange, sets your trading preferences, connects TradingView, optionally builds a strategy from a YouTube channel, deploys to Railway, and runs the bot for the first time. Every step is interactive. It pauses when it needs something from you and handles everything else automatically.

---

## What's Happening Under the Hood

For anyone who wants to understand the steps manually, or troubleshoot a specific part:

### Prerequisites

- **TradingView MCP** must already be set up — see [docs/setup-macos.md](docs/setup-macos.md) (Mac), [docs/setup-windows.md](docs/setup-windows.md) (Windows), [docs/setup-linux.md](docs/setup-linux.md) (Linux)
- **Claude Code** installed and running
- **A Tradovate account** — live funded account required for API access
- **Node.js 18+** — check with `node --version`

---

### Clone the repo

**Mac / Linux:**
```bash
git clone https://github.com/MrNobodySMC/claude-tradingview-mcp-trading
cd claude-tradingview-mcp-trading
```

**Windows:**
```powershell
git clone https://github.com/MrNobodySMC/claude-tradingview-mcp-trading
cd claude-tradingview-mcp-trading
```

---

### Add your exchange credentials

**Mac / Linux:**
```bash
cp .env.example .env
```

**Windows:**
```powershell
Copy-Item .env.example .env
```

**Tradovate:**
```
TRADOVATE_USERNAME=your_username
TRADOVATE_PASSWORD=your_password
PORTFOLIO_VALUE_USD=1000
MAX_TRADE_SIZE_USD=100
MAX_TRADES_PER_DAY=3
```

For other supported exchanges, see the guides in [docs/exchanges/](docs/exchanges/).

---

### Launch TradingView and connect the MCP

**Mac:** See [docs/setup-macos.md](docs/setup-macos.md)

**Windows:** See [docs/setup-windows.md](docs/setup-windows.md)

**Linux:** See [docs/setup-linux.md](docs/setup-linux.md)

Verify with `tv_health_check` — should return `cdp_connected: true`.

---

### Run the bot manually

```bash
node bot.js
```

---

## Deploy to Railway (Run in the Cloud 24/7)

The local setup runs when your laptop is open. Railway lets the bot check for setups around the clock — even while you sleep.

> **Note:** Cloud mode pulls candle data directly from Binance's free market API instead of TradingView. No TradingView Desktop needed in the cloud. The strategy logic and safety check are identical.

### 1. Deploy

```bash
npm install -g @railway/cli
railway login
railway init
railway up
```

### 2. Set your environment variables in Railway

Go to your Railway project → Variables and add everything from `.env.example`:

| Variable | Example |
|----------|---------|
| `TRADOVATE_USERNAME` | your username |
| `TRADOVATE_PASSWORD` | your password |
| `PORTFOLIO_VALUE_USD` | 1000 |
| `MAX_TRADE_SIZE_USD` | 100 |
| `MAX_TRADES_PER_DAY` | 3 |
| `SYMBOL` | BTCUSDT |
| `TIMEFRAME` | 4H |

### 3. Set a cron schedule

In Railway → Settings → Cron Schedule, set how often the bot runs. Recommended:

| Timeframe | Schedule | What it means |
|-----------|----------|----------------|
| 4H chart | `0 */4 * * *` | Every 4 hours |
| 1D chart | `0 9 * * *` | Once a day at 9am UTC |
| 1H chart | `0 * * * *` | Every hour |

### 4. Start in paper trading mode

`PAPER_TRADING=true` logs every decision but never places real orders. Watch a few days of paper trades, confirm the logic matches what you expect, then flip it to `false`.

---

## Build Your Own Strategy (Optional)

The example `rules.json` uses the van de Poppe + Tone Vays BTC strategy. To build one from any trader's public videos:

1. Go to [Apify](https://apify.com?fpr=3ly3yd) and search the actor store for **YouTube Transcript Scraper** — takes about 30 seconds per channel
2. Paste the output into `prompts/01-extract-strategy.md`
3. Run that prompt in Claude Code — it generates a `rules.json` tailored to that trader's methodology

---

## Files

| File | What it does |
|------|-------------|
| `rules.json` | Your strategy — indicators, entry rules, risk rules |
| `.env` | Your exchange credentials (gitignored — never commits) |
| `prompts/01-extract-strategy.md` | Build rules.json from trader transcripts |
| `prompts/automated-one-shot.md` | **The automated prompt — paste this to get started** |
| `safety-check-log.json` | Auto-generated log of every trade decision |
| `trades.csv` | Tax-ready trade record — auto-written on every execution |
| `docs/setup-macos.md` | macOS-specific MCP setup |
| `docs/setup-windows.md` | Windows-specific MCP setup |
| `docs/setup-linux.md` | Linux-specific MCP setup |

---

## Tax Accounting

Every trade the bot places is automatically written to `trades.csv` with the columns your accountant needs:

| Column | Description |
|--------|-------------|
| Date | ISO date of the trade |
| Time | UTC time |
| Exchange | Your exchange |
| Symbol | e.g. BTCUSDT |
| Side | Buy / Sell |
| Quantity | Units traded |
| Price | Price per unit at execution |
| Total USD | Gross trade value |
| Fee (est.) | Estimated exchange fee |
| Net Amount | Total USD minus fee |
| Order ID | Exchange reference |
| Mode | Paper / Live |

At tax time: open the file, hand it to your accountant, or import it directly into your accounting software. Nothing to reconstruct.

For a quick summary of your trading activity, run:

```bash
node bot.js --tax-summary
```

This prints total trades, volume, and fees paid.

---

## Safety

The safety check conditions are not fixed — they come directly from your `rules.json`. If you build a strategy from a YouTube trader's transcripts using the Apify prompt, your safety check will reflect that trader's entry logic. If you use the example strategy, it reflects those conditions. They're yours, not a generic filter.

Every condition in your `entry_rules` must pass before a trade goes through. One fails — nothing happens. The bot tells you exactly which condition failed and the actual value it saw.

Additional guardrails that apply regardless of strategy:
- Maximum trade size capped at `MAX_TRADE_SIZE_USD` in `.env`
- Maximum trades per day capped at `MAX_TRADES_PER_DAY` in `.env`
- Position sizing calculated from your portfolio value — max 1% risk per trade
- Every decision logged to `safety-check-log.json` with exact indicator values
- Every executed trade recorded in `trades.csv` for accounting

**This is not financial advice.** Build your strategy properly. Run the backtest. Paper trade before going live. Never put in more than you can afford to lose.

---

## Resources

- [TradingView MCP server repo](https://github.com/LewisWJackson/tradingview-mcp-jackson)
- [Tradovate API docs](https://api.tradovate.com)
- [Apify](https://apify.com) — search actor store for "YouTube Transcript Scraper"
