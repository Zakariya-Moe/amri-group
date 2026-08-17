# AMRI Group

Marketing website for AMRI Group — brand, web and digital presence work for
businesses in Atlanta, Georgia whose companies have outgrown their online presence.

Single-page static site. Typography-led identity, no logo symbol. Light and dark
themes with system preference support.

---

## Stack

- HTML
- CSS (custom properties, no preprocessor)
- Vanilla JavaScript (no framework, no build step, no dependencies)

Fonts are loaded from Fontshare (Switzer, Clash Grotesk) and Google Fonts
(IBM Plex Mono). No font files are redistributed in this repository.

---

## Local preview

The site must be served over HTTP rather than opened as a `file://` path, so that
root-relative asset paths (`/assets/...`) resolve the way they will in production.

```bash
python3 -m http.server 8080
```

Then open <http://localhost:8080>.

Any equivalent static server works, for example:

```bash
npx serve .
```

---

## Production

Designed for static hosting on Vercel. No build command and no output directory —
deploy the repository root as-is.

`vercel.json` sets `cleanUrls`, a small set of security headers, and long-lived
immutable caching for `/assets/*`. There is intentionally **no Content Security
Policy**: the site loads webfonts from two external origins, and a careless CSP
would break them. Add one only after testing font loading against it.

---

## Project structure

```text
amri-group/
├── index.html                 # the entire site
├── 404.html                   # branded not-found page
├── favicon.ico
├── robots.txt
├── sitemap.xml
├── site.webmanifest
├── vercel.json
├── .gitignore
├── README.md
└── assets/
    ├── brand/                 # favicon + app icon PNGs (typographic "A")
    ├── social/
    │   └── og-image.jpg       # 1200x630 social card
    └── images/
        ├── trademark/         # trademark-home / -detail / -mobile .webp
        ├── visionary/
        ├── linework/
        └── helio/
```

### Asset paths

`index.html` and `site.webmanifest` use **relative** paths (`assets/images/...`), so the
site works unchanged whether it is served from a domain root, served from a
subdirectory, or opened directly from disk. Keep the folder intact — `index.html`
alone will render its layout but show no project imagery.

`404.html` is the one deliberate exception: it uses **root-relative** paths (`/assets/...`).
Vercel serves that file's content at whatever URL was requested, so a relative path
inside it would resolve against the missing path (`/some/deep/url/assets/...`) and break.
Root-relative is the correct choice there.

CSS and JavaScript are intentionally inline in `index.html`. For a single-page
site this removes two render-blocking requests and keeps the deployable surface
to one file; splitting them out is only worth doing if a second page is added.

---

## Launch checklist

Unresolved items that must be handled before or at launch:

- [ ] **Production domain** — not confirmed. `canonical` and `og:url` are
      commented out in `index.html` rather than guessed. `sitemap.xml` uses a
      `PRODUCTION_DOMAIN` placeholder, and `robots.txt` needs the absolute
      sitemap URL. Search for `PRODUCTION_DOMAIN` to find every spot.
- [ ] **Contact form endpoint** — `form.consult` has an empty `data-endpoint`.
      Until it is set, submitting opens a pre-addressed email draft. That is a
      fallback, not a lead system. Set `data-endpoint` to a POST handler.
- [ ] **`og:image` absolute URL** — the card exists at
      `/assets/social/og-image.jpg`, but most crawlers require an absolute URL.
      Update once the domain is confirmed.
- [ ] **Business email** — currently `contact@amri-group.pro` (in the form's
      `data-mail`). Confirm this is the production address.
- [ ] **Social profile URLs** — the footer social column is intentionally absent
      until real profiles exist. Do not add placeholder links.
- [ ] **Analytics / Search Console** — not configured. No tracking script is
      present. Add one deliberately if wanted, and submit the sitemap.

### Never commit

No API keys, tokens, form secrets or credentials belong in this repository.
If the contact endpoint eventually needs a secret, put it behind a serverless
function and supply it as an environment variable.

---

## Updating project imagery

The four project screenshots in `assets/images/` were captured from the live
client sites in **August 2026**:

| Project | Live site |
|---|---|
| Trademark Design Painting & Decorating | https://www.trademarkdp.com/ |
| Visionary Construction | https://visionary-pro.com/ |
| Linework Studio | https://linework.studio/ |
| Helio | https://www.gethelioapp.com/ |

They are real screenshots, not mockups or stock imagery. When any of these sites
ships a meaningful redesign, recapture that project's three images
(`-home`, `-detail`, `-mobile`), keep the same filenames, and re-export as WebP
at roughly the current dimensions so the existing `width`/`height` attributes
stay accurate and no layout shift is introduced.

Never substitute stock photography, and never fabricate a screenshot of work
that does not exist.

---

## Editorial rules baked into this site

These are deliberate and should survive future edits:

- No public pricing. The model is consultation → audit → scope → proposal.
- No invented proof: no testimonials, metrics, traffic claims or awards.
- No internal production language in visible UI ("in development", "reserved",
  "active engagement"). Internal HTML comments are fine.
- No logo symbol. The wordmark is the identity.
