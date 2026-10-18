# Changelog

## 2026.10.18

### What Changed
- New Kiro News item: **Qtile users: do not upgrade python-xcffib to 1.7.0**. Arch's python-xcffib 1.7.0-1
  (2026-10-15) breaks cairocffi, so Qtile dies at startup. The item tells users to hold the package back, or to
  go back to 1.6.2 from the Arch archive with an `IgnorePkg` line, and to drop that line once Arch ships 1.7.1+.
  It goes with Erik's warning video; Kiro does not hold the package itself.

### Technical Details
- `usr/share/kiro-news/feed.json`: item `2026-10-18-qtile-python-xcffib-1-7-0` at the top, linking the qtile
  (#6075) and Arch (python-xcffib work item 2) bug reports. The 1.6.2 downgrade was verified on a kiro-next VM.

### Files Modified
- `usr/share/kiro-news/feed.json`

## 2026.10.17

### What Changed
- New Kiro News item: **KiroTux Niri Noctalia v26.10.17 is out**, the fourth KiroTux edition (Niri + Noctalia,
  Kirotux Niri Premium), with the Ko-fi link and the edition's website page.
  The item first said v26.10.16; the released ISO is v26.10.17, so title and summary were corrected.
- **Per-user local feeds.** A source path may start with `~/`, which is read from the user's own home. kiro-gdrive
  uses it for its daily "new files in your shared Google Drive folders" feed.

### Technical Details
- `usr/share/kiro-news/feed.json`: item `2026-10-17-kirotux-niri-noctalia-v26-10-16` at the top.
  The id keeps `v26-10-16` on purpose: changing it would notify everyone who already saw the item a second time.
- `fetch_body` treats `~/...` as a local path and expands the leading `~` to `$HOME`. kiro-news runs as a user
  timer, so each user reads their own file. A missing file is the usual "unavailable, skipping" case.

### Files Modified
- `usr/share/kiro-news/feed.json`, `usr/bin/kiro-news`, `CHANGELOG.md`, `CLAUDE.md` (release versions must match
  the ISO file name; the local flows now check it)

## 2026.10.16

### What Changed
- **Kiro's own news comes before Arch Linux News.** On the news page and in the notifications, the channels now come
  in this order: KiroTux News (on KiroTux systems), Kiro News, then Arch Linux News. Before, Arch came first.
- New Kiro News item: **KiroTux Hyprland Noctalia v26.10.16 is out**, the third KiroTux edition (Hyprland +
  Noctalia, Kirotux Hyprland Premium), with the Ko-fi link.
- **A news notification shows one Open button, not two.** Daemons that draw the click action as a button showed it
  next to the separate Open button.

### Technical Details
- `etc/kiro-news/sources.conf`: the `kiro` line moved above `arch`. `source_lines()` already reads `sources.d/*.conf`
  first, then this file, so feeds added by other packages stay on top. The file is not in `backup=()`, so pacman
  replaces it on upgrade and existing installs get the new order.
- `usr/share/kiro-news/feed.json`: item `2026-10-16-kirotux-hyprland-noctalia-v26-10-16` at the top.
- `notify_item` now passes only `--action=default=Open`. The extra `--action=open=Open` from 2026.10.14 duplicated
  it on daemons that draw `default` as a button. Spec-compliant daemons that hide `default` still open the link on
  click.

### Files Modified
- `etc/kiro-news/sources.conf`
- `usr/share/kiro-news/feed.json`
- `usr/bin/kiro-news`
- `CHANGELOG.md`

## 2026.10.14

### What Changed
- **The link in a news notification can now be opened.** Notification text isn't clickable or selectable in most
  notification daemons (DMS included), so the link printed in the toast was a dead end. Clicking the toast, or its
  **Open** button, now opens the link in the browser. The link stays in the text for daemons without actions.

### Technical Details
- `notify_item` passes `--action=default=Open --action=open=Open` when an item has a link and runs `xdg-open` when
  either action is chosen. `--action` makes notify-send wait until the toast closes, so it runs in a background
  subshell (capped by `timeout 600`), and `cmd_poll` ends with `wait` so the oneshot user service stays up until
  the toasts close. Items without a link still send a plain notification.

### Files Modified
- `usr/bin/kiro-news`
- `CHANGELOG.md`

## 2026.10.11

### What Changed
- New Kiro feed item `2026-10-11-kirotux-niri-dms-v26-10-11`: announces **KiroTux Niri DMS v26.10.11**, the second
  KiroTux edition (niri + DankMaterialShell, with Kirotux Niri Premium). Same build as the Hyprland DMS announcement:
  what KiroTux is, the desktop, the premium app as bullets, the free `kiro-niri-dms` config, donation with a minimum,
  and links to the Ko-fi product (https://ko-fi.com/s/b8f11814a2) and the KiroTux page. Now the newest item.
- **Other packages can add a news feed** without touching `sources.conf`: kiro-news now also reads every
  `/etc/kiro-news/sources.d/*.conf` (same `id|type|url|display name|verify` line format), in name order and
  **before** `sources.conf`, so a package's feed is the first section on the news page. Each extra source gets its own notifications and its own section in `kiro-news show`. Nothing changes
  for a system without such files: still Arch Linux News and Kiro News.
- **Optional 6th field `notify`** on a source line: the newest item is shown on the first run too. The default
  (`baseline`, or no 6th field) stays as before: a first run only records the newest item, so a fresh install isn't
  flooded. Meant for feeds a package adds later, whose newest item is exactly what the user should see.

### Technical Details
- New `source_lines()` prints the `sources.d/*.conf` files and then `sources.conf`; `cmd_poll` and `cmd_show` read
  from it instead of `sources.conf` alone. The folder follows `KIRO_NEWS_CONFIG` (or `KIRO_NEWS_SOURCES_DIR`).
- `etc/kiro-news/sources.d/README` keeps the folder in git and in the package; it isn't a `.conf`, so it's not read.
- Tested with a temporary config: no extra files → Kiro only; one extra `.conf` → baseline on first run, a
  notification for a new item, and a second section on the news page.

### Files Modified
- `usr/bin/kiro-news`
- `etc/kiro-news/sources.conf` (comment for the 6th field)
- `etc/kiro-news/sources.d/README` (new)
- `CHANGELOG.md`

## 2026.10.09

### What Changed
- New Kiro feed item `2026-10-09-kirotux-hyprland-dms-v26-10-08`: announces the KiroTux Hyprland DMS v26.10.08
  release. It explains what KiroTux is (built on Kiro, one ISO for Hyprland + DankMaterialShell), what's new (dark
  theming for Qt and root apps), what the premium settings app (Ctrl + Alt + H) does, and how to get it (donation
  with a minimum). Now the newest item in the feed. It's split into short paragraphs, lists the settings app's features as bullets, and ends with links to the Ko-fi shop and the KiroTux page.
- **`kiro-news show` reads like a web page:** each item is a card with space around it, the date sits on its own
  line, and inside an article paragraphs are spaced, lists show bullets and links are blue and underlined. Before,
  the page-wide `ul`/`a` styles hid bullets and made links look like plain text.
- **The six older posts are restructured the same way:** short paragraphs, bullets where they list steps or
  options, and a link at the end where the post names a page (Fish Tweak Tool on GitHub, informant in the AUR,
  the Arch advisory). The facts are unchanged.

### Technical Details
- Facts come from the kiro-iso-hyprland-dms RELEASES.md entry, the kirotux-hyprland-premium README and the
  website's kirotux.html; nothing from the web. Earlier test builds aren't mentioned. `build.sh` bumps the
  package version, which is what delivers the item to users.
- The item list now uses `ul.items`, so the card styling no longer leaks into lists inside an article; the
  `.summary` rules style `p`, `ul`, `li` and `a`. Notifications show only the title and link, so article HTML
  never reaches them.

### Files Modified
- `usr/share/kiro-news/feed.json`
- `usr/bin/kiro-news`
- `CHANGELOG.md`

## 2026.09.12

### What Changed
- New Kiro feed item `2026-09-12-v26-10-01-kernels`: announces that the next
  Kiro ISO, **v26.10.01** (due 1 October 2026), ships **`linux`** as the main
  kernel with **`linux-lts`** installed alongside as the backup — a change from
  v26.09.01's `linux-cachyos` + `linux-zen`. Explains the two-kernel policy and
  why the pairing rotates monthly, notes that both kernels keep their
  `-headers` so DKMS stays buildable, and tells existing users there is nothing
  to do. Now the newest item in the feed.
- Bumped `pkgver` 26.07 → 26.09 in the PKGBUILD — the feed ships inside the
  package, so a version bump is what actually delivers the item to users.
- **`kiro-news show` page readability:** item titles are now bold and the
  summary starts after a real blank line, so each headline stands out from the
  body text instead of blending into it.

### Technical Details
- Added `li>a{font-weight:700}` to the inline stylesheet in `cmd_show` — scoped
  to list items so the gradient `h1` and the `h2` source headings are untouched.
- Widened `.summary` margins from `.2rem 0 .8rem` to `.75rem 0 1.4rem`: the top
  margin creates the blank line under the title, the bottom one keeps
  consecutive items from running together.

### Files Modified
- `usr/share/kiro-news/feed.json`
- `usr/bin/kiro-news`
- `../KIRO-PKG-BUILD-APPS/kiro-news/PKGBUILD` (pkgver 26.07 → 26.09)

## 2026.07.01

### What Changed
- New Kiro feed item `2026-07-01-broadcom-wl-dkms-local-newer`: explains the
  `warning: broadcom-wl-dkms: local (...) is newer than extra (...)` pacman
  message users with the optional `cachyos` repo enabled may see — cachyos
  can rebuild DKMS packages with a higher pkgrel before `extra` catches up;
  since Kiro's pacman.conf lists `extra` before `cachyos`, later syncs keep
  resolving against the older `extra` pkgrel. Harmless, no downgrade happens,
  no action required. Now the newest item in the feed.

### Files Modified
- `usr/share/kiro-news/feed.json`

## 2026.06.29

### What Changed
- **Moved the launcher out of the Internet menu into the Kiro menu.** Changed
  `kiro-news.desktop` from `Categories=Network;News;X-Kiro;` to `Categories=X-Kiro;`
  so it no longer shows under Internet — it now appears only in the Kiro menu
  alongside the other Kiro links. (An earlier 102/103 build deleted it outright;
  this restores it, relocated.)

### Files Modified
- `usr/share/applications/kiro-news.desktop` (Categories → X-Kiro only)

## 2026.06.27

### What Changed
- New Kiro feed item `2026-06-27-fish-tweak-tool`: announces the release of
  **Fish Tweak Tool**, a GTK4 graphical configurator for the fish shell
  (install prompts/plugins, browse themes, tune greeting/cursor). Now the
  newest item in the feed.
- **Notifications now linger 60 seconds.** Added `--expire-time=60000` to the
  `notify-send` call in `notify_item()` so news messages stay on screen for a
  minute instead of vanishing in the daemon's default few seconds. Clicking the
  notification still dismisses it immediately (default daemon behaviour).

### Files Modified
- `usr/share/kiro-news/feed.json`
- `usr/bin/kiro-news`

## 2026.06.15

### What Changed
- **Localized the desktop entry.** Added a translated `Comment` and a new `GenericName`
  ("News Reader") in 14 languages (de, fr, nl, es, it, pt_BR, pt, ru, pl, uk, zh_CN, ja, tr, cs).
  Brand `Name` and technical `Keywords` stay English.
- **Fixed the menu category.** `Categories=X-Kiro;` had no registered main category (entry fell to
  the menu catch-all off-Kiro). Now `Network;News;X-Kiro;` — proper home as a news reader, while
  `X-Kiro` keeps it in the Kiro submenu.

### Files Modified
- `usr/share/applications/kiro-news.desktop`

## 2026.06.14

### What Changed
- New Kiro feed message `2026-06-14-pacman-conf-repos`: reminds users to verify
  their `/etc/pacman.conf` enables the two repositories Kiro relies on —
  `nemesis_repo` and `chaotic-aur` — with the exact stanzas to add. `cachyos`
  is included commented-out as optional (shipped disabled by default).

- Fixed multi-line summaries rendering as literal `\n`: the styled `<pre>` repo
  block (and any future multi-line summary) now breaks into real lines instead
  of showing escaped newlines.

### Technical Details
- Prepended the item to the top of `items` in
  `usr/share/kiro-news/feed.json`. The repo blocks are rendered in a styled
  `<pre>` (trusted inline HTML, consistent with the existing `<strong>` usage —
  the feed ships inside the package, so it is not escaped).
- Root cause of the literal-`\n` bug: item summaries flow through jq `@tsv`
  (one record per line), which re-escapes real newlines back to literal `\n`.
  Fixed at the transport layer — the `summary` field is now base64-encoded in
  the jq query (`(.summary // "" | @base64)`) and decoded with `base64 -d` in
  the HTML read loop. This preserves newlines and special chars for all
  summaries, not just this one string. RSS items carry no summary, so the
  decode is a no-op there.

### Files Modified
- `usr/share/kiro-news/feed.json`
- `usr/bin/kiro-news`

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

- **Kiro feed is now shipped inside the package**, not fetched from the website
  (design change, replaces the signed-website-feed plan). Two bugs found testing
  the dependency arrival on fresh PCs drove this: **(1)** the XFCE menu entry was
  missing because `kiro-dot-files`' `kiro.menu` had a `<Layout>` with explicit
  filenames and no `<Merge type="all"/>`, so garcon showed only the 4 listed
  links and hid `kiro-news.desktop` — fixed by adding `kiro-news.desktop` to the
  Layout **plus** a `<Merge type="all"/>` safety net so future `X-Kiro` apps
  can't be hidden again; **(2)** launching the client tried to read
  `https://kiroproject.be/news/feed.json` (404). Now the Kiro source points at a
  **local** `/usr/share/kiro-news/feed.json` shipped in the package
  (`verify=none` — the package is the trust boundary). This also **retires the
  minisign feed-signing** plan entirely: no website hosting, no keypair, no 404;
  updating Kiro news = edit `feed.json` → rebuild → publish → users get it on
  `-Syu`. (The icon was never the problem — `rss` resolves via `neo-candy-icons`
  → BeautyLine inheritance.)

- Published a second Kiro News post (now the newest item): an **opt-in informant**
  message — informant is not installed by default; users who want upgrades to pause
  on Arch news can `paru -S informant` themselves (chaotic-aur). Decided against an
  auto-install/dependency or an ATT toggle for now — a news post is the chosen surface.
- Feed items support a `summary` field with **light inline HTML** (`<strong>`,
  `<em>`) rendered in the `show` view — the feed ships in the package, so it's
  trusted content and not escaped. The AUR-incident message now bolds the key
  reassurances ("Kiro is not affected", "review every PKGBUILD…", "nothing you
  need to do on Kiro itself").

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
- `usr/bin/kiro-news` (added `show` command + multi-item parsers + HTML view; `fetch_body` reads local-file sources)
- `usr/share/applications/kiro-news.desktop` (menu launcher, `Categories=X-Kiro`, `Icon=rss`)
- `usr/share/kiro-news/feed.json` (new — Kiro's own feed, shipped in-package; replaces `README.pubkey`, removed)
- `etc/kiro-news/sources.conf` (Kiro source → local `/usr/share/kiro-news/feed.json`, `verify=none`)
- cross-repo: `kiro-dot-files` `kiro.menu` `<Layout>` (+`kiro-news.desktop`, +`<Merge type="all"/>`)
- `usr/lib/systemd/user/kiro-news.service`, `kiro-news.timer`
- `etc/systemd/user/timers.target.wants/kiro-news.timer` (symlink)
- `usr/share/kiro-news/README.pubkey` (placeholder until `kiro.pub` exists)
- `README.md`, `LICENSE`, `.gitignore`
- Packaging: `KIRO-PKG-BUILD-APPS/kiro-news/{PKGBUILD,build.sh,up.sh,.gitignore}`
