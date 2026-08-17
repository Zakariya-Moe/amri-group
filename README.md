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

> **Do not combine the two Fontshare requests.** They are two separate `<link>`
> tags on purpose. A single combined request
> (`?f[]=switzer@…&f[]=clash-grotesk@…`) returns Switzer only — Clash Grotesk
> never enters `document.fonts` and every display heading silently falls back to
> Switzer. That shipped undetected because the CSS still *says*
> `font-family:"Clash Grotesk"`; only `document.fonts` reveals it. If you touch
> the font links, verify with:
>
> ```js
> [...document.fonts].map(f => f.family + ' ' + f.weight)
> ```
>
> Clash Grotesk must appear in that list. `document.fonts.check()` is not a
> valid test here — it returns `true` even when the face is absent.

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
        ├── trademark/         # -wall (exhibition) · -detail / -mobile (dialog) · -home (unused)
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
- [ ] **Business email** — `contact@amri-group.pro`. **Now published in three
      places**, not just the form fallback: the form's `data-mail`, the
      "Prefer email?" line in the contact section, and the footer Contact
      column. Confirm the address or change all three together — search the
      file for `amri-group.pro`. This is the most urgent open item, because a
      wrong address is now a visible dead end rather than a silent one.

- [ ] **Ownership A/B — temporary code to remove.** As of V2.4 the **oxblood
      field is the default**: `section.own-band` with no attribute renders the
      full saturated treatment (ivory on `--red` in light, on `--red-deep` in
      dark, with the ghost AMRI wordmark in the field). The previous restrained
      treatment is kept behind `data-own="a"` purely so the two can still be
      compared on the real page:

      ```js
      document.querySelector('.own-band').setAttribute('data-own','a') // restrained
      document.querySelector('.own-band').removeAttribute('data-own')  // field
      ```

      Once the field is signed off, delete the `.own-band[data-own="a"]` block
      and the hook with it. Do not ship both.

      Note the field tokens are scoped with `.own-band:not([data-own="a"])`
      rather than overridden later. A custom property cannot be "reset to the
      root value" — `--ink:initial` blanks it rather than restoring it — so the
      restrained variant has to simply never receive them.
- [ ] **Social profile URLs** — the footer social column is intentionally absent
      until real profiles exist. Do not add placeholder links.
- [ ] **Analytics / Search Console** — not configured. No tracking script is
      present. Add one deliberately if wanted, and submit the sitemap.

### Section anchors

The nav label "What We Do" used to link to `#what-we-do`, which was the
Launch / Rebuild / Grow section — not the capabilities section the label
describes. Two things changed:

| Section | Anchor | Reached from |
|---|---|---|
| Launch / Rebuild / Grow | `#situations` (was `#what-we-do`) | footer "Where You Are" |
| Brand / Web / Presence / Growth | `#capabilities` | nav + footer "What We Do" |

`#what-we-do` no longer exists. Nothing external is known to link to it, but if
an old link turns up, point it at `#situations`.

Chapter numbering is sequential and rendered at up to 120px, so the numbers are
visible content rather than incidental labels: `01 The Gap` through `09 Start`.
Inserting or removing a section means renumbering the `.ch-no` values after it.

### Section spacing

There is deliberately **no global section padding**. Each chapter sets its own
`--sec-y`, so the page has tempo instead of pagination — the Gap is
investigative and tight, How We Work is wide and procedural, Ownership is a
poster, the Atlanta band is a single breath. Adding a section means choosing
its own value rather than inheriting one.

### Never commit

No API keys, tokens, form secrets or credentials belong in this repository.
If the contact endpoint eventually needs a secret, put it behind a serverless
function and supply it as an environment variable.

---

## Updating project imagery

Every project image is a real screenshot of a live client site. None are
mockups, renders or stock photography.

| Project | Live site |
|---|---|
| Trademark Design Painting & Decorating | https://www.trademarkdp.com/ |
| Visionary Construction | https://visionary-pro.com/ |
| Linework Studio | https://linework.studio/ |
| Helio | https://www.gethelioapp.com/ |

There are two sets, and they do different jobs.

### `-wall.webp` — the Selected Work exhibition (V2.4)

One per project, and **the art direction lives in the capture, not in the CSS
crop**. Each region was chosen for what that project actually proves, and
clipped so nothing is chopped at an edge — no client hero headlines, no half
CTA buttons, no stray text slivers:

| File | What it shows | Ratio |
|---|---|---|
| `trademark-wall.webp` | the Our Work project grid — six completed jobs, each with a photograph and its service / scope / location | 1.011 |
| `visionary-wall.webp` | the full-bleed terrace band — paver terrace, granite stair treads, boulder retaining walls, lit at dusk | 2.685 |
| `linework-wall.webp` | the design-packet catalogue — four packets as floor plan, elevation and 3D view | 1.832 |
| `helio-wall.webp` | three live interface surfaces and the CSV export bar | 1.399 |

The wall frame is sized from the image's own ratio via `--ar` on `.wall-art`,
so the picture is displayed as it was composed rather than squeezed into a
frame and sliced by `object-fit:cover`. **`--ar` must sit on `.wall-art`, not
on the `<img>`** — the spacer that sets the frame height is `.wall-art::after`,
and a property declared on the image is inherited downward, never up to its
parent. Getting that wrong silently falls back to the default ratio.

`--arm` / `--zm` / `--opm` (also on `.wall-art`) are the phone framing: below
900px each wall zooms into ONE legible piece of the same capture — a single
project card, a single design packet, a single interface card — because a
six-card grid rendered 390px wide is a texture, not evidence.

If a site ships a redesign, recapture that region, keep the filename, and
**update both the `width`/`height` attributes and `--ar`** so the frame still
matches the picture.

### `-detail.webp` / `-mobile.webp` — the case-study dialogs

Unchanged. These are the two-up gallery inside each `<dialog class="cs">`.

### `-home.webp` — currently unreferenced

The original homepage captures. Nothing loads them since V2.4 replaced the
project portals with the exhibition, so they cost nothing at runtime. They are
kept as the record of each site's homepage at capture time. Delete them if you
want the repository smaller; nothing will break.

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
