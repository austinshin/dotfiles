# dotfiles

Personal config files, version-controlled and symlinked into place on each machine.

This README is written so that a human **or an AI agent** (e.g. Claude Code) can set up a
fresh machine quickly: every tracked config lists exactly where it installs to and the
command to install it. See [CLAUDE.md](CLAUDE.md) for the rule that keeps this current.

## Tracked configs

| Config | Source in repo | Installs to |
|---|---|---|
| Claude Code | `claude/settings.json` | all OSes: `~/.claude/settings.json` |
| Alacritty | `alacritty/alacritty.toml` | Windows: `%APPDATA%\alacritty\alacritty.toml`<br>macOS/Linux: `~/.config/alacritty/alacritty.toml` |

## Prerequisites

- **git**
- **Windows only:** enable symlink creation without admin — **Settings → System → For developers → Developer Mode → On** (or run the install commands from an Administrator PowerShell).
- macOS/Linux: no special step; `ln -s` works out of the box.

## Quick start on a new machine

```powershell
# 1. Clone
git clone https://github.com/austinshin/dotfiles.git "$env:USERPROFILE\code\dotfiles"
```

Then run the install block for each config below. Each one **replaces the live file with a
symlink** into this repo (backing the original up to `*.bak` first), so future edits sync
through git.

### Claude Code → `~/.claude/settings.json`

```powershell
$t = "$env:USERPROFILE\code\dotfiles\claude\settings.json"
$l = "$env:USERPROFILE\.claude\settings.json"
New-Item -ItemType Directory -Force (Split-Path $l) | Out-Null
if (Test-Path $l) { Copy-Item $l "$l.bak" -Force; Remove-Item $l -Force }
New-Item -ItemType SymbolicLink -Path $l -Target $t
```
```bash
# macOS/Linux
ln -sf ~/code/dotfiles/claude/settings.json ~/.claude/settings.json
```
After installing: restart Claude Code (or run `/reload-plugins`). Enabled plugins download
automatically. MCP-backed plugins (`github`, `playwright`, `context7`) prompt for their own
auth on first use, per machine.

### Alacritty → `%APPDATA%\alacritty\alacritty.toml`

```powershell
$t = "$env:USERPROFILE\code\dotfiles\alacritty\alacritty.toml"
$l = "$env:APPDATA\alacritty\alacritty.toml"
New-Item -ItemType Directory -Force (Split-Path $l) | Out-Null
if (Test-Path $l) { Copy-Item $l "$l.bak" -Force; Remove-Item $l -Force }
New-Item -ItemType SymbolicLink -Path $l -Target $t
```
```bash
# macOS/Linux
mkdir -p ~/.config/alacritty
ln -sf ~/code/dotfiles/alacritty/alacritty.toml ~/.config/alacritty/alacritty.toml
```
**Machine-specific caveat:** `alacritty.toml` hardcodes `working_directory = 'C:\Users\shina\code'`.
On a machine with a different username or layout, edit that line after installing.

## Making changes (propagating to your other machines)

Because the live files are symlinks into this repo, editing a config edits the repo copy.
To share the change:

```powershell
git -C "$env:USERPROFILE\code\dotfiles" add -A
git -C "$env:USERPROFILE\code\dotfiles" commit -m "Update <config>"
git -C "$env:USERPROFILE\code\dotfiles" push
```

On another machine, `git pull` — the symlink reflects the new content automatically.

## Secrets

Never commit credentials or tokens. `.gitignore` already excludes Claude's
`.credentials.json` and auth-cache files. When adding a config that may hold secrets,
extend `.gitignore` before committing.
