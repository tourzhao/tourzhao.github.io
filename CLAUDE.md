# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the **personal academic website of Prof. Tuo Zhao** (Georgia Tech, ISyE & CSE). It is a static HTML/CSS/JS site — there is no build system, bundler, or server-side code. Files are served directly as-is.

## Site Structure

- **`index.html`** — Main homepage. Contains: bio/banner, recent news, preprints, publications, teaching, students, grants, and footer. This is the largest and most frequently updated file (~84 KB, hand-edited HTML).
- **`News.html`** — Full archive of past news items.
- **`Publications.html`** — Extended publications list.
- **`Software.html`** — Software packages page.
- **`seminars.html`** / **`others.html`** — Seminar and miscellaneous pages.
- **`III1717916/`** — NSF grant project pages (`proj1_nonconvex.html` through `proj21_opt.html`, plus `index.html` and `source.html`). Uses its own `w3.css` stylesheet.
- **`Publications/`** — PDF files of selected publications.
- **`posters/`**, **`pictures/`**, **`Seminars/`** — Static assets (images, poster PDFs, seminar materials).

## Stylesheets

- **`main_style.css`** — Primary theme stylesheet (Weebly-originated theme with `wsite-*` class conventions).
- **`sites.css`** — Large base stylesheet (also Weebly-originated).
- **`css.css`** — Minimal file that loads the "Actor" Google Font via `@font-face`.
- **`fancybox.css`** / **`jquery.fancybox.css`** — Lightbox plugin styles.

## JavaScript

- **`jquery.js`** — jQuery library.
- **`main.js`** — Weebly site framework JS (handles menus, theme behavior). Both loaded with `defer`.

## Key Conventions

- The site was originally exported from Weebly, so HTML uses Weebly class names (`wsite-theme-light`, `wsite-header`, `wsite-multicol`, `wsite-button`, `wsite-nav-*`, etc.) and the associated CSS/JS framework.
- Content is all inline HTML — no templating engine. Updates to publications, news, and student listings are made by directly editing `index.html`.
- Publication entries follow a consistent pattern: bold title, author list (with `<sup>‡</sup>` for advisees, `<sup>*</sup>` for equal contribution, `<sup>#</sup>` for alphabetical order), venue in bold italic, and an arXiv link.
- News items are `<li>` entries inside `<ul>` with bold date followed by description.
- The page width is fixed at 900px (`#page { width: 900px; max-width: 100%; }`).

## Development

There is no build step. To preview changes, open the HTML files directly in a browser or use any local HTTP server:

```
python3 -m http.server 8000
```

## Editing Tips

- When adding a new publication, prepend it to the relevant `<ul>` section in `index.html` (Preprints or Recent Publications). Follow the existing `<li>` pattern exactly.
- When adding news, prepend new `<li>` entries to the news `<ul>` in `index.html`. Move older items to `News.html` periodically.
- `index_bak2.html` and `index_short.html` are older backup/variant versions of the homepage — not actively served.
