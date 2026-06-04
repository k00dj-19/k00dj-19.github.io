# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Personal portfolio site for Kim Dongjin (AI researcher, KAIST). Forked from the `congchu/web-porfolio` static template (HTML + Bootstrap 4 + jQuery). Hosted on GitHub Pages from the `main` branch — no build step is required to deploy.

## Local development

There is no Node toolchain or package manager wired up. Open the HTML directly or serve the repo root over a static server, e.g.:

```bash
python3 -m http.server 8000   # then open http://localhost:8000/index.html
```

Two top-level pages exist:
- `index.html` — the live portfolio (Home / About / Education / Publications / Experiences / Talks / Contact)
- `single.html` — a leftover template page from the original Colorlib "Ronaldo" theme; not linked from the portfolio. Leave alone unless you're intentionally repurposing it.

### SCSS → CSS

`scss/style.scss` is the source of truth for theme variables (e.g. `$primary: #3e64ff`), but the shipped stylesheet is `css/style.css` and the two are not currently auto-compiled by any tooling in this repo. Small visual tweaks are typically edited directly in `css/style.css`. If you need to regenerate from SCSS, run an external Sass compiler against `scss/style.scss` (it `@import`s the vendored `scss/bootstrap/` partials) and write the output to `css/style.css`.

## Deployment

`.github/workflows/static.yml` is the active workflow: on push to `main`, it uploads the entire repo as a Pages artifact and deploys via `actions/deploy-pages@v4`. Two other workflow files exist but are inert/legacy:
- `main.yml` — placeholder ("echo Hello, world!"), no real deploy
- `jekyll-gh-pages.yml` — Jekyll build path, unused (the site is plain static HTML, not Jekyll)

If you change deployment, edit `static.yml`; don't add Jekyll config unless you're intentionally migrating.

## Architecture notes

- **Single-page scroll layout.** `index.html` is one document with anchor sections (`#home-section`, `#about-section`, `#education`, `#publications`, `#experiences`, `#talks`, `#contact-section`). The navbar uses Bootstrap scrollspy (`data-spy="scroll" data-target=".site-navbar-target"`) and `js/main.js` provides smooth-scroll on `#ftco-nav a[href^="#"]` clicks with a 70px offset for the fixed navbar. When adding a new section, update both the nav `<ul>` and add a matching `id="…"` on the section.
- **jQuery plugin stack** (loaded in order at the bottom of each HTML file): jQuery → Bootstrap → Owl Carousel → AOS / Stellar / Waypoints / Scrollax / Magnific Popup → `js/main.js`. Animations rely on the `ftco-animate` class being picked up by the IntersectionObserver-style logic inside `main.js`; new sections need that class on the animated children for fade-ins to fire.
- **Hero height.** `js/main.js` sets `.js-fullheight` elements to `window.height()` on load and resize. Recent commits added mobile interactivity tweaks here — be careful when changing the resize handler.
- **Image assets** live in `images/` and are referenced inline via `style="background-image:url(images/…)"` in the HTML rather than via CSS classes. New publication/experience cards follow this same inline-background pattern.

## Editing content

Most updates (publications, talks, experiences, about text) are pure HTML edits inside `index.html`. The structure repeats per item, so copy an existing card/row block as a template rather than hand-writing markup. Profile metadata (name, email, LinkedIn) is hardcoded in the `about-section` block near the top of `index.html`.
