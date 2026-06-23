# Claude Status Bar

A macOS menu bar app that shows Claude Code's live status: animated spark while thinking, a label while running a tool, and an elapsed timer.

Forked from [m1ckc3s/claude-status-bar](https://github.com/m1ckc3s/claude-status-bar). The original marked VS Code and Cursor as unsupported. After checking the extension source and confirming the hooks fire identically via `~/.claude/settings.json`, this fork adds confirmed support for both.

<img width="1016" height="566" alt="for gif (1)" src="https://github.com/user-attachments/assets/55a7b294-e893-4f73-b16b-b8beef784400" />

<a href="https://github.com/m1ckc3s/claude-status-bar/releases/latest/download/ClaudeStatusBar.dmg"><img src="assets/download.png" alt="Download ClaudeStatusBar.dmg for macOS" width="260"></a>

## What it shows

- **Thinking / working** — Claude spark animates with a live `1m 1s` timer.
- **Running a tool** — short label (`Editing`, `Reading`, `Running command`, `Using tool`, …).
- **Awaiting permission** — paused yellow dot (CLI only).
- **Idle / done** — rests on the Claude logo.

Two animation styles: **Claude** (web morph spark) and **Claude Code** (terminal glyph spinner). Icon color: **Orange** (`#d97757`) or **System** (black/white). Timer can be toggled off.

## Where it works

| Surface | Tracked? |
|---|---|
| Claude Code CLI (terminal) | ✅ |
| Claude Code Desktop — Code tab | ✅ |
| **VS Code / Cursor extension** | ✅ *(confirmed in this fork)* |
| Claude Desktop — Chat tab | ❌ |
| Cowork | ❌ |
| JetBrains extension | ❓ untested |

Chat and Cowork don't use the Claude Code hook system. VS Code and Cursor hook into `~/.claude/settings.json` the same way the CLI does.

### Permissions

The yellow dot only fires in the CLI. The desktop app handles permission prompts in-window and doesn't emit the notification hook.

## Requirements

- macOS 12+
- [Claude Code](https://claude.com/claude-code)
- Node.js

## Install

### Option A — DMG

1. Download `ClaudeStatusBar.dmg` from [Releases](../../releases).
2. Drag **Claude Status Bar** into Applications.
3. Launch it once to wire the hooks automatically.
4. Start a Claude Code session.

Signed and notarized.

### Option B — Claude Code plugin

```
/plugin marketplace add m1ckc3s/claude-status-bar
/plugin install claude-status-bar@claude-status-bar
```

Installs the hooks but not the app — drag it into Applications from the DMG first.

### Claude Code desktop app

The desktop app has its own menu bar icon. To avoid duplicates, go to **Settings → General** and disable the built-in one.

## How it works

Claude Code hooks fire on lifecycle and tool events. Node.js scripts write the current status to `~/.claude/statusbar/state.json`. The menu bar app polls that file and renders the icon and label. Each active session is tracked as a file under `~/.claude/statusbar/sessions.d/`. The app quits itself when no sessions remain.

The installer merges hooks into `~/.claude/settings.json` without modifying existing hooks, and backs the file up as `settings.json.bak-statusbar`.

## Uninstall

```bash
node "/Applications/ClaudeStatusBar.app/Contents/Resources/uninstall.js"
```

Then drag the app to Trash. Run the script first — dragging to Trash alone leaves the hooks in place.

## Build from source

```bash
git clone https://github.com/ManiForoughi2/claude-status-bar
cd claude-status-bar
./build.sh            # builds build/ClaudeStatusBar.app
./build.sh --dmg      # also produces build/ClaudeStatusBar.dmg
```

Requires Xcode Command Line Tools (`xcode-select --install`).

## Troubleshooting

**Icon appeared then disappeared.** The app exits when no Claude session is active and launches automatically when one starts.

**Nothing shows up.**
- Start a fresh Claude Code session.
- Check if the app is running: `pgrep -x ClaudeStatusBar`.
- If hooks weren't set up on first launch: `node "/Applications/ClaudeStatusBar.app/Contents/Resources/install.js"`

**Running but not visible.** Menu bar is full. Use Ice or Bartender to expose hidden items.

## Not affiliated with Anthropic

Unofficial fork of [m1ckc3s/claude-status-bar](https://github.com/m1ckc3s/claude-status-bar). Not affiliated with Anthropic. "Claude" is a trademark of Anthropic.

## License

MIT
