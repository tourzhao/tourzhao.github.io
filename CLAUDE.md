# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal academic website of **Prof. Tuo Zhao** (Georgia Tech, ISyE with a courtesy appointment in CSE). Static HTML/CSS/JS — no build system, bundler, package manager, or tests. Files are served as-is.

The repo is the GitHub Pages source for `https://tourzhao.github.io/` (remote: `tourzhao/tourzhao.github.io`). **Pushing to `main` publishes the live site** — there is no staging environment or CI gate.

Preview locally by opening `index.html` directly, or:

```
python3 -m http.server 8000
```

## Architecture

**`index.html` (~119 KB) is the entire site.** Everything — bio banner, news, preprints, publications, software, awards, current students, alumni, "About Alchemy", teaching, NSF projects, contact — lives in this one hand-edited file. Nearly all work in this repo means editing it.

The multi-page structure the site once had (`News.html`, `Publications.html`, `Software.html`, `seminars.html`, `others.html`, the `III1717916/` NSF project pages) **no longer exists.** Those pages were consolidated into `index.html`; the overflow content they held is now behind the collapsible sections described below. Don't try to link to them or restore content from them.

Supporting files:

- `redirect.html` — meta-refresh stub pointing at `https://tourzhao.github.io/`, for the retired Weebly URL.
- `index_bak_20260314.html` — dated backup, **gitignored** (`.gitignore` matches `index_bak*.html`). Not served.
- `posters/`, `Publications/`, `pictures/` — poster PDFs, publication PDFs, banner images. `Seminars/` is gitignored.
- Root PDFs are linked directly from `index.html`: `Ads.pdf` (recruiting ad), `Yann_Response.pdf`, `Ali_Response.pdf`, `TZ.jpg` (portrait).

### Weebly inheritance

The site was exported from Weebly, so `sites.css` (135 KB) and `main.js` (227 KB) are vendored framework files, and the markup uses `wsite-*` class names (`wsite-theme-light`, `wsite-multicol`, `wsite-button`, `wsite-nav-*`). `index.html` still sets `IS_ARCHIVE = 1` and calls `initPublishedFlyoutMenus`. **Treat `sites.css` and `main.js` as vendored — don't edit or reformat them.** Site-specific overrides go in `main_style.css`; page-specific CSS goes in the inline `<style>` block in `index.html`'s `<head>`.

Stylesheet roles: `main_style.css` (theme overrides, and the 900px page width at `main_style.css:17`), `sites.css` (vendored base), `css.css` (loads the "Actor" Google Font), `fancybox.css` / `jquery.fancybox.css` (lightbox).

Analytics are inline at the bottom of `index.html`: Google Analytics (`UA-30301817-1`) and StatCounter (project `11145196`).

### Collapsible "Show more" sections

Three sections — News, Preprints, Publications — show recent entries and hide the long tail. Each uses the same hand-rolled pattern, with **no shared helper**, so a change to one must be replicated in the other two:

1. A `<div id="{news,preprints,pubs}-older" class="collapsible-content">` wrapping the older `<li>` entries.
2. A `.toggle-btn` anchor with an inline `onclick` that toggles `.open` on both the div and the button and swaps the label between "Show more" / "Show less".
3. An IIFE right after the button using an `IntersectionObserver` to auto-collapse the section once the button scrolls above the viewport.

The IDs are load-bearing — the inline `onclick` and observer both look them up by `getElementById`. When adding a section, copy all three parts and rename consistently.

**The wrapper div sits *inside* the `<ul>`,** between a `</li>` and the next `<li>` (see `index.html:210`, `272`, `490`). This is invalid HTML that browsers tolerate, and it's what makes the collapse animate as one block. Preserve it; "fixing" the nesting breaks the layout.

**`.collapsible-content.open` is capped at `max-height: 5000px`** (`index.html:33`). A CSS height transition can't animate to `auto`, hence the magic number. The Publications list has 72 hidden entries, each forced to at least 3 lines (title / authors / venue), which puts its expanded height in the neighborhood of the cap. If the bottom of an expanded section appears clipped, raise the cap rather than restructuring.

