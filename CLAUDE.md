# Personal site (al-folio)

This is the GitHub Pages site for Deepkumar Pambhar — an [al-folio](https://github.com/alshedivat/al-folio) Jekyll theme rendered selectively from `../context/`. It's its own git repo (`github.com:pambhar-deepkumar/pambhar-deepkumar.github.io`), a compartment of the broader [branding workspace](../CLAUDE.md). User-site mode → lives at the root URL **https://pambhar-deepkumar.github.io/**, no subpath.

al-folio's bundled docs live in `AGENTS.md`, `INSTALL.md`, `CUSTOMIZE.md`, `FAQ.md`, `TROUBLESHOOTING.md`, etc. — read those for theme-specific mechanics. This file covers what's specific to _this_ site and _this_ workflow.

## Source of truth

`../context/` is the archive. The site renders from it. **Never invent content** — if you're asked to add a project, publication, role, or bio line, the right move is almost always:

1. Check whether the fact already exists in `context/`.
2. If not, surface it to the user and capture it there first.
3. Then come back to the site and render it.

This mirrors the parent rule: facts live in exactly one place (`context/`), and surfaces never own facts. Placeholder TODOs in this repo's data files are _not_ facts — they're scaffolding waiting for content that should originate in `context/`.

## Where things live

The site currently has TODO placeholders in every identity-bearing file. When you fill them in, source the values from `context/`:

- `_config.yml` — site title, description, keywords, contact note, blog name. **Do not touch `baseurl`** — it must stay `""` for user-site URLs to work.
- `_data/cv.yml` — RenderCV-compatible CV data (drives both the `/cv` page and the auto-generated PDF). Schema is restrictive: see "Auxiliary workflows" below.
- `_data/socials.yml` — social/contact icons. Currently only `rss_icon: true`; the rest are commented examples.
- `_data/repositories.yml` — pinned GitHub users + repos for the `/repositories` page.
- `_pages/about.md` — landing page copy + profile picture config.
- `_pages/cv.md` — `cv_pdf` path (currently empty placeholder).
- `assets/img/prof_pic.{jpg,png}` — still al-folio's bundled placeholder photos. Replace.
- `_bibliography/papers.bib` — empty; populate when there are publications worth showing.

Several pages (`profiles.md`, `teaching.md`, `books.md`) are scaffolded but `nav: false` — flip on when content lands.

## Build & deploy

Local preview: `bundle exec jekyll serve` (al-folio recommends Docker via `docker compose up`; either works). For non-trivial changes, **verify locally before pushing** — the deploy workflow takes ~1 minute on every push and the failure feedback loop is slower than `serve`.

Push to `main` triggers `.github/workflows/deploy.yml` which builds and deploys via the modern Pages flow (`actions/upload-pages-artifact` + `actions/deploy-pages`). Pages source is configured to "GitHub Actions" — _don't_ switch it back to "Deploy from a branch".

## Auxiliary workflows

al-folio bundles a handful of extras. Current state:

- **`render-cv.yml`** — generates a CV PDF from `_data/cv.yml` using [RenderCV](https://github.com/sinaatalay/rendercv). RenderCV's schema is stricter than al-folio's example: it rejects `cv.label`, `cv.image`, `cv.summary`, `cv.address`. If you reintroduce any of those keys it'll fail. Use `cv.sections` (e.g. a "Summary" section) for equivalent content.
- **`prettier.yml`** — runs `npx prettier . --check` on every push. Run `npx prettier . --write` from the repo root before committing. `CUSTOMIZE.md` is in `.prettierignore` because the bundled `update-tocs.yml` workflow auto-rewrites it in a non-prettier-formatted way after each push (chicken-and-egg).
- **`update-tocs.yml`** — auto-commits TOC updates to bundled markdown docs. Causes occasional push races: `git pull --rebase origin main` then re-push.
- **`lighthouse-badger.yml.disabled`** — **renamed to disable**. To re-enable: rename back to `.yml`, create a PAT secret called `LIGHTHOUSE_BADGER_TOKEN` (repo scope), and edit the workflow's `BRANCH` reference from `master` to `main`.
- **`broken-links*.yml`, `codeql.yml`, `axe.yml`, `docker-slim.yml`, `prettier-comment-on-pr.yml`, etc.** — al-folio bundled, mostly path-filtered. Leave unless they actively start failing.

## Identity

Local repo config is set to `pambhardeepkumar@gmail.com` / `Deepkumar Pambhar`. Don't override with `-c user.email=...` on commits — let the local config win. (See [feedback memory](../../.claude/projects/-Users-deep-personal-branding/memory/feedback_git_identity.md) for context.)

## Don't

- Don't fabricate bio/project/CV content. Pull from `context/` or capture there first.
- Don't enable `lighthouse-badger` without the PAT _and_ the `master`→`main` fix.
- Don't change `_config.yml` `baseurl` from `""`. User-site URLs break otherwise.
- Don't add the al-folio-rejected fields (`label`, `image`, `summary`, `address`) back to `_data/cv.yml`.
- Don't commit demo/placeholder photos as if they were real (the Einstein `prof_pic*` files are still placeholders).

## Open follow-ups

Tracked inline as `# TODO:` comments. The shortlist:

- Real CV data into `_data/cv.yml` (sourced from `context/`).
- Real socials into `_data/socials.yml`.
- Real pinned repos into `_data/repositories.yml`.
- Bio + subtitle in `_pages/about.md`.
- Replace `assets/img/prof_pic*` with an actual photo.
- `_pages/cv.md` `cv_pdf` path once a CV PDF exists.
- `_bibliography/papers.bib` if/when there are publications.
- Decide whether to set up a custom domain (no `CNAME` currently).
- Decide whether to disable `update-tocs` workflow to stop the push-race noise.
