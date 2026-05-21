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
`tools/make_web_screenshots.swift`. After updating the source
captures there, copy the regenerated `_web/` directory into this repo:

```sh
cd ../spool
swift tools/make_web_screenshots.swift
cp -R landing/screenshots/_web/. ../spool-website/screenshots/_web/
cd ../spool-website
git diff screenshots/_web/   # sanity check
git add screenshots/_web/
git commit -m "Refresh web screenshots"
git push
```

## Deploy

Cloudflare Pages watches `main`. Every push redeploys to
[getspool.news](https://getspool.news).

## License

MIT. See [LICENSE](./LICENSE).
