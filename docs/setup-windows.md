# Windows Setup Guide

Everything in the main README applies — the only differences are how TradingView is installed and launched on Windows.

---

## 1. Install TradingView Desktop

Download and install from the [TradingView website](https://www.tradingview.com/desktop/). Run the installer as normal.

---

## 2. Clone and Install the MCP Server

```powershell
git clone https://github.com/MrNobodySMC/claude-tradingview-mcp-trading $HOME\claude-tradingview-mcp-trading
cd $HOME\claude-tradingview-mcp-trading
npm install
```

---

## 3. Register the MCP Server with Claude Code

Open a terminal and run:

```powershell
claude mcp add --scope user tradingview -- node $HOME\claude-tradingview-mcp-trading\src\server.js
```

Verify it registered:

```powershell
claude mcp list
```

---

## 4. Find Your TradingView Executable

TradingView Desktop on Windows installs as an `.msix` package. The executable is NOT in `Program Files` — find it with:

```powershell
Get-AppxPackage -Name "TradingView*" | Select-Object -ExpandProperty InstallLocation
```

Inside that folder, look for `TradingView.exe`.

---

## 5. Launch TradingView with CDP Enabled

Close TradingView if it's running, then launch it with both required flags:

```powershell
& "C:\Users\[YourName]\AppData\Local\Packages\TradingView.TradingViewDesktop_[hash]\LocalCache\Local\TradingView\TradingView.exe" --remote-debugging-port=9222 --remote-allow-origins=*
```

Replace the path with the one you found in Step 4.

> **Important:** You need BOTH `--remote-debugging-port=9222` AND `--remote-allow-origins=*`. Without the second flag the connection will be rejected with a 403 error.

**Tip:** Save this as a `.ps1` script or desktop shortcut so you don't have to type it every time.

---

## 6. Verify the CDP Connection

In PowerShell, confirm TradingView is listening:

```powershell
Invoke-RestMethod http://localhost:9222/json/version
```

Expected output includes `Browser: Chrome/...`. If you get nothing or a 403, TradingView was not launched with the correct flags — close it and rerun the command from Step 5.

---

## 7. Restart Claude Code

MCP servers only load at startup. Open a new terminal:

```powershell
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
| Launching TradingView normally (not via PowerShell) | Must launch with the flags — normal launch won't open port 9222 |
| Expecting MCP to load in the same session | Always open a new terminal after registering |
