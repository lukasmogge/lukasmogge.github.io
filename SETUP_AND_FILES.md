# Lukas Mogge — Website handbook

This is your own notes file for the al-folio v1 site at `lukasmogge.github.io`.
It explains what each custom file does, where it belongs, and how the whole
setup (git, VS Code, Codespaces) fits together. Keep a copy in your knowledge
base and update it as the site changes. It is NOT published — exclude it from
the build (see the `_config.yml` note below) or just keep it out of the repo.

---

## 1. How the whole thing is wired (the mental model)

- **The theme is a set of gems, not files in your repo.** al-folio v1 is a
  "thin starter": the layouts, styling and logic live in the `al_folio_core`
  gem (and sibling gems). Your repo holds only *content* and *overrides*. This
  is why you can't find the theme's CSS in your repo — it's inside the gem at
  `/usr/local/bundle/gems/al_folio_core-<version>/`.
- **You customise in three ways, in order of preference:**
  1. `_config.yml` settings and `_data/*.yml` — the safe, update-proof way.
  2. Content files — `_pages/*.md`, `_projects/*.md`, `_bibliography`, your bio.
     Also safe; these are yours, updates never touch them.
  3. **Overrides** — copying a gem file into your repo (same path/name) and
     editing it. Your copy wins. This is the fragile route: when the gem
     updates, your copy can drift from the new original and may need
     re-reconciling. Use only when 1 and 2 can't express the change.
     Colour and fonts are the two overrides we use.
- **The build output (`_site/`) is generated — never edit it.** Same for
  anything the gem compiles (e.g. `assets/css/main.css`).

### The "is this change safe?" test
If a change only edits *your* files (`_config.yml`, `_data/`, `_pages/`,
`_projects/`, your bio, assets) → safe, update-proof.
If it needs copying/editing a *gem* file (anything under `_sass/` that mirrors
a gem file) → override; note it, and expect to re-check it after a gem upgrade.

### Updates
You are NOT obliged to update the gems. A pinned version keeps working
indefinitely on GitHub Pages. Update only to get new features/fixes. When you
do, run `bundle exec al-folio upgrade audit` — it flags your overrides
(currently `_sass/_themes.scss`, later `_sass/_typography.scss`) so you can
re-check them against the new gem versions.

---

## 2. The files in this package — what each is and where it goes

| File (in this package)        | Goes in repo at         | Status | Purpose |
|-------------------------------|-------------------------|--------|---------|
| `_config.yml`                 | `/_config.yml`          | ready  | Site settings (done + a few additions marked `# LM:`) |
| `_sass/_themes.scss`          | `/_sass/_themes.scss`   | done   | Colour override — navy accent. Already working. |
| `_sass/_typography.scss`      | `/_sass/_typography.scss` | DRAFT | Font override (Source Serif 4 body + Source Sans 3 headings). Needs gem check — see notes. |
| `_pages/about.md`             | `/_pages/about.md`      | ready  | Landing page (bio, photo, contact). |
| `_pages/cv.md`                | `/_pages/cv.md`         | ready  | CV page — links to your PDF. |
| `_pages/research.md`          | `/_pages/research.md`   | DRAFT  | The Research page (projects collection, topic-grouped). Needs gem check. |
| `_projects/_TEMPLATE.md`      | `/_projects/<slug>.md`  | ready  | Copy once per research piece. One file = one card. |
| `_projects/example-temperature.md` | `/_projects/...`   | example | A filled-in example so you see the shape. |
| `content/website-texts.docx`  | (knowledge base only)   | ready  | Your drafting doc for all site prose. NOT in the repo. |

Files we do NOT ship (you edit in place, just once):
- `_pages/publications.md`, `_pages/teaching.md`, `_pages/books.md`,
  `_pages/blog.md`, `_pages/projects.md` (the demo one), the "dropdown"/submenu
  pages → set `nav: false` in each so they leave the navbar. (You've largely
  done this.) We don't keep copies of these; just remember the step.

---

## 3. Per-file detail

### `_config.yml`
Mostly done already. New/least-obvious bits are marked with `# LM:` comments:
- `search_enabled: false` (you turned off the ⌘K search)
- `enable_darkmode: false` (you removed the light/dark toggle)
- the `google_fonts` line has Source Serif 4 + Source Sans 3 added (the "load"
  half of the font change — see fonts below)
- `scholar:` name fields set to you (was Einstein)
Leave the `al_folio:`, `plugins:`, `theme:` and `third_party_libraries:` blocks
alone — that's the build machinery.

