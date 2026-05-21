# dotfiles — rules for Claude / AI agents

This repo stores personal config files and symlinks them into place on each machine. The
goal is that a fresh PC or developer environment can be set up **quickly and without prior
context** by reading `README.md`.

## Rule: every config must be installable from the README

Whenever you **add a new config** to this repo, or **change where an existing one lives**,
you MUST update `README.md` in the same change so a future developer or AI agent can install
it on a fresh machine. For each config the README must state:

1. **Source path** in this repo (e.g. `alacritty/alacritty.toml`).
2. **Target location** it installs to — give the per-OS path if it differs (Windows
   `%APPDATA%`/`%USERPROFILE%`, macOS/Linux `~/...`).
3. **Install command** — the symlink command, noting the Windows Developer Mode requirement.
4. **Post-install step or caveat** — e.g. "restart the app", or a machine-specific value
   baked into the file (hardcoded paths, usernames).

Keep the **Tracked configs** table and the per-config install sections in sync with what is
actually committed. If you add `foo/bar.conf`, it must appear in both.

## How configs are tracked

- Each live config file is a **symlink** into this repo, so editing the live file edits the
  repo copy.
- When importing a config, **copy the real file in byte-for-byte** (`Copy-Item` / `cp`) — do
  not retype it. Escape sequences (such as the ESC byte, 0x1B, used in Alacritty
  keybindings) get mangled when retyped through text editors. Verify the copy with a hash
  check (`Get-FileHash`).
- When creating the symlink, back the original up to `*.bak` first and restore it if symlink
  creation fails (Windows needs Developer Mode or admin).

## Secrets

Never commit credentials or auth tokens. `.gitignore` excludes Claude Code's
`.credentials.json` and auth-cache files. Extend `.gitignore` before committing any config
that could contain secrets.
