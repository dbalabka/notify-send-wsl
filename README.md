# notify-send-wsl

Send native Windows toast notifications directly from WSL.

`notify-send-wsl` is a small dependency-light CLI that runs inside WSL and forwards notifications through `powershell.exe`. It works well as a lightweight `notify-send` replacement for shell scripts, local tooling, and agent hooks.

## ✨ Quick start

Run it once with `npx`:

```bash
npx @dbalabka/notify-send-wsl "Hello from WSL" "It works"
```

## 💡 Why use it

- Native Windows toast notifications from WSL
- No setup beyond WSL and `powershell.exe`
- Works well for scripts, CI helpers, Codex, and Claude hooks

## 📋 Requirements

- WSL on Windows
- `powershell.exe` available in `PATH`

## 📦 Installation

### `npx`

```bash
npx @dbalabka/notify-send-wsl "Installed" "Ready to use"
```

### Global npm install

```bash
npm install -g @dbalabka/notify-send-wsl
notify-send-wsl "Installed" "Ready to use"
```

Optional alias for Linux-style calls:

```bash
alias notify-send=notify-send-wsl
```

## 🚀 Usage

```bash
notify-send-wsl [OPTIONS] <summary> [body]
```

## 🛠 Supported options

- `-u, --urgency=LEVEL` with `low`, `normal`, or `critical`
- `-t, --expire-time=TIME_MS`
- `-i, --icon=ICON`
- `-a, --app-name=APP_NAME`
- `-c, --category=TYPE[,TYPE...]`
- `-h, --hint=TYPE:NAME:VALUE`
- `--agent=auto|codex|claude`
- `-?, --help`

## 🧪 Examples

Basic notification:

```bash
notify-send-wsl "Build complete" "All checks passed"
```

Critical notification:

```bash
notify-send-wsl -u critical "Build failed" "Check CI logs"
```

App logo image:

```bash
notify-send-wsl \
  -i /mnt/c/Windows/Web/Screen/img100.jpg \
  "Build complete" \
  "All checks passed"
```

## 🤖 Agent integration

From Codex JSON payload:

```bash
notify-send-wsl --agent=codex '{"title":"Codex task done","message":"Implemented feature"}'
```

Codex config:

```toml
notify = ["npx", "@dbalabka/notify-send-wsl", "--agent", "codex"]
```

Codex event compatibility:

- `type` must be `agent-turn-complete`; other event types are ignored
- title becomes `Codex: <last-assistant-message>` with fallback `Turn Complete!`
- body is derived from `input-messages`, then common body-like fields
- category/group becomes `codex-<thread-id>` when available

From Claude JSON payload on stdin:

```bash
cat claude-event.json | notify-send-wsl --agent=claude -
```

Claude config:

```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "npx @dbalabka/notify-send-wsl --agent claude"
          }
        ]
      }
    ]
  }
}
```

Raw JSON string mode:

```bash
notify-send-wsl --agent=auto '{"title":"Done","message":"Task finished"}' 'ignored'
```

When `--agent=...` is used, the first positional argument is treated as JSON input. Use `-` to read JSON from stdin. Any second positional argument is ignored.

## 🔢 Exit codes

- `0` success
- `1` usage or validation error
- `2` PowerShell execution failure
- `3` unsupported option

## 📦 Publishing

This repository is set up to publish the executable as the public npm package `@dbalabka/notify-send-wsl`.

Dry run:

```bash
npm pack --dry-run
```

Publish:

```bash
npm publish
```

## 📚 Reference

- Microsoft docs: <https://learn.microsoft.com/en-us/windows/apps/develop/notifications/app-notifications/adaptive-interactive-toasts?tabs=appsdk>
