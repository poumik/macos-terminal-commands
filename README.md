# macOS Terminal Commands Guide

A practical, version-agnostic quick reference for **managing macOS** — the system, apps, and development packages — all from the terminal.

The full guide lives in [`macos-terminal-commands.md`](macos-terminal-commands.md).

## What's Inside

| Section | Covers |
|---------|--------|
| **System Update & Maintenance** | `softwareupdate`, Homebrew (formulae + casks), Mac App Store (`mas`), npm, pip, nvm, pyenv |
| **System Monitoring** | `top`/`htop`, `ps`, memory pressure, swap, CPU/battery/power, `log stream`, crash reports |
| **Disk & Storage Maintenance** | `df`/`du`/`ncdu`, `diskutil`, APFS snapshots, cache cleanup |
| **Network** | interfaces, IPs, DNS, open ports (`lsof -i`), Wi-Fi diagnostics, proxies, `networkQuality`, `nettop` |
| **Security & Gatekeeper** | quarantine removal (`xattr`), Gatekeeper checks (`spctl`), SIP status (`csrutil`) |
| **System Maintenance Utilities** | Time Machine (`tmutil`), Spotlight reindexing (`mdutil`), `defaults`, Dock/Finder restarts, `sysdiagnose` |
| **Services & Processes** | `launchctl`, `brew services`, process management, background jobs |
| **Miscellaneous Utilities** | `pbcopy`/`pbpaste`, `mdfind`, `caffeinate`, Quick Look (`qlmanage`), `open .` |
| **Useful Aliases** | copy-paste `.zshrc`/`.bashrc` aliases incl. one-shot `update-all` |

Plus a suggested **weekly maintenance routine** and **explicit safety notes** for `sudo` and destructive commands.

## Prerequisites

The core commands (`softwareupdate`, `diskutil`, `top`, `tmutil`, …) work on any stock macOS.

Optional tools that unlock more of the guide — all via [Homebrew](https://brew.sh):

```bash
brew install mas     # Mac App Store CLI (updates/upgrades for App Store apps)
brew install htop    # better interactive process viewer
brew install ncdu    # interactive disk usage explorer
```

## Quick Start

```bash
# Check what's outdated everywhere
softwareupdate --list
brew outdated
mas outdated

# One-shot full update (see Aliases section for the update-all alias)
# --recommended: skips major OS upgrades that --all would pull in
brew update && brew upgrade && mas upgrade && softwareupdate --install --recommended
```

## Conventions

- Commands work in both `zsh` (default) and `bash` unless noted.
- ⚠️ marks commands requiring `sudo` or extra care.
- Safety scale: unmarked = read-only/diagnostic; ⚠️ = modifies state or needs care; explicit destructive/irreversible warnings = deletes data.
- Before any `sudo` or destructive command, verify the target and make sure you have a backup.
- If a flag errors on your macOS release, check `man <command>`.

## Contributing

Spot a missing command, a better flag, or an error? Open an issue or pull request — keep additions version-agnostic and follow the existing `command   # explanation` style.

## ⚠️ Disclaimer

This guide includes destructive commands — some can remove caches, reset network or boot settings, or delete data from mounted volumes. Commands marked ⚠️ can modify system settings, delete data, or alter your Mac's state. **Always back up important data, verify commands before running them, and check `man <command>` for your macOS release.** While these commands are tested, the authors cannot be held responsible for unintended outcomes.

## License

This project is licensed under the [MIT License](LICENSE.txt).
