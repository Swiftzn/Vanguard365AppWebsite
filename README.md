# Vanguard365 site

Static marketing site for vanguard365.co.uk — homepage plus one landing page
per app. Plain HTML/CSS/JS, no build step, deployed on Cloudflare Pages.

## Structure

```
index.html                          Homepage — lists all apps
apps/docx-to-pdf-converter/index.html   Product page for the DOCX→PDF app
assets/css/style.css                Shared styles
assets/js/main.js                   Tiny shared JS (just sets footer year)
robots.txt, sitemap.xml             SEO
_redirects, _headers                Cloudflare Pages config
404.html                            Custom not-found page
```

To add a new app: copy the `apps/docx-to-pdf-converter/` folder, edit its
content and `<title>`/meta tags, add a card for it on the homepage, and add
its URL to `sitemap.xml`.

## Downloads — how they're wired up

The download buttons all point at `/download/docx-to-pdf-converter`, which
`_redirects` 302-redirects to a GitHub Releases asset. This keeps the public
link stable even as the installer gets rebuilt and re-released.

Releases live in a separate **public** repo,
[Swiftzn/BatchConvertPDF-Releases](https://github.com/Swiftzn/BatchConvertPDF-Releases) —
kept separate from this (private) site repo and from the app's actual source
repo, so no code has to be public for downloads to work. GitHub release
assets on a private repo require login to fetch, so the repo holding the
`.exe` has to be public even though the site itself is private.

To publish a new release of the installer:

```bash
gh release create v1.0.0 "E:/Projects/BatchConverPDF/app/installer/Output/DocxToPdfConverter-Setup-1.0.0.exe#DocxToPdfConverter-Setup.exe" \
  --repo Swiftzn/BatchConvertPDF-Releases \
  --title "DOCX to PDF Converter v1.0.0" \
  --notes "Initial release"
```

The `#DocxToPdfConverter-Setup.exe` part renames the uploaded asset so its
filename stays constant across releases — this is required for the
`/releases/latest/download/DocxToPdfConverter-Setup.exe` URL in `_redirects`
to keep working without editing it every time you ship a new version.

For a future release, use `gh release create v1.1.0 ... --repo Swiftzn/BatchConvertPDF-Releases`
again with the same `#DocxToPdfConverter-Setup.exe` rename — no other changes
needed, and nothing in this site repo has to be touched or redeployed.

## Deploying to Cloudflare Pages

1. Push this repo to GitHub — [Swiftzn/Vanguard365AppWebsite](https://github.com/Swiftzn/Vanguard365AppWebsite)
   (private is fine; Cloudflare Pages' GitHub App integration works with
   private repos too, it just needs to be granted access to this one).
2. In the Cloudflare dashboard: **Workers & Pages → Create → Pages → Connect
   to Git**, pick this repo (grant the GitHub App access to it if prompted).
3. Build settings: framework preset **None**, build command **empty**,
   output directory **/** (root) — this is a static site, no build step.
4. Deploy. Cloudflare will give you a `*.pages.dev` URL first.
5. **Custom domain**: in the Pages project → **Custom domains** → add
   `vanguard365.co.uk` (and `www.vanguard365.co.uk` if you want that too).
   If the domain's DNS is already on Cloudflare, this is one click; Cloudflare
   adds the necessary DNS records automatically. If not, point your
   registrar's nameservers at Cloudflare first.
6. Every push to the main branch auto-deploys. Pull requests get their own
   preview URL automatically.

## Before going live — placeholders to fill in

- [ ] Pricing on `apps/docx-to-pdf-converter/index.html` (`#pricing` section)
      — the £19 figure is a placeholder, set it to your actual price
- [ ] `support@vanguard365.co.uk` — set up this mailbox, or swap the address
      used across both pages and the footer
- [ ] `assets/img/og-default.png` and `assets/img/og-docx-to-pdf.png` — social
      share preview images referenced in the `<meta property="og:image">`
      tags don't exist yet; add 1200×630px PNGs or the previews will break
- [ ] `assets/img/logo.png` — referenced in the homepage's JSON-LD, doesn't
      exist yet
- [ ] Real product screenshots on the product page (currently text/feature
      list only — a screenshot or short GIF of the app converting a folder
      would help conversion a lot)
