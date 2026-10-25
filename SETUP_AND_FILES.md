# Lukas Mogge — Website handbook

Notes file for the al-folio v1 site at `lukasmogge.github.io`. Explains what each
custom file does, where it belongs, and how the setup (git, VS Code, Codespaces)
fits together. Keep a copy in your knowledge base and update it as the site
changes. It is in the repo but excluded from the build (see `_config.yml`
`exclude:`), so it is not published.

Gem version at last edit: **al_folio_core 1.0.15**.

---

## 1. Mental model (how it is wired)

- **The theme is a set of gems, not files in your repo.** al-folio v1 is a
  "thin starter": layouts, styling and logic live in the `al_folio_core` gem
  (and siblings), at `/usr/local/bundle/gems/al_folio_core-<version>/`. Your
  repo holds only content and overrides.
- **Three ways to customise, in order of preference:**
  1. `_config.yml` settings and `_data/*.yml` — safe, update-proof.
  2. Content files — `_pages/*.md`, `_projects/*.md`, your bio. Safe; yours.
  3. **Overrides** — copying a gem file into your repo (same path/name) and
     editing it. Your copy wins. Fragile across gem upgrades. Used for colour,
     fonts, and (via a custom page) the research layout.
- **Build output (`_site/`) is generated — never edit it.** Same for the
  compiled `assets/css/main.css`.

### "Is this change safe?" test
Only edits *your* files (`_config.yml`, `_pages/`, `_projects/`, bio, assets) →
safe. Needs copying/editing a *gem* file (anything mirroring `_sass/…`) →
override; note it, re-check after a gem upgrade.

### Updates
You are NOT obliged to update the gems. A pinned version keeps working. When you
do update, run `bundle exec al-folio upgrade audit` — it flags your overrides
(`_sass/_themes.scss`, `_sass/_typography.scss`) so you can re-check them against
the new gem versions.

---

## 2. What is DONE (state of the site)

- **Landing page** (`_pages/about.md`) — bio, photo (`RWI_Lukas-4646.jpg`),
  contact line. Social icons, news, posts all off.
- **Navbar** — About / research / CV. All other demo pages set `nav: false`.
- **Colour** — navy accent via `_sass/_themes.scss` override. Dark-mode toggle
  off (`enable_darkmode: false`).
- **Font** — Source Serif 4, self-hosted, via `_sass/_typography.scss` override +
  `google_fonts` line in `_config.yml`. No serif/sans split (all serif).
- **Self-hosted fonts / GDPR** — `third_party_libraries: download: true`, so the
  build fetches fonts and serves them from your own domain (no Google contact at
  page load). Confirmed working.
- **CV** — navbar "CV" redirects straight to the PDF
  (`assets/pdf/CURRICULUM_VITAE_RWI_Mogge.pdf`) via `_pages/cv.md`
  (`layout: null` + meta-refresh + fallback link). The PDF is currently a
  placeholder (full RWI CV); replace the file (same name) to update.
- **Research page** — custom layout (`_pages/research.md`), described below.
- **Search** off (`search_enabled: false`).

---

## 3. The research page (custom — the important bit)

Your Research page does NOT use al-folio's stock projects cards (those are
clickable boxes that open a separate detail page — not what you wanted). Instead
`_pages/research.md` holds a **custom layout**: a Liquid loop + inline `<style>`
that renders each piece as **full-width title → authors → journal-or-status →
figure-left / text-right (abstract) → links**, grouped by topic. No cards, no
click-through. This matches the "journalism / Aguilar-Gómez" look.

How it works:
- `display_categories` (front-matter) lists the topic groups, in order. Each must
  EXACTLY match the `category:` in the project files.
- `category_intros` (front-matter) optionally maps a category to an intro
  paragraph shown under its heading (currently used for the meta-science group).
- The loop pulls `site.projects`, filters by category, sorts by `importance`.
- Images use `{% include figure.liquid … %}` so they go through the responsive
  `.webp` pipeline (do NOT hand-write `<img>` — it would bypass that).
- The `<style>` block controls: title size (`1.4rem`), status black vs authors
  grey, 38% figure / rest text, and mobile stacking (figure above text under
  768px).

### One research piece = one file in `_projects/`
Front-matter fields:
- `title` — full paper title; include the year in `(…)` for PUBLISHED pieces;
  no year for unpublished.
- `authors` — e.g. "With A, B, and C" (rendered grey).
- `journal` — the journal name, for published pieces (rendered *italic*, black).
- `status` — for unpublished pieces instead of `journal` (rendered roman,
  black). Renders as HTML, so you can italicise a venue inside it with `<em>…</em>`
  and write anything: "Working paper", "Work in progress",
  "Ruhr Economic Paper #1201", "Under review at <em>Geneva Papers</em>",
  "Accepted at <em>Journal of Economic Literature</em>". Use `journal` OR
  `status`, not both.
- `img` — the figure, in `assets/img/` (optional; text goes full-width if absent).
- `importance` — order within the topic group (lower = higher up).
- `category` — one of the exact `display_categories` strings.
Body (below front-matter): the abstract, then a links line, e.g.
`[Journal article](URL) · [Replication package](URL)`.

