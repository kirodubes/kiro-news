# Changelog

## 2026.06.13

### What Changed
- New package `kiro-news`: an in-system, DE-agnostic news notifier for Kiro.
  Born from the archlinux.org "Active AUR malicious packages" incident — a way
  to surface upstream Arch news *and* Kiro's own announcements to people running
  Kiro, without any server.
- **Arch news** source is live (RSS, HTTPS, display-only).
- **Kiro feed** source is scaffolded but **inert**: it is a JSON feed that must
  be minisign-signed and is verified against a shipped public key before
  display. It stays skipped until the keypair is generated and the feed is
  published. This authenticity step is deliberate — auto-displaying remote
  "Kiro says ..." messages unsigned would recreate the supply-chain hole that
  motivated the project.

- Added a **`kiro-news show`** command and a menu launcher: toasts are
  ephemeral, so `show` opens a readable HTML page of recent items from every
  source in the browser (consistent with the Kiro URL launchers, which also
  `xdg-open`). Shipped as `/usr/share/applications/kiro-news.desktop` with
  `Categories=X-Kiro;` so it lands in the XFCE **Kiro** submenu next to the Kiro
  link launchers. `Icon=rss` (recognizable; resolves across all shipped themes).

### Technical Details
- One client (`/usr/bin/kiro-news`) reads `/etc/kiro-news/sources.conf`
  (`id|type|url|name|verify`), polls each source, and notifies on the newest
  unseen item. Per-source, per-user read-state under `~/.cache/kiro-news`.
- Source types: `rss` (grep/sed parse, reused from the session prototype) and
  `json` (jq). `verify=signed` runs `minisign -V` against
  `/usr/share/kiro-news/kiro.pub`; missing key or signature → source skipped.
- Delivered on a systemd **user** timer (`OnUnitActiveSec=1h`,
  `RandomizedDelaySec=30min`) auto-enabled via an
  `etc/systemd/user/timers.target.wants/` symlink (the kiro-pci-latency pattern).
- One unreachable/malformed source never aborts the run.
- Reaches existing installs via package update (no reinstall): once in
  nemesis_repo, `kiro-system-files` will depend on `kiro-news`.

### Verified
- Trial-built from the local repo (`git+file://`) and installed with `pacman -U`
  on the hq box. The `informant` pre-transaction hook **blocked the first
  install** over 10 unread Arch news items — live proof the upgrade gate works;
  cleared with `informant read`, install then succeeded.
- Timer auto-enabled via the shipped symlink (`is-enabled` → enabled); service
  ran `Result=success`; desktop toast fired and was confirmed on screen. The
  `edu-` prototype timer was disabled to avoid double toasts.

### Files Modified
- `usr/bin/kiro-news` (added `show` command + multi-item parsers + HTML view)
- `usr/share/applications/kiro-news.desktop` (menu launcher, `Categories=X-Kiro`, `Icon=rss`)
- `etc/kiro-news/sources.conf`
- `usr/lib/systemd/user/kiro-news.service`, `kiro-news.timer`
- `etc/systemd/user/timers.target.wants/kiro-news.timer` (symlink)
- `usr/share/kiro-news/README.pubkey` (placeholder until `kiro.pub` exists)
- `README.md`, `LICENSE`, `.gitignore`
- Packaging: `KIRO-PKG-BUILD-APPS/kiro-news/{PKGBUILD,build.sh,up.sh,.gitignore}`
