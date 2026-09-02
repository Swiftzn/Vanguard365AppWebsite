# Vanguard365 site

Static marketing site for vanguard365.co.uk — homepage plus one landing page
per app. Plain HTML/CSS/JS, no build step, deployed on GitHub Pages.

## Structure

```
index.html                          Homepage — lists all apps
apps/docx-to-pdf-converter/index.html   Product page for the DOCX→PDF app
assets/css/style.css                Shared styles
assets/js/main.js                   Tiny shared JS (just sets footer year)
robots.txt, sitemap.xml             SEO
CNAME                                Custom domain for GitHub Pages
404.html                            Custom not-found page
```

To add a new app: copy the `apps/docx-to-pdf-converter/` folder, edit its
content and `<title>`/meta tags, add a card for it on the homepage, and add
its URL to `sitemap.xml`.

## Downloads — how they're wired up

Download buttons link straight to a GitHub Releases asset URL:

```
https://github.com/Swiftzn/BatchConvertPDF-Releases/releases/latest/download/DocxToPdfConverter-Setup.exe
```

The `/releases/latest/download/<filename>` pattern always resolves to the
newest release's asset with that exact filename, so this link never needs to
change as new versions ship — as long as the uploaded asset is always named
`DocxToPdfConverter-Setup.exe` (see below).

Releases live in a separate **public** repo,
[Swiftzn/BatchConvertPDF-Releases](https://github.com/Swiftzn/BatchConvertPDF-Releases) —
kept separate from this site repo and from the app's actual source repo, so
no app source has to be public for downloads to work. (GitHub Pages itself
also requires this site's repo to be public — see below — but the releases
repo would need to be public regardless, since private-repo release assets
require a logged-in GitHub session to fetch.)

To publish a new release of the installer:

```bash
gh release create v1.0.0 "E:/Projects/BatchConverPDF/app/installer/Output/DocxToPdfConverter-Setup-1.0.0.exe#DocxToPdfConverter-Setup.exe" \
  --repo Swiftzn/BatchConvertPDF-Releases \
  --title "DOCX to PDF Converter v1.0.0" \
  --notes "Initial release"
```

The `#DocxToPdfConverter-Setup.exe` part renames the uploaded asset so its
filename stays constant across releases — this is required for the
`/releases/latest/download/DocxToPdfConverter-Setup.exe` link above to keep
working without editing every page that links to it, every time you ship a
new version.

For a future release, use `gh release create v1.1.0 ... --repo Swiftzn/BatchConvertPDF-Releases`
again with the same `#DocxToPdfConverter-Setup.exe` rename — no other changes
needed, and nothing in this site repo has to be touched or redeployed.

## Deploying to GitHub Pages

GitHub Pages only serves sites from **public** repos on the free plan (Pro/
Team/Enterprise can use private repos). This repo contains no app source or
secrets, so make it public: repo **Settings → General → Danger Zone → Change
repository visibility → Public**.

1. Push this repo to GitHub — [Swiftzn/Vanguard365AppWebsite](https://github.com/Swiftzn/Vanguard365AppWebsite).
2. Repo **Settings → Pages**. Under "Build and deployment," set **Source** to
   **Deploy from a branch**, branch **main**, folder **/ (root)**. Save.
3. GitHub builds and serves the site at `https://swiftzn.github.io/Vanguard365AppWebsite/`
   within a minute or two — check that URL works before moving on. (It will
   look broken on that URL specifically once the custom domain is set below,
   since all the HTML uses root-relative paths like `/assets/...` — that's
   expected and fine, it's not the final URL.)
4. **Custom domain**: still on the Pages settings screen, enter
   `vanguard365.co.uk` under "Custom domain" and save — this writes the
   `CNAME` file already committed in this repo back if it's ever missing.
5. At your domain's current DNS provider (wherever it's hosted right now —
   no nameserver change needed), add these records:
   - Four `A` records for the apex domain (`vanguard365.co.uk`) pointing at
     GitHub Pages' IPs: `185.199.108.153`, `185.199.109.153`,
     `185.199.110.153`, `185.199.111.153`
   - A `CNAME` record for `www` pointing at `swiftzn.github.io`
6. Back in GitHub Pages settings, once DNS has propagated (can take up to a
   few hours), tick **Enforce HTTPS** — GitHub provisions a free certificate
   automatically.
7. Every push to `main` auto-deploys within a minute or two.

## Before going live — placeholders to fill in

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
