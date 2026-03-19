# CLAUDE.md — Elena Smirnova Portfolio

This file provides guidance for AI assistants working on this repository.

## Project Overview

A bilingual (English/German) personal portfolio website for Elena Smirnova, MSc — doctoral researcher, project manager, and feminist peace researcher. Deployed via GitHub Pages at `elenasmirnova84.github.io`.

## Repository Structure

```
elenasmirnova84.github.io/
├── index.html                        # Entire website (HTML + CSS + JS, single file)
├── photo.png                         # Profile photo displayed in hero section
├── CV_Elena Smirnova MSc ENG.pdf     # English CV (downloadable)
├── CV_Elena Smirnova MSc GER.pdf     # German CV (downloadable)
└── CLAUDE.md                         # This file
```

**No build tools, no package manager, no frameworks, no dependencies.** The site is pure vanilla HTML/CSS/JS in a single file.

## Architecture

### Single-File Design

Everything lives in `index.html`:
- `<style>` block: all CSS (~77 lines, minified)
- `<body>`: HTML shell with empty placeholder elements
- `<script>` block: content data + rendering logic

### Content Data Object (`C`)

All text content is stored in a single `const C` object at the top of the `<script>` block, split into two language keys:

```js
const C = {
  en: { nav, sub, inst, dlcv, about, res, experience, education, pub, med, sk, con, ft },
  de: { nav, sub, inst, dlcv, about, res, experience, education, pub, med, sk, con, ft }
}
```

**To update content, edit the corresponding field in `C.en` and/or `C.de`.** The `render()` function reads from `C[lang]` and populates the DOM.

### Sections and Their Data Keys

| Section | HTML `id` | Data key in `C[lang]` | Rendered by |
|---|---|---|---|
| Hero | `#hero` | `sub`, `inst`, `dlcv` | `render()` inline |
| About | `#about` | `about.h`, `about.t` | `render()` inline |
| Research & Teaching | `#research` | `res.diss`, `res.teach` | `render()` inline |
| Experience | `#experience` | `experience.exp[]` | `tlHTML()` |
| Education | `#education` | `education.edu[]`, `education.fur[]` | `tlHTML()` |
| Publications | `#publications` | `pub.pubs[]`, `pub.conf[]` | `render()` inline |
| Media | `#media` | `med.ri[]`, `med.cl`, `med.ct` | `render()` inline |
| Skills | `#skills` | `sk.langs[]`, `sk.tools[]`, `sk.comps[]` | `render()` inline |
| Contact | `#contact` | `con.items[]` | `render()` inline |
| Footer | `#ft` | `ft` | `render()` inline |

### Rendering

- `render()` — called once on page load and again when language switches. Writes all dynamic content into the DOM.
- `tlHTML(items)` — helper that renders a timeline list (`<div class="tl">`) from an array of `{r, o, p, d}` objects (role, organisation, period, description).
- `setLang(l)` — switches `lang` between `'en'` and `'de'`, updates button active states, calls `render()`.
- `navTo(id)` — smooth-scrolls to a section by id.

## CSS Conventions

Custom properties (CSS variables) are defined on `:root`:

| Variable | Purpose |
|---|---|
| `--p` | Primary dark colour (`#2b3235`) — headings, borders, accents |
| `--pl` | Primary lighter (`#4a5a60`) — hover states |
| `--pb` | Background muted (`#EDEEF0`) — alt sections, tags |
| `--pbm` | Background medium (`#DDE0E3`) — conference items |
| `--txt` | Body text (`#1a1f21`) |
| `--txtl` | Muted text (`#556066`) |
| `--white` | White (`#fff`) |
| `--bdr` | Border colour (`#d5d9db`) |

Key CSS classes:

| Class | Purpose |
|---|---|
| `.card` | White card with left border accent, used for research/media items |
| `.tl` / `.tli` | Timeline layout for experience and education entries |
| `.skills-grid` | CSS Grid auto-fit, min 250px columns |
| `.sc` | Skills card — used inside `.skills-grid` |
| `.pub` | Publication entry with border |
| `.conf-item` | Conference entry with muted background |
| `.hl` | Hero link button (filled); `.hl.out` = outlined variant |
| `.lb` | Language toggle button; `.lb.active` = selected language |
| `section.alt` | Alternating background sections |

## Development Workflow

### Making Changes

1. Edit `index.html` directly — there is no build step.
2. Open the file in a browser to preview locally.
3. Commit and push to trigger GitHub Pages deployment.

### Adding / Editing Content

- **Update both languages** (`C.en` and `C.de`) whenever content is added or changed.
- **Experience entries** (`experience.exp[]`) use `{r, o, p, d}`: role, organisation, period, description.
- **Education entries** (`education.edu[]`, `education.fur[]`) use the same `{r, o, p, d}` shape.
- **Publications** (`pub.pubs[]`) are raw HTML strings — bold, italics, etc. are inline HTML.
- **Media radio programmes** (`med.ri[]`) use `{title, desc, url}`.
- **Skills languages** (`sk.langs[]`) are `[name, level]` tuples rendered as a two-column table.
- **Skills tools and competencies** (`sk.tools[]`, `sk.comps[]`) are plain string arrays rendered as tags.

### CV File Naming

CV files have spaces in their names: `CV_Elena Smirnova MSc ENG.pdf`. The `render()` function encodes spaces with `%20` only (not full URI encoding) when building the download link:
```js
cvBtn.href = d.dlcv.file.replace(/ /g, '%20');
```
Do not rename the PDF files without updating the `dlcv.file` strings in both `C.en` and `C.de`.

### Adding a New Section

1. Add a `<section id="new-id">` placeholder in the HTML body (follow existing pattern).
2. Add navigation label to `C.en.nav[]` and `C.de.nav[]`, and the matching id to `C.en.ids[]` and `C.de.ids[]`.
3. Add the data fields to both `C.en` and `C.de`.
4. Add rendering logic inside `render()`.

## Git & Deployment

- **Hosting**: GitHub Pages — pushes to `main` auto-deploy the site.
- **Branch convention for Claude**: feature branches must be named `claude/<description>-<SESSION_ID>`.
- **No CI/CD, no tests** — verify visually in a browser before merging.

## Key Conventions

- **Do not introduce external dependencies** (no npm, no CDN imports, no frameworks). Keep it a single self-contained HTML file.
- **Always edit both language blocks** (`C.en` and `C.de`) when changing content.
- **Do not add build tooling** unless explicitly requested.
- **Preserve minified CSS style** — the `<style>` block is deliberately compact; do not expand or prettify it.
- **Photo**: `photo.png` is displayed with `object-position: center top` to keep the face visible — respect this when suggesting layout changes.
- **No JavaScript framework** — keep all logic as vanilla JS consistent with the existing style.
