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
├── .gitignore
└── README.md
```

There is **nothing to compile**. `public/index.html` is the whole application.
This is a pure static site — there is deliberately no `wrangler.toml`,
`package.json`, or build tooling (see *Troubleshooting* below for why).

---

## Run it locally

Any of these work — pick one:

```bash
# 1. Simplest: just open the file in a browser
open public/index.html          # macOS  (or double-click the file)

# 2. Serve it (matches production paths)
npx serve public                        # then visit the printed URL
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
2. **Cloudflare dashboard** → **Workers & Pages** → **Create** →
   **Pages** → **Connect to Git**, and pick this repository.
3. Set the build configuration **exactly** like this:

   | Setting | Value |
   |---|---|
   | Framework preset | **None** |
   | Build command | *(leave completely empty)* |
   | Build output directory | **`public`** |

4. **Save and Deploy.** You get a `https://<project>.pages.dev` URL. Every
   push to `main` re-deploys; pull requests get preview URLs.

> The three settings above are the whole game. Because there is no build
> command, Cloudflare simply publishes the files in `public/` — it cannot
> "fail to build" when there is nothing to build.

### Option B — Wrangler CLI (deploy from your machine)

No config file needed — pass the output folder and project name as flags:

```bash
npm install -g wrangler
wrangler login
wrangler pages deploy public --project-name=interior-life-mapping
```

### Custom domain

Pages project → **Custom domains** → **Set up a domain**
(e.g. `assessment.yourdomain.com`).

---

## Troubleshooting

**"Missing entry-point to Worker script or to assets directory" / build fails
immediately.**
This happens when a `wrangler.toml` is present in a *Pages* repo: Cloudflare's
Git builder switches into "build with Wrangler configuration" mode, treats the
project as a **Worker**, looks for a script entry-point, and fails because a
static site has none. **Fix:** there must be no `wrangler.toml` in the repo
(this package ships without one). If you added one, delete it, commit, and
re-deploy.

**"Output directory 'public' not found" or the deployed page is the README /
a 404.**
The **Build output directory** in the Pages settings is wrong. Set it to
`public` (Settings → Builds & deployments → Build configuration), then
**Retry deployment**.

**Build tries to run `npm install` / a framework build.**
Set **Framework preset: None** and clear the **Build command** field entirely.
Retry.

**Fonts don't load / a security warning in the console.**
The Content-Security-Policy in `public/_headers` already allows Google Fonts.
If you host the fonts elsewhere, update the `style-src` / `font-src` lines.

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
re-run the `wrangler pages deploy` command (Option B).

---

## Notes

- **Scoring formula:** the original paper form printed "÷ 7 × 5," which would
  score the lowest answers highest. This app uses the **item mean (raw ÷ 7)**,
  which is what the 1.0–5.0 reference bands actually describe.
- **No LLM / no server:** all scoring runs in the visitor's browser. Cloudflare
  only serves the static file; nothing is sent anywhere.
- **Fonts** load from Google Fonts (Fraunces + Hanken Grotesk) with system
  fallbacks, so the page still renders cleanly if fonts are blocked.

---

*Interior Life Mapping* and its items are © Winston H.K. Chew, companion to
*Emotionally Whole: From the Mirror to the Door*. This instrument is a
conversation starter, not a diagnostic.
