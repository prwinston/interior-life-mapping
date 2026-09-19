# Interior Life Mapping

An online, domain-specific **emotional intelligence self-assessment** — a
biblical-EQ instrument that maps a person's interior life across five domains,
*from the mirror to the door*.

Companion resource to *Emotionally Whole: From the Mirror to the Door* by
**Winston H.K. Chew**.

- **35 statements**, five domains, about ten minutes.
- Items adapted from validated psychological instruments (cited in-app).
- Automatic scoring, reverse-item handling, a radar chart, an interior-life
  map, per-domain bands, and a clean **Print / Save-as-PDF** results page.
- **No backend, no build step, no tracking, no personal data collected.**
  One self-contained HTML file.

---

## The five domains

| # | Domain | Room | Anchor |
|---|--------|------|--------|
| 1 | Self-Awareness | The Mirror | Psalm 139:1 |
| 2 | Self-Regulation | The Furnace | Genesis 39:9 |
| 3 | Motivation | The Ruins | Nehemiah 1:4 |
| 4 | Empathy | The Border Crossing | Ruth 1:16 |
| 5 | Social Skills | The Door | Acts 16:15 |

Each domain score is the **average of its seven responses** on a 1.0–5.0 scale.
Reverse-worded items are scored inversely so a higher number always reflects
greater strength.

| Score | Status |
|-------|--------|
| 1.0 – 2.4 | Priority — formation work is needed here |
| 2.5 – 3.4 | Developing — present but inconsistent |
| 3.5 – 4.2 | Strength — operating well; maintain and deepen |
| 4.3 – 5.0 | Exceptional — a formation asset; ensure it serves others |

---

## Repository layout

```
.
├── public/            ← the site Cloudflare deploys (build output directory)
│   ├── index.html     ← the entire app (HTML + CSS + JS in one file)
│   └── _headers       ← security & caching headers for Cloudflare Pages
├── wrangler.toml      ← config for `wrangler pages deploy`
├── .gitignore
└── README.md
```

There is nothing to compile. `public/index.html` is the whole application.

---

## Run it locally

Any of these work — pick one:

```bash
# 1. Simplest: just open the file in a browser
open public/index.html          # macOS
# (or double-click public/index.html)

# 2. Serve it (recommended, matches production paths)
npx serve public                # then visit the printed URL
# or
python3 -m http.server 8000 -d public   # then visit http://localhost:8000
```

---

## Deploy to Cloudflare Pages

### Option A — Connect to GitHub (recommended, auto-deploys on push)

1. Create a repository on GitHub and push this project:
   ```bash
   git init
   git add .
   git commit -m "Interior Life Mapping assessment"
   git branch -M main
   git remote add origin https://github.com/<you>/interior-life-mapping.git
   git push -u origin main
   ```
2. In the **Cloudflare dashboard** → **Workers & Pages** → **Create** →
   **Pages** → **Connect to Git**, and select this repository.
3. Set the build configuration:
   - **Framework preset:** `None`
   - **Build command:** *(leave empty)*
   - **Build output directory:** `public`
4. **Save and Deploy.** Cloudflare gives you a `https://<project>.pages.dev`
   URL. Every push to `main` re-deploys automatically; pull requests get
   preview URLs.

### Option B — Wrangler CLI (deploy from your machine)

```bash
# One-time
npm install -g wrangler
wrangler login

# Deploy (wrangler.toml already points at ./public)
wrangler pages deploy
# or explicitly:
wrangler pages deploy public --project-name=interior-life-mapping
```

### Custom domain

In your Pages project → **Custom domains** → **Set up a domain**, add e.g.
`assessment.yourdomain.com`. If the domain's DNS is on Cloudflare, the record
is created for you.

---

## Customizing the instrument

Everything lives in `public/index.html`:

- **Items, verses, and bands** — the `DOMAINS` array near the top of the
  `<script>` block. Each item is `{ t: "statement", s: "citation" }`; add
  `r: true` to mark a reverse-scored item.
- **Scoring thresholds** — the `tierOf()` function.
- **Colours, fonts, spacing** — the CSS variables in `:root` at the top of the
  `<style>` block (`--ink`, `--brass`, the per-room hues, etc.).

After editing, refresh locally to confirm, then commit and push (Option A) or
re-run `wrangler pages deploy` (Option B).

---

## Notes

- **Scoring formula:** the original paper form printed "÷ 7 × 5," which would
  score the lowest answers highest. This app uses the **item mean (raw ÷ 7)**,
  which is what the 1.0–5.0 reference bands actually describe. Lowest possible
  score is 1.0 (Priority); highest is 5.0 (Exceptional).
- **Fonts** load from Google Fonts (Fraunces + Hanken Grotesk) with system
  fallbacks, so the page still renders cleanly offline or if fonts are blocked.
- **Privacy:** nothing is stored or sent anywhere. All scoring happens in the
  visitor's browser.
- To tighten the Content-Security-Policy further, move the inline `<style>`
  and `<script>` into separate `styles.css` / `app.js` files and drop
  `'unsafe-inline'` from `_headers`.

---

*Interior Life Mapping* and its items are © Winston H.K. Chew, companion to
*Emotionally Whole: From the Mirror to the Door*. This instrument is a
conversation starter, not a diagnostic.
