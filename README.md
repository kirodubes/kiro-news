# kiro-news

In-system news notifier for Kiro. Polls one or more sources and shows a
desktop notification when a source has a new headline — DE-agnostic (XFCE,
every TWM), runs on a per-user systemd timer, no server.

Two source types (`/etc/kiro-news/sources.conf`):

| type | trust | use |
|------|-------|-----|
| `rss`  | HTTPS, display-only | feeds we consume but don't own (Arch Linux news) |
| `json` | **minisign-signed**, verified before display | Kiro's own announcements |

Kiro's own feed is a static signed file published on the website; the client
verifies it against the public key shipped at `/usr/share/kiro-news/kiro.pub`
before showing anything, so a forged "Kiro says ..." message is never displayed.
One unreachable or malformed source never aborts the others.

## Status

- **Arch news** — live and working.
- **Kiro feed** — present in config but **inert** until the minisign keypair is
  generated (`minisign -G`, private key kept off-repo) and the signed
  `feed.json` + `feed.json.minisig` are published. While `kiro.pub` is absent
  the client skips the Kiro source cleanly.

## Publishing a Kiro message (once signing is enabled)

1. Add an item to the top of `items` in the feed JSON.
2. Sign it: `minisign -Sm feed.json` → `feed.json.minisig`.
3. Deploy both files to the website.

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
- `/usr/share/kiro-news/kiro.pub` — Kiro signing public key (added later)
- `/usr/lib/systemd/user/kiro-news.{service,timer}` + auto-enable symlink
