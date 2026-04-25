# Linux Setup Guide

Everything in the main README applies — the only differences are how TradingView is installed and launched on Linux.

---

## 1. Install TradingView Desktop

TradingView Desktop is available for Linux via:

**Option A — Snap (most common):**
```bash
sudo snap install tradingview
```

**Option B — Flatpak:**
```bash
flatpak install flathub com.tradingview.TradingViewDesktop
```

**Option C — AppImage:**
Download from the [TradingView website](https://www.tradingview.com/desktop/) and make it executable:
```bash
chmod +x TradingView-*.AppImage
```

---

## 2. Detect Which Install Type You Have

Run these commands to find out:

```bash
which tradingview 2>/dev/null
flatpak list 2>/dev/null | grep -i trading
find ~/Desktop /opt -name "*.AppImage" 2>/dev/null | grep -i trading
```

- `/usr/bin/tradingview` → **Snap**
- `flatpak list` shows it → **Flatpak**
- `.AppImage` file found → **AppImage**

---

## 3. Clone and Install the MCP Server

```bash
git clone https://github.com/MrNobodySMC/claude-tradingview-mcp-trading ~/claude-tradingview-mcp-trading
cd ~/claude-tradingview-mcp-trading
npm install
```

---

## 4. Register the MCP Server with Claude Code

Use the CLI command — do NOT edit config files manually:

```bash
claude mcp add --scope user tradingview -- node ~/claude-tradingview-mcp-trading/src/server.js
```

Verify it registered:

```bash
claude mcp list
```

---

## 5. Set Up the `tv` Alias

Add a shell alias so TradingView always launches with the correct flags.

> **Important:** You need BOTH `--remote-debugging-port=9222` AND `--remote-allow-origins=*`. Without the second flag, the connection will be rejected with a 403 error.

**Snap:**
```bash
echo 'alias tv="tradingview --remote-debugging-port=9222 --remote-allow-origins=*"' >> ~/.bashrc
source ~/.bashrc
```

**Flatpak:**
```bash
echo 'alias tv="flatpak run com.tradingview.TradingViewDesktop --remote-debugging-port=9222 --remote-allow-origins=*"' >> ~/.bashrc
source ~/.bashrc
```

**AppImage:**

First find the full path to your AppImage file:
```bash
find ~ -name "*.AppImage" 2>/dev/null | grep -i trading
```

Then add the alias using that exact path:
```bash
echo 'alias tv="/full/path/to/TradingView.AppImage --remote-debugging-port=9222 --remote-allow-origins=*"' >> ~/.bashrc
source ~/.bashrc
```

Replace `/full/path/to/TradingView.AppImage` with the path returned above.

Then just type `tv` to launch TradingView correctly every time.

---

## 6. Launch TradingView

Close TradingView if it is already running, then:

```bash
tv
```

---

## 7. Verify the CDP Connection

Confirm TradingView is listening on port 9222:

```bash
curl -s http://localhost:9222/json/version
```

Expected output includes `"Browser": "Chrome/..."`. If you get nothing or a 403, TradingView was not launched with the correct flags — close it and run `tv` again.

---

## 8. Restart Claude Code

MCP servers only load at startup. Open a new terminal:

```bash
claude
```

---

## 9. Verify the MCP Connection

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
| AppImage alias not working | Make sure you used the full absolute path, not `./` |
| Editing `~/.claude/mcp.json` directly | Use `claude mcp add` instead |
| Expecting MCP to load in the same session | Always open a new terminal after registering |
