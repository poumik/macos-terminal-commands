# macOS Terminal Commands Guide

A practical, version-agnostic quick reference for **updating, maintaining, and monitoring macOS** — the system, apps, and development packages — all from the terminal.

The full guide lives in [`macos-terminal-commands.md`](macos-terminal-commands.md).

## What's Inside

| Section | Covers |
|---------|--------|
| **System Update & Maintenance** | `softwareupdate`, Homebrew (formulae + casks), Mac App Store (`mas`), npm, pip, nvm, pyenv |
| **System Monitoring** | `top`/`htop`, `ps`, memory pressure, swap, CPU/battery/power, `log stream`, crash reports |
| **Disk & Storage Maintenance** | `df`/`du`/`ncdu`, `diskutil`, APFS snapshots, cache cleanup |
| **Network** | interfaces, IPs, DNS, open ports (`lsof -i`), Wi-Fi diagnostics, proxies |
| **System Maintenance Utilities** | Time Machine (`tmutil`), Spotlight reindexing (`mdutil`), `defaults`, Dock/Finder restarts |
| **Services & Processes** | `launchctl`, `brew services`, process management, background jobs |
| **Useful Aliases** | copy-paste `.zshrc`/`.bashrc` aliases incl. one-shot `update-all` |

Plus a suggested **weekly maintenance routine** and **safety notes** for `sudo`/destructive commands.

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
brew update && brew upgrade && mas upgrade && softwareupdate --install --all
```

## Conventions

- Commands work in both `zsh` (default) and `bash` unless noted.
- ⚠️ marks commands requiring `sudo` or extra care.
- Version-agnostic: if a flag errors on your macOS release, check `man <command>`.

## Contributing

Found a missing command, a better flag, or an error? Open an issue or a pull request — keep additions version-agnostic and follow the existing `command   # explanation` style.

## ⚠️ Disclaimer

This guide is provided for informational purposes only. Commands — especially those marked ⚠️ — can modify system settings, delete data, or otherwise alter your Mac's state. **Always understand a command before running it, back up important data, and verify against `man <command>` on your macOS release.** The authors are not responsible for any data loss, system damage, or other issues arising from the use of these commands. Use at your own risk.

## License

This project is licensed under the [MIT License](LICENSE).