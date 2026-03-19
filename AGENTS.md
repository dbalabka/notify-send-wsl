# Repository Guidelines

## Project Structure & Module Organization
This repository is intentionally small and script-based:

- `notify-send`: Bash entrypoint used inside WSL.
- `toast.ps1`: PowerShell implementation that builds and shows the Windows toast.
- `CLAUDE.md`: Maintainer notes and architecture summary.

Execution flow is: WSL shell -> `notify-send` -> `powershell.exe` -> `toast.ps1` -> Windows toast API.  
Keep cross-file assumptions aligned, especially the hardcoded path `C:\bin\toast.ps1`.

## Build, Test, and Development Commands
There is no build step or package manager. Use direct script execution:

- `chmod +x notify-send` - ensure executable bit is set.
- `./notify-send "Test Title" "Test body"` - send a test notification from WSL.
- `bash -n notify-send` - syntax check the Bash wrapper.
- `powershell.exe -NoProfile -Command "& { . C:\bin\toast.ps1; Show-Notification -ToastTitle 'Ping' -ToastText 'Smoke test' }"` - validate PowerShell path and notification function.

## Coding Style & Naming Conventions
- Bash: keep quoting strict (`"$var"`), avoid unescaped interpolation, and prefer simple one-purpose commands.
- PowerShell: keep function names in `Verb-Noun` form (for example `Show-Notification`) and use 4-space indentation.
- Filenames: use lowercase with dashes for shell scripts (`notify-send`) and `.ps1` for PowerShell scripts.
- Keep scripts dependency-light and self-contained.

## Testing Guidelines
No automated test suite exists today. Validate changes with manual smoke tests on a WSL2 + Windows host:

1. Run `bash -n notify-send`.
2. Trigger `./notify-send "Title" "Body"`.
3. Confirm a Windows toast appears with expected title/body.

When changing escaping or argument handling, test strings containing spaces, quotes, and punctuation.

## Commit & Pull Request Guidelines
Git metadata is not available in this directory snapshot, so follow standard conventions:

- Commits: imperative subject, concise scope (example: `fix quoting for toast body`).
- PRs: include purpose, behavior change summary, and manual test evidence (command run + observed result).
- Link related issues and call out any Windows/WSL environment assumptions.
