# Claude Status Bar

Forked from [m1ckc3s/claude-status-bar](https://github.com/m1ckc3s/claude-status-bar). The original is great. I forked it because the README listed VS Code and Cursor as unsupported, and I wasn't convinced that was actually true.

It wasn't. The Claude Code extension fires the exact same hooks via `~/.claude/settings.json`. The menu bar works fine in Cursor. I checked the extension source, confirmed sessions were being tracked, tested it live, and updated the docs. That's most of what changed here.

If you're running Claude Code inside Cursor or VS Code, this is the version you want.

<img width="1016" height="566" alt="for gif (1)" src="https://github.com/user-attachments/assets/55a7b294-e893-4f73-b16b-b8beef784400" />

<a href="https://github.com/m1ckc3s/claude-status-bar/releases/latest/download/ClaudeStatusBar.dmg"><img src="assets/download.png" alt="Download ClaudeStatusBar.dmg for macOS" width="260"></a>

Signed and notarized. Drag to Applications, launch once.

## What it shows

- **Thinking / working** — the Claude spark animates with a live `1m 1s` timer.
- **Running a tool** — a short label (`Editing`, `Reading`, `Running command`, `Using tool`, …).
- **Awaiting permission** — a paused yellow dot (CLI only, see below).
- **Idle / done** — rests on the Claude logo.

Two animation styles in the menu: **Claude** (the web morph spark) and **Claude Code** (the terminal glyph spinner). Icon color is **Orange** (Anthropic's `#d97757`) or **System** (black/white, matches your other menu bar icons). You can toggle the timer off too.

## Where it works

| Surface | Tracked? |
|---|---|
| Claude Code CLI (terminal) | ✅ |
| Claude Code Desktop — Code tab | ✅ |
| VS Code / Cursor extension | ✅ |
| Claude Desktop — Chat tab | ❌ |
| Cowork | ❌ |
| JetBrains extension | ❓ untested |

Chat and Cowork don't go through the hook system, so those won't work regardless of what fork you're on. The VS Code and Cursor extensions hook into `~/.claude/settings.json` the same way the CLI does, which is why they work and why the original README was wrong to list them as unsupported. JetBrains is probably fine too, I just haven't run it.

### One thing about permissions

The yellow dot only shows in the CLI. The desktop app handles permission prompts inside its own window and doesn't fire the notification hook, so you'll see whatever tool was running last. If you're on auto/bypass mode this never comes up anyway.

## Requirements

- macOS 12+
- [Claude Code](https://claude.com/claude-code) installed
- Node.js

## Install

### Option A — DMG

1. Download `ClaudeStatusBar.dmg` from [Releases](../../releases).
2. Drag **Claude Status Bar** into Applications.
3. Launch it once. It wires the hooks automatically.
4. Start a Claude session. The spark appears.

No Gatekeeper warning — it's signed and notarized.

### Option B — Claude Code plugin

```
/plugin marketplace add m1ckc3s/claude-status-bar
/plugin install claude-status-bar@claude-status-bar
```

This gets the hooks in place but not the app itself, so you still need to drag it into Applications from the DMG first. Once that's done the plugin handles launching it.

### Using the Claude Code desktop app

The desktop app has its own menu bar icon (the screenshot shortcut one). If two Claude icons next to each other bothers you, go to **Settings → General** and turn the built-in one off.

## How it works

Claude Code hooks fire on lifecycle and tool events. Small Node.js scripts write the current status to `~/.claude/statusbar/state.json`. The menu bar app polls that file and updates the icon and label. Each active session gets its own file under `~/.claude/statusbar/sessions.d/` — as long as any of those exist, the app stays up. When there's nothing left to track, it quits itself.

The installer merges its hooks into `~/.claude/settings.json` and backs the file up first (`settings.json.bak-statusbar`), so your existing hooks stay intact.

## Uninstall

```bash
node "/Applications/ClaudeStatusBar.app/Contents/Resources/uninstall.js"
```

Then drag the app to the Trash. Run the script first — dragging to Trash alone leaves the hooks behind.

## Build from source

```bash
git clone https://github.com/ManiForoughi2/claude-status-bar
cd claude-status-bar
./build.sh            # builds build/ClaudeStatusBar.app
./build.sh --dmg      # also produces build/ClaudeStatusBar.dmg
```

Requires Xcode Command Line Tools (`xcode-select --install`).

## Troubleshooting

**The icon appeared, then disappeared.** That's intentional. The app exits when no Claude session is active. Open Claude Code or start a `claude` session and it'll stay up. You don't launch it yourself — the session does.

**Nothing shows up at all.**
- Start a fresh Claude session. Sessions already running before install get picked up eventually, but a new session is more reliable.
- Check if it's running: `pgrep -x ClaudeStatusBar`. A number means it's running but probably hidden. No output means it exited.
- If the first-launch setup didn't take: `node "/Applications/ClaudeStatusBar.app/Contents/Resources/install.js"`

**Running but not visible.** Notch Mac with a crowded menu bar. Use Ice or Bartender to surface it.

## Not affiliated with Anthropic

This is an unofficial fork of [m1ckc3s/claude-status-bar](https://github.com/m1ckc3s/claude-status-bar), which is itself unofficial. Neither is affiliated with Anthropic. "Claude" is a trademark of Anthropic. If there's a trademark issue, open an issue and I'll sort it out.

## License

MIT
