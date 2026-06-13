# kiro-news

In-system news notifier for Kiro. Polls one or more sources and shows a
desktop notification when a source has a new headline — DE-agnostic (XFCE,
every TWM), runs on a per-user systemd timer, no server.

Sources are defined in `/etc/kiro-news/sources.conf` (`id|type|url|name|verify`):

| source | type | location | use |
|--------|------|----------|-----|
| Arch Linux News | `rss` | HTTPS (`archlinux.org`) | upstream news we consume but don't own |
| Kiro News | `json` | **local file in this package** (`/usr/share/kiro-news/feed.json`) | Kiro's own announcements |

**Kiro's own feed ships *inside this package*** — it is not fetched from any
website. The package is the trust boundary: `feed.json` is exactly as trusted as
`/usr/bin/kiro-news` itself (and is covered automatically if/when nemesis_repo
packages get signed). No network, no separate feed-signing key. One unreachable
or malformed source never aborts the others.

## Publishing a Kiro message

1. Edit `usr/share/kiro-news/feed.json` — add an item to the top of `items`
   (`id`, `title`, `url`, `date`).
2. Rebuild + publish the package (`build.sh` bumps pkgrel → nemesis_repo).
3. Users receive it on their next `pacman -Syu`; the timer toasts the new item.

## Commands

- `kiro-news` (or `kiro-news poll`) — the timer entry point: poll each source,
  toast the newest unseen item.
- `kiro-news show` — open a readable HTML page of recent items from every source
  in the browser. Toasts are ephemeral; this is the re-read view. Exposed in the
  XFCE menu under **Kiro** (next to the Kiro links) via the shipped `.desktop`.

## Files

- `/usr/bin/kiro-news` — the client (`poll` + `show`)
- `/usr/share/applications/kiro-news.desktop` — menu launcher (`Categories=X-Kiro`)
- `/etc/kiro-news/sources.conf` — source definitions
- `/usr/share/kiro-news/feed.json` — Kiro's own news feed (edit + republish to update)
- `/usr/lib/systemd/user/kiro-news.{service,timer}` + auto-enable symlink
