# dotfiles

Personal config synced across machines. Currently tracks Claude Code and Alacritty settings.

## What's here

```
claude/settings.json       # Claude Code config: theme, update channel, enabled plugins
alacritty/alacritty.toml   # Alacritty terminal config
.gitignore                 # excludes credentials + auth-cache files (never commit secrets)
```

## Set up on a new machine

1. Clone the repo:
   ```powershell
   git clone https://github.com/austinshin/dotfiles.git "$env:USERPROFILE\code\dotfiles"
   ```
2. Enable symlinks (one-time): **Settings → System → For developers → Developer Mode → On**
   (or run the symlink step from an Administrator PowerShell instead).
3. Symlink the live config files to the tracked copies:
   ```powershell
   # Claude Code
   $t="$env:USERPROFILE\code\dotfiles\claude\settings.json"
   $l="$env:USERPROFILE\.claude\settings.json"
   if (Test-Path $l) { Copy-Item $l "$l.bak" -Force; Remove-Item $l -Force }
   New-Item -ItemType SymbolicLink -Path $l -Target $t

   # Alacritty
   $t="$env:USERPROFILE\code\dotfiles\alacritty\alacritty.toml"
   $l="$env:APPDATA\alacritty\alacritty.toml"
   New-Item -ItemType Directory -Force (Split-Path $l) | Out-Null
   if (Test-Path $l) { Copy-Item $l "$l.bak" -Force; Remove-Item $l -Force }
   New-Item -ItemType SymbolicLink -Path $l -Target $t
   ```
4. Restart Claude Code (or run `/reload-plugins`). Enabled plugins download automatically.

> MCP-backed plugins (`github`, `playwright`, `context7`) prompt for their own auth on first use per machine.

## Making changes

`~/.claude/settings.json` is a symlink into this repo, so editing your Claude settings
edits `claude/settings.json` here directly. To propagate to your other machines:

```powershell
git -C "$env:USERPROFILE\code\dotfiles" add -A
git -C "$env:USERPROFILE\code\dotfiles" commit -m "Update Claude settings"
git -C "$env:USERPROFILE\code\dotfiles" push
```

On the other machine, `git pull` — the symlink reflects the new content automatically.

## Secrets

Never commit `~/.claude/.credentials.json` or auth-cache files. The `.gitignore`
already excludes them; keep it that way.