## Content conventions

Sections are `<div class="paragraph">` blocks opened by a header of the form:

```html
<br><font><font size="4"><strong><font color="#808080">Section Name</font> </strong></font></font>
```

Entries are `<li>` items separated by the literal pattern `<br></li><li>` — a `<br>` *before* the closing tag, which supplies the inter-item spacing. Match it exactly; a plain `</li><li>` renders too tight.

The **first `<li>` of each `<ul>`** wraps its content in `<span class="txt-normal">` (or the equivalent inline `style="font-size: 1em; line-height: 1.5;"`). This establishes the font context Weebly's CSS otherwise inherits wrongly for the whole list, so keep it on the first item when prepending a new entry. Do *not* add `txt-normal` around inner links or bold keywords — doing so leaks a green link color (fixed repeatedly in commits `0f726ba`, `9a82f3f`, `56e5619`).

Publication entries follow: bold title in `<font color="#2a2a2a">`, author list with `Tuo Zhao` in `<font color="black">`, venue in `<strong><em>`, year, then a bracketed `[arXiv]` / `[PDF]` / `[Link]` / `[Software]` link. Author markers: `<sup>&#8225;</sup>` (‡) advisees, `<sup>*</sup>` equal contribution, `<sup>#</sup>` alphabetical order — the legend in each section header must stay consistent with the markers used.

News items lead with a bold date (`<strong><font color="#2a2a2a">Feb. 2026</font></strong>:`) in `Mon. YYYY` form, newest first.

External links consistently carry `target="_blank" rel="noopener noreferrer"`.

**Use straight ASCII quotes in HTML attributes.** Curly quotes (`href=”…”`) silently produce dead links — two such broken links currently exist in the news list at `index.html:188` and `index.html:190`. This is the most likely failure mode when pasting content from a document or chat.

### Where to add new content

Prepend to the visible portion of the relevant `<ul>` (above the `-older` wrapper div), and move the oldest visible entry down into the wrapper to keep the visible count steady. When a preprint is accepted, move its `<li>` from Preprints to Publications and add the venue and year — see commit `9da8b1c` for the shape of that edit.

Group listings distinguish current members ("Alchemists in My Group", with `Ph.D. Student` / `Ph.D. Candidate` and a `(2022.8--Present)` date range) from "FLASH Alumni" (closed date range plus a `Current Position:` line). Graduating a student means moving the entry between these two lists.

Both lists use a two-column flex layout so that every continuation line (`Current Position:`, a prior degree, `Former Visiting Student`) aligns with the text after that entry's `--`:

```html
<li><span class="member"><span class="member-name">LINK&nbsp;--&nbsp;</span><span class="member-detail">Ph.D. …, Georgia Tech (…)<br>Current Position: …</span></span>
```

The `--` and its surrounding `&nbsp;` belong **inside** `.member-name`, and every continuation line is a `<br>` **inside** `.member-detail` — the alignment comes from the column edge, so don't add per-entry `margin-left` or `&nbsp;` padding to indent a line. `.member` falls back to `display: block` under 700px (CSS in the inline `<style>` block) so narrow screens don't get a squeezed detail column.

**These two lists are the exception to the `<br></li><li>` separator rule** — they use a plain `</li><li>`. Because `.member` is a block-level flex box, a trailing `<br>` lands *after* that box and renders as a full blank line under every entry. Entry spacing here comes from `div.paragraph li { margin: 3px 0 0 }` instead, which reproduces the same rhythm as the other lists.

## Commit conventions

Single-purpose commits with an imperative summary naming the affected content, e.g. `Add 5 ICML 2026 papers, reorder publications: ICML before ACL`, `Update alumni positions: Tianyi Liu (Meta), Ethan Wang (Anthropic)`, `Fix news chronological order: Feb 2026 before Jan 2026`. Since a push goes live immediately, only commit or push when asked.
