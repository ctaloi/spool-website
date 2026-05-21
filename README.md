# spool-website

Marketing site for [Spool](https://github.com/ctaloi/spool) — a calm
Hacker News reader for iPhone and iPad with on-device AI summaries,
a hands-free audio queue, and zero tracking.

Lives at **[getspool.news](https://getspool.news)**, deployed via
Cloudflare Pages.

## Structure

- `index.html` — home page (hero, features, screenshots)
- `privacy/index.html` — privacy policy
- `screenshots/` — source captures + web-sized variants
  - `_web/` — status-bar-cropped versions the page actually serves
  - per-feature directories — original device captures

## Editing

Single static HTML files, no build step.

```sh
# Open the home page in your browser.
open index.html
```

Pure HTML + CSS + light SVG. No JS. No analytics. No third-party scripts.

## Refreshing screenshots

The app repo (`ctaloi/spool`) generates the web-sized PNGs via
`tools/make_web_screenshots.swift`. The tool writes directly across
the worktree boundary into `../spool-website/screenshots/_web/`, so a
single run regenerates both light and dark variants in place — no
copy step needed.

```sh
cd ../hacker-news    # the spool app repo, sibling to this one
swift tools/make_web_screenshots.swift
cd ../spool-website
git diff screenshots/_web/   # sanity check
git add screenshots/_web/
git commit -m "Refresh web screenshots"
git push                                      # GitHub only — does NOT deploy
npx wrangler@latest pages deploy . \
    --project-name=spool --branch=main \
    --commit-dirty=false                      # actually publishes to getspool.news
```

Each screenshot section in `index.html` uses a `<picture>` element
with a `prefers-color-scheme: dark` source so the dark capture is
served automatically when the visitor's OS is in dark mode. Adding a
new screenshot is a two-step change:

1. Add a `WebSlot` entry in the app repo's
   `tools/make_web_screenshots.swift` (the stem becomes
   `<stem>-light.png` + `<stem>-dark.png` in `_web/`).
2. Drop a new `<section class="deep">` into `index.html` with a
   `<picture>` block referencing both variants.

## Deploy

The Cloudflare Pages project (`spool` in the Vaspian Engineering
account) is **direct-upload**, not git-bound — `git push` only updates
GitHub; it does not redeploy the site. To publish a change to
[getspool.news](https://getspool.news), run wrangler from this repo
root:

```sh
npx wrangler@latest pages deploy . \
    --project-name=spool --branch=main \
    --commit-dirty=false
```

`--branch=main` aliases the upload to the production custom domains
(`getspool.news` + `www.getspool.news`). The first run installs
wrangler on the fly via npx and uses cached Cloudflare credentials
in `~/.config/.wrangler/`.

## License

MIT. See [LICENSE](./LICENSE).
