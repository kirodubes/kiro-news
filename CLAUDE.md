# kiro-news — project notes

In-system news notifier for Kiro (Arch RSS + Kiro signed JSON feed). Source repo;
packaged by `KIRO-PKG-BUILD-APPS/kiro-news` → nemesis_repo → kirodubes/kiro-news.

Design spec: `~/KIRO/kiro-news-channel-DESIGN.md`.

Key invariants:
- The Kiro (`json`, `verify=signed`) source must never display without a valid
  minisign signature. The private key is Erik's, kept off-repo; only `kiro.pub`
  ships.
- The client is headless (systemd user timer) — keep logging lean, no banners.
- One bad source must never abort the others.
