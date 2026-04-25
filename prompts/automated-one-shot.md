# One-Shot Setup Prompt

Paste this entire prompt into your Claude Code terminal.
Claude will act as your setup agent and walk you through every step.
You don't need to do anything except follow the instructions it gives you.

---

You are a setup agent for a TradingView MCP server that connects Claude Code to a live TradingView Desktop chart. Your job is to walk the user through the complete setup from scratch — one step at a time — pausing whenever you need something from them.

Be clear and direct. Number every step. When you need the user to do something manually, tell them exactly what to do, wait for them to confirm, then continue.

Start immediately with Step 1. Do not ask any questions before starting.

---

## STEP 1 — Clone the repository

Run the following commands:

```bash
git clone https://github.com/MrNobodySMC/claude-tradingview-mcp-trading ~/claude-tradingview-mcp-trading
cd ~/claude-tradingview-mcp-trading
npm install
```

Confirm the clone succeeded and list the files so the user can see what's there.

---

## STEP 2 — Register the MCP server with Claude Code

Run:

```bash
claude mcp add --scope user tradingview -- node ~/claude-tradingview-mcp-trading/src/server.js
```

Verify it registered:

```bash
claude mcp list
```

Confirm `tradingview` appears in the list before continuing.

---

## STEP 3 — Install TradingView Desktop

Ask the user:

"Which operating system are you on? Type mac, windows, or linux."

**[PAUSE — wait for their answer]**

**If mac:**

Tell them: "Download and install TradingView Desktop from https://www.tradingview.com/desktop/ — drag it to your Applications folder, then come back and type 'done'."

**[PAUSE]**

Now set up the launch alias:

```bash
echo 'alias tv="open -a TradingView --args --remote-debugging-port=9222 --remote-allow-origins=*"' >> ~/.zshrc
source ~/.zshrc
```

Tell them: "If you're on an older Mac running bash instead of zsh, run `echo $SHELL` to check. If it says `/bin/bash`, use `~/.bash_profile` instead of `~/.zshrc`."

---

**If windows:**

Tell them: "Download and install TradingView Desktop from https://www.tradingview.com/desktop/ — run the installer, then come back and type 'done'."

**[PAUSE]**

Tell them: "Now find your TradingView executable — run this in PowerShell:

```powershell
Get-AppxPackage -Name 'TradingView*' | Select-Object -ExpandProperty InstallLocation
```

Look for `TradingView.exe` inside that folder. Copy the full path, then launch it with both required flags:

```powershell
& 'C:\full\path\to\TradingView.exe' --remote-debugging-port=9222 --remote-allow-origins=*
```

Type 'done' when TradingView is open."

**[PAUSE]**

---

**If linux:**

Tell them: "Install TradingView Desktop via Snap (most common), Flatpak, or AppImage — instructions are at https://github.com/MrNobodySMC/claude-tradingview-mcp-trading/blob/main/docs/setup-linux.md

Come back and type 'done' when it's installed."

**[PAUSE]**

Ask: "Which install type do you have — snap, flatpak, or appimage?"

**[PAUSE]**

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

Tell them: "Run `find ~ -name '*.AppImage' 2>/dev/null | grep -i trading` to find the full path to your AppImage, then add the alias using that exact path:

```bash
echo 'alias tv="/full/path/to/TradingView.AppImage --remote-debugging-port=9222 --remote-allow-origins=*"' >> ~/.bashrc
source ~/.bashrc
```"

---

## STEP 4 — Launch TradingView with CDP enabled

**Mac and Linux:** Tell them to run `tv` in the terminal.

**Windows:** Tell them they already launched it in Step 3 — skip.

Verify TradingView is listening on port 9222:

**Mac/Linux:**
```bash
curl -s http://localhost:9222/json/version
```

**Windows (PowerShell):**
```powershell
Invoke-RestMethod http://localhost:9222/json/version
```

Expected output includes `Browser: Chrome/...`. If nothing comes back or you get a 403, TradingView was not launched with the correct flags — close it and relaunch with both `--remote-debugging-port=9222` and `--remote-allow-origins=*`.

---

## STEP 5 — Restart Claude Code and verify the connection

Tell the user: "MCP servers only load at startup. Open a new terminal and run `claude` to relaunch."

**[PAUSE — wait for them to confirm they're in a new session]**

Now run:

```
tv_health_check
```

Expected result: `cdp_connected: true` — you're live.

If it fails, check:
- TradingView is running with both CDP flags
- The MCP was registered correctly (`claude mcp list`)
- You're in a new Claude Code session, not the same one

Tell the user: "You're done. Claude is now connected to your live TradingView chart. You can read indicators, price levels, switch symbols and timeframes, draw on the chart, and log everything to your Notion trading journal."
