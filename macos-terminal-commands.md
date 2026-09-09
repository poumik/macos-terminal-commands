# macOS Terminal Commands — Update / Maintain / Monitor

Quick reference for keeping macOS, apps, and packages up to date, maintaining the system, and monitoring its health.
Commands work in both `zsh` and `bash` unless noted. ⚠️ marks commands needing `sudo` or extra care.

---

## Table of Contents

1. [System Update & Maintenance](#1-system-update--maintenance)
   - [macOS Software Updates](#macos-software-updates)
   - [Homebrew (brew + casks)](#homebrew-brew--casks)
   - [Mac App Store (mas)](#mac-app-store-mas)
   - [Node.js / npm](#nodejs--npm)
   - [Python / pip](#python--pip)
2. [System Monitoring](#2-system-monitoring)
3. [Disk & Storage Maintenance](#3-disk--storage-maintenance)
4. [Network](#4-network)
5. [System Maintenance Utilities](#5-system-maintenance-utilities)
6. [Services & Processes](#6-services--processes)
7. [Quick Utilities & Clipboard](#7-quick-utilities--clipboard)
8. [Useful Aliases](#8-useful-aliases)

---

## 1. System Update & Maintenance

### macOS Software Updates

```bash
# List available macOS updates (no install)
softwareupdate --list

# List updates without a fresh network scan (reuse results of the last scan)
softwareupdate --list --no-scan

# Install all available updates
sudo softwareupdate --install --all          # ⚠️ may reboot

# Install a specific update (use the exact name from --list output)
sudo softwareupdate --install "macOS 15.7.1-24Hxxx"

# Install only recommended updates
sudo softwareupdate --install --recommended

# Download updates without installing
softwareupdate --download --all

# Install Rosetta 2 (needed for Intel apps on Apple Silicon)
softwareupdate --install-rosetta --agree-to-license

# Download the full macOS installer for a specific version
softwareupdate --fetch-full-installer --full-installer-version 15.7

# Trigger the GUI Software Update pane
open "x-apple.systempreferences:com.apple.preferences.softwareupdate"
```

### Homebrew (brew + casks)

```bash
# Update Homebrew itself + formula/cask definitions
brew update

# Show what's outdated (formulae and casks)
brew outdated
brew outdated --verbose          # show current vs. new versions
brew outdated --greedy           # include casks with auto_updates: true

# Upgrade everything — formulae + casks (auto-updating casks excluded unless --greedy)
brew upgrade
brew upgrade --greedy               # also upgrade casks with auto_updates: true

# Upgrade formulae only / casks only
brew upgrade --formula
brew upgrade --cask

# Upgrade a specific package
brew upgrade wget
brew upgrade --cask visual-studio-code

# Dry run — show what would be upgraded without doing it
brew upgrade --dry-run

# Remove stale lock files and old downloads, free disk space
brew cleanup
brew cleanup --prune=all         # remove ALL old versions/downloads
brew cleanup -s                  # clear download cache too

# Remove orphaned dependencies of uninstalled formulae
brew autoremove

# Health check — diagnose common problems
brew doctor

# Show install location / environment info
brew --prefix
brew config

# List installed packages
brew list                        # everything
brew list --formula              # CLI packages
brew list --cask                 # GUI apps
brew leaves                      # top-level packages (no dependents)

# Show info / dependencies for a package
brew info wget
brew deps wget

# Pin a formula so `brew upgrade` skips it
brew pin wget
brew unpin wget

# Search for packages
brew search neovim

# Install / uninstall
brew install wget
brew uninstall wget
brew uninstall --zap --cask app  # ⚠️ also removes app's config/data files

# Export installed packages to a Brewfile / restore from it
brew bundle dump                 # writes Brewfile in current dir
brew bundle dump --file=~/Brewfile
brew bundle install --file=~/Brewfile

# See what's using the most space
brew cleanup -n                  # dry-run: what cleanup would remove
```

### Mac App Store (mas)

> **What is mas?** `mas` (Mac App Store CLI) is a third-party open-source tool (https://github.com/mas-cli/mas) that lets you search, install, and upgrade App Store apps from the terminal. It uses the same private frameworks as the App Store app, so anything it installs is fully compatible with the GUI.
>
> **Install**: `brew install mas`
> **Requirements**: macOS with App Store; signing into the App Store GUI app is required for *installing* — `mas upgrade` works even when signed out (on recent macOS versions).

```bash
# List all apps installed from the App Store (ID, name, version)
mas list

# Search the App Store (shows app ID, name, price)
mas search xcode
mas search "pixelmator" --price    # include price info

# Show apps with available updates
mas outdated                      # lists ID, name, old -> new version

# Upgrade all App Store apps
mas upgrade

# Upgrade a specific app (use app ID from mas list/outdated)
mas upgrade 497799835             # Xcode

# Install an app by ID (must be signed in to App Store)
mas install 497799835
mas install 497799835 409203825   # multiple apps at once

# Get info about an app (version, developer, price)
mas info 497799835

# Open an app's App Store page (find ID without installing)
mas open 497799835                # opens App Store GUI to that app

# Look up the ID of an app by its bundle identifier
mas find com.apple.dt.Xcode

# Sign in / out (⚠️ interactive; sign-in is often flaky — use the App Store GUI instead)
mas signin "you@example.com"
mas signout

# Version of mas itself
mas version
```

> **Troubleshooting mas**
> - `mas signin` fails or hangs on newer macOS → sign in via the App Store GUI; mas picks it up.
> - `mas install` says "Not signed in" → open App Store, sign in, retry.
> - `mas upgrade` finds no apps but App Store shows updates → run `softwareupdate --list`; some Apple system apps update via softwareupdate, not mas.
> - After a macOS upgrade mas may error → `brew upgrade mas` to get a build for the new OS.

### Node.js / npm

```bash
# Update global npm packages to latest allowed by semver ranges
npm update -g

# Check which global packages are outdated
npm outdated -g

# Upgrade a global package to the newest version
npm install -g typescript@latest

# List global packages
npm list -g --depth=0

# Interactive upgrade tool (pick which globals to upgrade)
npx npm-check -u -g

# Update npm itself
npm install -g npm@latest
npm doctor                        # diagnose npm environment

# Clear npm cache if installs act up
npm cache clean --force

# --- nvm (Node version manager) ---
nvm ls-remote                     # list all available Node versions
nvm install --lts                 # install newest LTS
nvm install 22                    # install specific major version
nvm use 22
nvm alias default 22              # set default Node version
nvm ls                            # list installed versions
```

### Python / pip

```bash
# List installed packages
pip list
pip list --user                   # user-site packages only

# Show outdated packages
pip list --outdated

# Upgrade a single package
pip install --upgrade requests

# Upgrade all outdated packages (⚠️ review the list first)
pip list --outdated --format=freeze | cut -d= -f1 | xargs -n1 pip install -U

# Upgrade pip itself
pip install --upgrade pip

# Show package details
pip show requests

# Clean build/cache artifacts
pip cache purge

# --- pyenv (Python version manager) ---
pyenv install --list              # all available versions
pyenv install 3.13                # install a version
pyenv versions                    # installed versions
pyenv global 3.13                 # set default
pyenv local 3.13                  # set version for current directory
pyenv update                      # update pyenv itself (needs pyenv-update plugin)

# --- Homebrew-managed Python note ---
# `brew upgrade` also upgrades brew-installed Python versions.
# --formula is kept intentionally: plain `brew list | grep python` also
# matches casks, so this stays scoped to CLI formulae only.
brew list --formula | grep python
```

---

## 2. System Monitoring

```bash
# Live process monitor (built-in). Press 'o' for filter, 'q' to quit.
top                              # CPU sorted by default
top -o mem                       # sort by memory
top -o cpu -n 15                 # top 15 by CPU
top -stats pid,command,cpu,mem   # choose columns

# Better process viewer (brew install htop)
htop

# Snapshot of all processes with full paths
ps aux

# Top 10 processes by CPU / memory
ps aux | sort -nrk 3 | head -11   # by CPU %
ps aux | sort -nrk 4 | head -11   # by MEM %

# Memory pressure — check "free" RAM (look at page-ins/outs & swap)
memory_pressure                  # ⚠️ heavy stress test with -l all; plain run shows stats
vm_stat                          # VM statistics (page size = 16384 on Apple Silicon)
sysctl vm.swapusage              # swap usage summary

# System info
uname -a                          # kernel version
sw_vers                           # macOS version + build
sysctl -n machdep.cpu.brand_string        # CPU model
sysctl -n hw.memsize                      # RAM in bytes (÷ 1024³ for GB)
sysctl -n hw.ncpu                         # logical CPU count
system_profiler SPHardwareDataType        # full hardware overview
system_profiler SPSoftwareDataType        # OS overview

# Uptime & load average (compare load to CPU count)
uptime
sysctl -n hw.ncpu                 # load > ncpu for long = saturation

# Disk I/O (2-second intervals)
iostat 2

# Battery & power (laptops)
pmset -g batt                     # charge %, source, condition
pmset -g                          # all power settings

# Deep power/thermal analysis (⚠️ sudo; sample for 30s)
sudo powermetrics --samplers cpu_power,gpu_power -i 30000

# Thermal state: 0=nominal 1=fair 2=serious 3=critical
pmset -g therm

# Log stream — live system log (Ctrl-C to stop)
log stream                        # everything (floods quickly)
log stream --predicate 'processImagePath contains "kernel"' --info
log show --last 1h --predicate 'eventMessage contains "error"'  # past errors

# Recent system boot/shutdown/crash history
last reboot
last shutdown
ls -lt ~/Library/Logs/DiagnosticReports/ | head  # recent crash reports (user apps — most common)
ls -lt /Library/Logs/DiagnosticReports/ | head   # system-wide crash reports
```

---

## 3. Disk & Storage Maintenance

```bash
# Human-readable disk usage for all mounted volumes
df -h

# Check APFS container / volume layout
diskutil list
diskutil info /                   # details of the system volume

# Verify a volume's filesystem (safe, read-only)
diskutil verifyVolume /

# Repair a volume's filesystem (⚠️ sudo; usually unneeded on APFS — self-healing)
# Note: the live boot volume can't be repaired while booted from it —
# use Recovery Mode (or target a non-boot volume) if repair is really needed.
sudo diskutil repairVolume /

# Size of a directory (add -h for human-readable)
du -sh ~/Downloads
du -sh ~/Library/Caches/* | sort -h     # find biggest cache dirs

# Interactive disk usage explorer (brew install ncdu) — navigate + delete
ncdu ~

# Time Machine local snapshots (can eat space on APFS)
tmutil listlocalsnapshots /
sudo tmutil deletelocalsnapshots 2026-09-08-123456   # delete a specific one
tmutil thinlocalsnapshots / 999999999999 4           # thin snapshots to free space

# Purge RAM disk cache (rarely needed; macOS manages this)
sudo purge                        # ⚠️ brief slowdown after; mostly placebo on modern macOS

# Check free space including purgeable (System Settings may show more free)
df -h /
diskutil info / | grep -i 'free\|container'

# Flush DNS cache (also fits under Network)
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder

# Eject/unmount volumes safely
diskutil eject /Volumes/USBDrive
diskutil unmountDisk /dev/disk2   # ⚠️ unmounts ALL volumes on that disk

# Clear app caches (⚠️ apps must be closed; safe to delete, they regenerate)
rm -rf ~/Library/Caches/*         # ⚠️ review first — some apps store state here

# Empty Trash — safer, Finder-native way (handles per-user .Trashes/$UID/
# structure on external volumes correctly; a raw wildcard glob can hit
# permission errors or skip files there)
osascript -e 'tell application "Finder" to empty trash'

# Empty Trash from all volumes — raw filesystem wildcard
# ⚠️ irreversible, no confirmation, and the /Volumes/* glob hits EVERY
# mounted volume (external drives, network shares, disk images) —
# unmount anything you don't want touched first, or scope to one volume by name
sudo rm -rf ~/.Trash/* /Volumes/*/.Trashes/*
```

---

## 4. Network

```bash
# Show all network interfaces with status
networksetup -listallnetworkservices
ifconfig                          # full interface details
ifconfig en0 | grep ether         # MAC address of Wi-Fi/Ethernet

# Get current IP address (en0 = Wi-Fi on most Macs, en1 on desktops)
ipconfig getifaddr en0            # IPv4
ifconfig en0 | grep 'inet6 '      # IPv6

# External / public IP
curl -s ifconfig.me; echo

# Ping / traceroute / DNS lookup
ping -c 4 1.1.1.1                 # 4 packets then stop
traceroute example.com
dig example.com +short            # DNS lookup
dig @1.1.1.1 example.com          # query specific resolver
nslookup example.com

# Routing table / default gateway
netstat -rn | head -20
route get default

# Show open network connections + owning process (very useful)
lsof -i                          # all connections
lsof -i :3000                     # what's listening on port 3000
lsof -i -P | grep LISTEN         # all listening ports (no service-name translation)

# Flush DNS cache
sudo dscacheutil -flushcache && sudo killall -HUP mDNSResponder

# Wi-Fi diagnostics — wdutil is the reliable option on all modern Macs
sudo wdutil info                          # current Wi-Fi status/details
# legacy airport tool — removed on newer macOS releases; may not exist on your Mac
/System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport -I

# Wi-Fi on/off + forget network
networksetup -setairportpower en0 on
networksetup -setairportpower en0 off
networksetup -removepreferredwirelessnetwork en0 "NetworkName"

# Renew DHCP lease
sudo ipconfig set en0 BOOTP
sudo ipconfig set en0 DHCP

# Proxy / VPN related
scutil --proxy                    # show proxy settings

# Computer/sharing names
scutil --get ComputerName
scutil --get LocalHostName        # Bonjour name (name.local)
sudo scutil --set ComputerName "NewName"
sudo scutil --set LocalHostName "NewName"

# Ports scan of your own machine (see what's exposed)
lsof -i -P -n | grep -i tcp
```

---

## 5. System Maintenance Utilities

```bash
# Time Machine
tmutil status                     # current backup status
tmutil destinationinfo            # where backups go
tmutil startbackup                # trigger a backup now
tmutil stopbackup
tmutil latestbackup               # path of newest backup
tmutil listbackups                # all snapshots on destination

# Spotlight: reindex a volume (fixes broken search; takes a while)
sudo mdutil -E /                  # erase index and reindex
sudo mdutil -i on /               # enable indexing
sudo mdutil -s /                  # show indexing status

# Reset an app's state without reinstalling (⚠️ app-specific data lost)
defaults delete com.apple.Safari  # example

# View/change hidden system preferences (example: show hidden files)
defaults write com.apple.finder AppleShowAllFiles -bool true && killall Finder
defaults write com.apple.finder AppleShowAllFiles -bool false && killall Finder

# Restart UI processes without rebooting (safe; windows may close)
killall Dock                      # restart Dock
killall Finder                    # restart Finder
killall SystemUIServer            # restart menu bar extras

# Clear the print queue
cancel -a

# SMC reset (Apple Silicon): just shut down for 30s. Intel Macs: model-specific key combos.
# NVRAM reset (Intel): sudo nvram -c   ⚠️ clears boot settings
sudo nvram -c                     # ⚠️ Intel Macs only; reboots needed after

# Kernel extensions (legacy; check what's loaded)
kextstat | grep -v com.apple      # non-Apple kexts

# Open system panes from the CLI
open "x-apple.systempreferences:com.apple.preferences.softwareupdate"
open "x-apple.systempreferences:com.apple.preferences.network"

# Screenshots: change format/location
defaults write com.apple.screencapture type -string "png"
defaults write com.apple.screencapture location ~/Pictures/Screenshots
killall SystemUIServer

# Sleep / shutdown / restart from CLI
pmset sleepnow
sudo shutdown -r now              # ⚠️ restart
sudo shutdown -h now              # ⚠️ shutdown
```

---

## 6. Services & Processes

```bash
# List all running launchd services (label, PID, last exit status)
launchctl list
launchctl list | grep -v 'com.apple'    # non-Apple services

# Details of one service
launchctl print system/com.example.service
launchctl print gui/$(id -u)/com.example.service   # user-domain services

# Enable/disable services (⚠️ SIP may block modifying system services)
sudo launchctl disable system/com.apple.example.service
sudo launchctl enable system/com.apple.example.service

# Third-party services via Homebrew (preferred way to manage services)
brew services list                # all brew-managed services + status
brew services start postgresql@17
brew services stop postgresql@17
brew services restart postgresql@17
brew services run postgresql@17   # run once, not as service
brew services cleanup             # remove stale service files

# Start a GUI app from the CLI
open -a "Safari"
open -a "Visual Studio Code" .    # open project in VS Code
open ~/Downloads/file.pdf         # open file with default app
open -R ~/Downloads/file.pdf      # reveal in Finder

# Process management
pgrep -fl python                  # find processes by name, show command line
pkill -f "python script.py"       # kill by name/match
kill -TERM 12345                  # graceful stop (TERM is kill's default signal, so plain `kill 12345` does the same)
kill -9 12345                     # ⚠️ force kill — no cleanup, last resort
killall Safari                    # kill all processes named Safari (⚠️ unsaved data)

# Background/nice processes
nohup command &                   # run surviving terminal close
command &                         # run in background
jobs                              # list background jobs of this shell
fg %1                             # bring job 1 to foreground
renice -n 10 -p 12345             # lower priority of a running process
```

---

## 7. Quick Utilities & Clipboard

```bash
# Clipboard — pipe text in/out without leaving the terminal
cat file.txt | pbcopy             # copy a file's contents to the clipboard
pbpaste > newfile.txt             # paste clipboard contents into a file
pbpaste | grep "TODO"             # pipe clipboard contents into another command

# Spotlight search from the CLI — index-backed, much faster than `find` for broad searches
mdfind "quarterly report"         # search entire index by content/name
mdfind -onlyin ~/Documents "invoice"   # scope search to one folder

# Keep the Mac awake during a long-running task (no need to touch System Settings)
caffeinate -t 3600                # stay awake for 1 hour, then allow sleep again
caffeinate -i long_command        # stay awake only while long_command runs

# Quick Look preview from the terminal (same as pressing Space in Finder)
qlmanage -p image.png             # opens a Quick Look preview window

# Open the current directory in Finder
open .
```

---

## 8. Useful Aliases

Add to `~/.zshrc` (and/or `~/.bashrc`), then `source ~/.zshrc`:

```bash
# --- Update everything ---
alias update='softwareupdate --list; brew update; brew outdated; mas outdated; npm outdated -g'
alias upgrade='brew upgrade; brew upgrade --cask; mas upgrade; npm update -g'
alias update-all='brew update && brew upgrade && brew upgrade --cask && brew autoremove && brew cleanup && mas upgrade && npm update -g && softwareupdate --install --all'

# --- Maintenance ---
alias brewcheck='brew doctor && brew missing'
alias brewclean='brew cleanup --prune=all && brew autoremove'
alias flushdns='sudo dscacheutil -flushcache && sudo killall -HUP mDNSResponder'
alias showfiles='defaults write com.apple.finder AppleShowAllFiles -bool true && killall Finder'
alias hidefiles='defaults write com.apple.finder AppleShowAllFiles -bool false && killall Finder'

# --- Monitoring ---
alias myip='curl -s ifconfig.me; echo'
alias lsnet='lsof -i -P -n | grep LISTEN'
alias mem='vm_stat | head -5; sysctl vm.swapusage'
alias batt='pmset -g batt'
alias cpu='top -o cpu -n 10 -stats pid,command,cpu,mem'

# --- Shortcuts ---
alias ls='ls -GFh'                # colors, type suffixes, human sizes
alias ll='ls -la'
alias ..='cd ..'
alias ...='cd ../..'
```

---

## Suggested Weekly Routine

```bash
brew update && brew outdated && mas outdated      # check what needs updating
brew upgrade && mas upgrade                       # update apps
brew autoremove && brew cleanup --prune=all       # reclaim disk space
softwareupdate --list                             # check OS updates
brew doctor                                       # sanity check
df -h /                                           # keep an eye on disk space
```

---

## ⚠️ Safety Notes

- **Read before running**: `man <command>` shows the manual; e.g. `man softwareupdate`.
- **Sudo commands** can break the system when misused — double-check targets of `rm -rf`, `kill -9`, `diskutil repairVolume`.
- **Backups first**: Time Machine before OS upgrades (`tmutil startbackup`).
- **Flags vary by macOS version**: if a command errors, check `man <command>` for your version.