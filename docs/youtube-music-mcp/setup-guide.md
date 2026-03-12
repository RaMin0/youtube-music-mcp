# Setup Guide

Configure YouTube Music MCP with Codex, Claude Code, Claude Desktop, Cursor, Gemini CLI, GitHub Copilot, VS Code, Windsurf, and similar MCP-compatible clients. Use each client's CLI or built-in MCP flow when it exists, and fall back to direct config editing when it does not.

## Prerequisites

1. Install the **YouTube Music MCP Bridge** app from the DMG and drag it to Applications.
2. Open the app once so the menu bar bridge starts.
3. Enable the Safari extension in **Safari -> Settings -> Extensions**.
4. Open [music.youtube.com](https://music.youtube.com) in Safari.

## Paths used below

Shell commands use the escaped binary path:

```bash
~/Applications/YouTube\ Music\ MCP\ Bridge.app/Contents/Resources/youtube-music-mcp
```

Config files and app settings should use the unescaped absolute path, for example:

```text
/Users/YOUR_NAME/Applications/YouTube Music MCP Bridge.app/Contents/Resources/youtube-music-mcp
```

> **Tip:** If you installed the app to `/Applications`, replace the `/Users/YOUR_NAME/Applications` prefix with `/Applications`.

---

## Codex

Run in your terminal:

```bash
codex mcp add youtube-music -- ~/Applications/YouTube\ Music\ MCP\ Bridge.app/Contents/Resources/youtube-music-mcp
```

To verify:

```bash
codex mcp list
```

To remove:

```bash
codex mcp remove youtube-music
```

---

## Claude Code

Run in your terminal:

```bash
claude mcp add youtube-music -- ~/Applications/YouTube\ Music\ MCP\ Bridge.app/Contents/Resources/youtube-music-mcp
```

To verify:

```bash
claude mcp list
```

To remove:

```bash
claude mcp remove youtube-music
```

---

## Claude Desktop

Open the Claude Desktop config file:

```bash
open ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

Add or merge this `mcpServers` entry:

```json
{
  "mcpServers": {
    "youtube-music": {
      "command": "/Users/YOUR_NAME/Applications/YouTube Music MCP Bridge.app/Contents/Resources/youtube-music-mcp"
    }
  }
}
```

Restart Claude Desktop after saving.

---

## Cursor

Open `Cursor Settings -> MCP -> Add new MCP Server`.

Use these values:

- **Name:** `youtube-music`
- **Type:** `Command`
- **Command:** `/Users/YOUR_NAME/Applications/YouTube Music MCP Bridge.app/Contents/Resources/youtube-music-mcp`

To verify, reopen the MCP settings and confirm the server appears in the list.

---

## Gemini CLI

Gemini CLI currently documents MCP server setup through `~/.gemini/settings.json`.

Open the file:

```bash
open ~/.gemini/settings.json
```

Add or merge this `mcpServers` entry:

```json
{
  "mcpServers": {
    "youtube-music": {
      "command": "/Users/YOUR_NAME/Applications/YouTube Music MCP Bridge.app/Contents/Resources/youtube-music-mcp"
    }
  }
}
```

To verify, start Gemini CLI and run:

```text
/mcp
```

---

## GitHub Copilot

Start Copilot in your terminal:

```bash
copilot
```

Inside the interactive session, run:

```text
/mcp add
```

When prompted, use:

- **Name:** `youtube-music`
- **Type:** Choose the local-process option
- **Command:** `/Users/YOUR_NAME/Applications/YouTube Music MCP Bridge.app/Contents/Resources/youtube-music-mcp`

To verify, run:

```text
/mcp list
```

---

## VS Code

If the `code` command is not available in your shell, run `Shell Command: Install 'code' command in PATH` from the VS Code Command Palette first.

Then add the server from your terminal:

```bash
code --add-mcp '{"name":"youtube-music","command":"/Users/YOUR_NAME/Applications/YouTube Music MCP Bridge.app/Contents/Resources/youtube-music-mcp"}'
```

After installation, open Copilot Chat in VS Code and switch to **Agent Mode** to use the MCP tools.

To verify, open the Command Palette and run `MCP: List Servers`.

---

## Windsurf

Open `Settings -> Cascade -> Model Context Protocol (MCP) Servers -> View raw config`.

Add or merge this `mcpServers` entry:

```json
{
  "mcpServers": {
    "youtube-music": {
      "command": "/Users/YOUR_NAME/Applications/YouTube Music MCP Bridge.app/Contents/Resources/youtube-music-mcp"
    }
  }
}
```

After saving, reopen the MCP settings and confirm the server is listed.

---

## Other MCP-compatible clients

If your client can register a local MCP server process, point it at the embedded binary:

```bash
~/Applications/YouTube\ Music\ MCP\ Bridge.app/Contents/Resources/youtube-music-mcp
```

### Environment Variables

| Variable | Default | Purpose |
|---|---|---|
| `YTM_APP_SERVER_URL` | `http://127.0.0.1:8765` | Bridge app URL |
| `YTM_BRIDGE_TOKEN` | (none) | Bearer token for bridge auth |
| `YTM_COMMAND_TIMEOUT_SECONDS` | `30` | Action timeout |
| `YTM_DEBUG_LOGS` | `false` | Enable debug logging |

---

## Verifying it works

1. Make sure the bridge app is running and visible in the menu bar.
2. Make sure Safari has `music.youtube.com` open with the extension enabled.
3. Check the bridge status from the menu bar icon. You should see `Connected: 1`.
4. Ask your client to play a song, for example: *"Play some lo-fi hip hop on YouTube Music."*
