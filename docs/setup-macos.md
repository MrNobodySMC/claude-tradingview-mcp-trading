# macOS Setup Guide

Everything in the main README applies — the only differences are how TradingView is installed and launched on macOS.

---

## 1. Install TradingView Desktop

Download and install from the [TradingView website](https://www.tradingview.com/desktop/). Drag it to your Applications folder as normal.

---

## 2. Clone and Install the MCP Server

```bash
git clone https://github.com/MrNobodySMC/claude-tradingview-mcp-trading ~/claude-tradingview-mcp-trading
cd ~/claude-tradingview-mcp-trading
npm install
```

---

## 3. Register the MCP Server with Claude Code

```bash
claude mcp add --scope user tradingview -- node /Users/YOUR_USERNAME/claude-tradingview-mcp-trading/src/server.js
```

Replace `YOUR_USERNAME` with your macOS username. Run `echo $USER` if unsure.

Verify it registered:

```bash
claude mcp list
```

---

## 4. Set Up the `tv` Alias

Add a shell alias so TradingView always launches with the correct flags:

```bash
echo 'alias tv="open -a TradingView --args --remote-debugging-port=9222 --remote-allow-origins=*"' >> ~/.zshrc
source ~/.zshrc
```

> **Important:** You need BOTH `--remote-debugging-port=9222` AND `--remote-allow-origins=*`. Without the second flag the connection will be rejected with a 403 error.

Then just type `tv` to launch TradingView correctly every time.

---

## 5. Launch TradingView

Close TradingView if it's already open, then:

```bash
tv
```

---

## 6. Verify the CDP Connection

Confirm TradingView is listening on port 9222:

```bash
curl -s http://localhost:9222/json/version
```

Expected output includes `"Browser": "Chrome/..."`. If you get nothing or a 403, TradingView was not launched with the correct flags — close it and run `tv` again.

---

## 7. Restart Claude Code

MCP servers only load at startup. Open a new terminal:

```bash
claude
```

---

## 8. Verify the MCP Connection

In the new Claude Code session:

```
tv_health_check
```

Expected result: `cdp_connected: true` — you're live.

---

## Common Mistakes

| Mistake | Fix |
|---|---|
| Forgetting `--remote-allow-origins=*` | Always include it alongside `--remote-debugging-port=9222` |
| Using `@tradingview/mcp-server` (npm package) | That package doesn't exist — use the cloned repo above |
| Launching TradingView by clicking the icon | Must use `tv` alias or the flags won't be applied |
| Expecting MCP to load in the same session | Always open a new terminal after registering |

---

## 9. Continue with the Main Setup

Once `tv_health_check` passes, go back to the [main README](../README.md) and continue from the BitGet credentials step.
