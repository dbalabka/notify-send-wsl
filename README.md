# wsl-notify

`wsl-notify` provides a `notify-send`-style command for WSL that shows Windows toast notifications through `powershell.exe`.

## Requirements

- WSL on Windows
- `powershell.exe` available in PATH

## Install

```bash
chmod +x notify-send-wsl
mkdir -p ~/bin
cp notify-send-wsl ~/bin/
```

Ensure `~/bin` is in your PATH, then run:

```bash
notify-send-wsl "Installed" "Ready to use"
```

Optional alias for Linux-style calls:

```bash
alias notify-send=notify-send-wsl
```

## Usage

```bash
notify-send-wsl [OPTIONS] <summary> [body]
```

### Supported options

- `-u, --urgency=LEVEL` (`low`, `normal`, `critical`)
- `-t, --expire-time=TIME_MS`
- `-i, --icon=ICON`
- `-a, --app-name=APP_NAME`
- `-c, --category=TYPE[,TYPE...]`
- `-h, --hint=TYPE:NAME:VALUE` (accepted as metadata text)
- `--agent=auto|codex|claude`
- `-?, --help`

### Urgency mapping

- `low` -> `ToastNotification.Priority = Default`
- `normal` -> `ToastNotification.Priority = Default`
- `critical` -> `ToastNotification.Priority = High`

## Examples

Basic:

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

From Codex JSON payload:

```bash
notify-send-wsl --agent=codex '{"title":"Codex task done","message":"Implemented feature"}'
```

Codex official event payload compatibility:
- `type` must be `agent-turn-complete` (other event types are ignored)
- title uses `Codex: <last-assistant-message>` (fallback: `Turn Complete!`)
- body joins `input-messages` with spaces
- group/category uses `codex-<thread-id>`

From Claude JSON payload (stdin):

```bash
cat claude-event.json | notify-send-wsl --agent=claude -
```

Raw JSON string mode:

```bash
notify-send-wsl --agent=auto '{"title":"Done","message":"Task finished"}' 'ignored'
```

When `--agent=...` is used, the first positional argument is treated as JSON input (`-` for stdin or raw JSON string), and the second positional argument is ignored.

## Exit codes

- `0`: success
- `1`: usage/validation error
- `2`: PowerShell execution failure
- `3`: unsupported option

## Reference

- Microsoft docs (adaptive/interactive toasts):  
  <https://learn.microsoft.com/en-us/windows/apps/develop/notifications/app-notifications/adaptive-interactive-toasts?tabs=appsdk>