### Adding / changing a research piece
1. Copy an existing `_projects/*.md` to a new slug (lowercase-hyphens).
2. Fill the front-matter; write the abstract + links in the body.
3. Add any figure to `assets/img/` (resize to ~1600px wide first — see §5).
4. Preview, commit, push. To reorder, change `importance`. To re-theme a topic,
   edit `display_categories` (and the matching `category:` in each file).

Current pieces (9): perception-protection, anticipatory-cash-transfers,
charity-hazard, allocation-humanitarian-aid, stated-realized-demand-vietnam
(Weather risk…); heat-violence-children (Temperature effects);
researcher-variation, manydaughters, robustness-replicability (Meta science…).

---

## 4. The overrides (fragile files — re-check after gem upgrade)

- `_sass/_themes.scss` — COLOUR. Verbatim copy of the gem's file with two lines
  changed in `:root`: `--global-theme-color: #1f3a5f` and
  `--global-hover-color: #16293f`. To change colour: edit those two hex values.
- `_sass/_typography.scss` — FONT. Verbatim copy of the gem's file with one added
  `body { font-family: "Source Serif 4", Georgia, serif; }` rule near the top
  (marked `LM`). To compare/change fonts: edit that one line.
- `_pages/research.md` — the custom research layout (loop + CSS). Not a gem-file
  shadow, but it is custom logic worth keeping a knowledge-base copy of.

If a gem upgrade changes the originals, re-copy the gem's new `_themes.scss` /
`_typography.scss` and re-apply the marked lines.

---

## 5. Figures (for the research page)

- Export from Stata as PNG (`graph export fig.png, width(2000)`), or
  PowerPoint → PDF → PNG at 300 DPI (avoids PowerPoint's blurry PNG export).
- Resize to ~1600px wide before adding (Paint: Resize → Pixels → 1600 wide,
  keep aspect ratio → Save as PNG). Keeps files small; the build then generates
  responsive `.webp` sizes automatically.
- Put the web copy in `assets/img/`; keep the high-res original elsewhere for
  print.
- Mixed aspect ratios are fine — the layout handles varying heights. Solve any
  layout issue in the CSS, never by padding image files.

---

## 6. The Word drafting doc (`website-texts.docx`)
Source of truth for all site PROSE: bio, each research abstract, statuses.
Rule to avoid drift: **edit text in the doc, then copy into the site files.
Never edit prose directly in the site files.** When you refine a working-paper
status (e.g. add a Ruhr Economic Paper number), draft it in the doc, then copy to
the project file's `status` field. Keep the doc in your knowledge base.

---

## 7. Environment & workflow

### Three places the repo lives
- **GitHub** — source of truth; pushing triggers the deploy.
- **Laptop** (Git Bash / VS Code) — quick edits, NO local preview (no admin →
  no Ruby/Jekyll).
- **Codespaces** — cloud dev machine WITH live preview. The workshop for anything
  visual. Connect from VS Code desktop ("Codespaces: Connect to Codespace…") to
  avoid the browser copy-paste problems.

Pick ONE place per session. `git pull` BEFORE editing; commit + push after.

### Codespaces loop
1. Connect from VS Code desktop (bottom-left shows green "Codespaces").
2. `git pull` first.
3. `bundle exec jekyll serve` → open the forwarded port (Ports tab / popup).
4. Edit → save → preview auto-rebuilds. EXCEPTION: `_config.yml` changes and
   `git pull` need a server restart (`Ctrl+C`, then serve again).
5. Use a SECOND terminal for git/grep so the server keeps running in the first.
6. Commit + push. Stop (don't delete) the Codespace when done; it auto-suspends.
   The "directory already being watched" warnings on serve are harmless.

### Git basics
```
git pull                      # before editing
git add -A                    # stage all (includes deletions!)
git commit -m "message"       # note the -m
git push                      # triggers deploy
git status                    # confirm clean
```
The LF→CRLF warnings on Windows are harmless. An "Auto update markdown TOC"
commit appearing on pull is a GitHub Action, not you.

### Deploy
Push to `main` → the `deploy` Action builds (full gem toolchain, fetches
self-hosted fonts) and publishes to `gh-pages`. Settings → Pages serves from
`gh-pages` (root). Live a few minutes after push. Other red Actions (Lighthouse,
Prettier, broken-link, Docker) are optional extras — ignore or delete.

---

## 8. Open / later

- **CV font** — your Word CV is Georgia; the site is Source Serif 4. They don't
  match (both serif, different faces). To match exactly, install Source Serif 4
  into Word, or accept the near-match. Replace `assets/pdf/CURRICULUM_VITAE_RWI_Mogge.pdf`
  with the final CV (same filename) when ready.
- **Richer statuses** — add discussion-paper numbers etc. to `status` fields as
  papers progress (draft in the doc first).
- **Figures for more pieces** — only 2 of 9 have figures; add more when you have
  them (just set `img:`).
- **SEO / being found** — later: turn on `serve_og_meta` / `serve_schema_org`,
  write a real `description`, add Google Search Console verification. The sitemap
  is already generated (jekyll-sitemap).
