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
- A release item's version must match the released ISO's file name exactly. The local flow scripts check this
  before building and stop on a mismatch.

Translations (since 2026-10-31):
- **Every new Kiro feed item gets an `i18n` block** with all 13 languages (nl de es fr pt_BR ru hu it tr pl ja zh_CN
  ro): `"i18n": {"de": {"title": "…", "summary": "…"}, …}`. Claude drafts it from the English item (Copilot is
  out since 2026-11-02, Erik: all translation work by Claude), keep every HTML tag and the product names, then check tags match before publishing. Items without `i18n`
  simply stay English. A release item's version text must match in every language too. Since 2026-11-01 every
  item in both feeds is translated, so new items without `i18n` are the only English ones left.
- **Button and page names in `<strong>` must be the app's own translation**, taken from its `.po` (premium apps:
  `~/KIROTUX/kirotux-*-premium/po`, KiroTux Drive: `~/KIRO/kiro-gdrive/po`), not a fresh translation. The apps
  differ from each other (e.g. ru "Status" is Статус in Ohmychadwm Tweak Tool but Состояние in the premium apps).
- The client picks the language in `news_languages()` + jq `tr()`; kiro-news' own texts go through `_t "…"`
  (gettext, domain `kiro-news`). After changing one: `po/make-mo.sh`. The menu entry is hand-translated (edit it
  directly, all 13 languages).
- Arch Linux news (RSS) is upstream and stays English.
- The news page (`kiro-news show`) carries every language at once and switches in the browser (globe menu,
  since 2026-11-01): page texts use `_tall "…"` (not `_t`), feed items come from `parse_json_items_html`. A new
  language = an entry in `NEWS_UI_LANGS` + `LANG_NAMES`, besides its `.po`.