### `_sass/_themes.scss` — COLOUR (done, working)
A verbatim copy of the gem's `_themes.scss` with two lines changed in `:root`:
```
--global-theme-color: #1f3a5f;   /* navy — links, active nav, headings */
--global-hover-color: #16293f;   /* darker navy — hover */
```
To change colour later: edit those two hex values, save, preview, push. Nothing
else. If you ever upgrade the gem, re-copy its new `_themes.scss` and re-apply
these two lines (that's the override tax).

### `_sass/_typography.scss` — FONTS (DRAFT — needs one gem check)
Two-font "serious journalism" pairing: **Source Serif 4** for body text,
**Source Sans 3** for headings/nav. Two linked steps:
1. LOAD (done in `_config.yml` `google_fonts` line) — fetches the fonts.
2. USE (this file) — tells the theme to apply them.
OPEN QUESTION we must resolve against the live gem before this is final:
the theme's base font is set in `assets/tailwind/app.css` line 36
(`font-family: Roboto, sans-serif;`), NOT in a Sass variable we found. So the
correct override file/hook still needs confirming. Next session: view lines
30–40 of the gem's `app.css` and the top of the gem's `_typography.scss`, then
we finalise this file. Treat the shipped version as a placeholder.

### `_pages/about.md` (ready)
Your landing page. Bio in the body; photo via `image:` (points to
`assets/img/RWI_Lukas-4553.jpg`); `social: false`, news/posts off. Edit prose in
the Word doc, paste here.

### `_pages/cv.md` (ready)
Links to a PDF CV you upload to `assets/pdf/cv.pdf`. The `cv_pdf:` front-matter
field makes the download button appear. Replace the PDF anytime by overwriting
that file. (We can also do the fancy rendered-from-data CV later; this is the
simple, robust version.)

### `_pages/research.md` + `_projects/` (DRAFT — needs gem check)
Your single Research page: curated, grouped by topic, free-format, includes
unpublished work. Built on the **projects collection** — one Markdown file per
research piece in `_projects/`, each its own card (figure + heading + however
much text you want + links). `research.md` is the page that lists them; the
topic grouping uses the `category` field in each project file.
OPEN QUESTION: how the gem's projects layout actually renders a card (figure
left / text right, like your reference) — confirm next session by viewing the
gem's `projects` layout, then we adjust. The TEMPLATE and example show the
intended front-matter.

---

## 4. Writing a new research piece (once Research is set up)
1. Copy `_projects/_TEMPLATE.md` to `_projects/my-slug.md` (slug = short name,
   lowercase-hyphens, becomes the URL).
2. Fill the front-matter: `title`, `category` (your topic), `importance`
   (controls order), `img` (thumbnail in `assets/img/`), link fields.
3. Write the body text in Markdown — as much or as little as you like.
4. Add the figure to `assets/img/`. You provide these.
5. Preview, commit, push.

---

## 5. The Word drafting doc (`content/website-texts.docx`)
Source of truth for all site *prose*: bio, each research description, CV intro,
any page text. Rule to avoid drift: **edit text in the doc, then copy into the
site files. Never edit prose directly in the site files.** Keep the doc in your
knowledge base; it is not part of the repo.

---

## 6. Environment setup (reference)

### The three places your repo lives
- **GitHub** (`github.com/lukasmogge/lukasmogge.github.io`) — the source of
  truth. Pushing here triggers the deploy.
- **Your laptop** (Git Bash / VS Code) — for quick edits. No local preview
  (no admin rights → no Ruby/Jekyll locally).
- **Codespaces** — cloud dev machine, gives the live preview. This is the
  workshop for anything visual (colour, fonts, layout).

Pick ONE place per work session to avoid sync confusion. Before editing in
either, `git pull` first. After finishing, commit + push.

### Codespaces workflow
1. Open the repo's Codespace (github.com/codespaces or the repo's Code button).
2. `git pull` to get the latest.
3. `bundle exec jekyll serve` → click the forwarded-port popup to preview.
4. Edit → save → the preview auto-rebuilds (a few seconds).
   EXCEPTION: `_config.yml` changes and `git pull` need a server restart
   (`Ctrl+C`, then `bundle exec jekyll serve` again).
5. Commit + push when happy.
6. Stop (don't delete) the Codespace when done — it suspends itself after 30
   min idle. Delete only to reclaim storage, and only after pushing.
   NOTE: the gem's dev server assumes baseurl `/al-folio`; your live site uses
   blank baseurl (correct). Preview at the URL the server prints.

### Git basics (Git Bash or VS Code terminal)
```
git pull                      # get latest before editing
git add -A                    # stage all changes
git commit -m "message"       # save a snapshot
git push                      # send to GitHub → triggers deploy
git status                    # see what changed / confirm clean
```

### Deploy
Pushing to `main` triggers the `deploy` GitHub Action, which builds with the
full gem toolchain and publishes to the `gh-pages` branch. Settings → Pages is
set to serve from `gh-pages` (root). Live a few minutes after push. The other
red Actions (Lighthouse, Prettier, broken-link, Docker) are optional extras you
can ignore or delete later.

---

## 7. Later: being found better (SEO) — noted, not done yet
When ready: turn on `serve_og_meta` / `serve_schema_org`, write a real
`description`, add Google Search Console verification, ensure the sitemap is
generated (it is, via jekyll-sitemap), and make sure each page has sensible
title/description. Low effort, worth doing once the content is in place.
