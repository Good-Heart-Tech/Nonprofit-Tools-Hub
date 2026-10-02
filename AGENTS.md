# Nonprofit Tools Hub - Agent Guidelines

This file provides context for AI coding agents working on the Nonprofit Tools Hub.

## Project Overview

Nonprofit Tools Hub is a **static site** hosted on **Cloudflare Pages**. It serves as a navigation hub for free nonprofit tech tools. Individual tools run on separate subdomains (e.g., `convert.nonprofittools.org`, `ocr.nonprofittools.org`) and are loaded in iframes when embedded, or open in new tabs when marked `target="_blank"`.

- **Live site**: [nonprofittools.org](https://nonprofittools.org)
- **Maintainer**: [Good Heart Tech](https://goodhearttech.org/)

## Tech Stack

- **HTML5** - Single `index.html` with inline CSS
- **Vanilla JavaScript** - No frameworks; `script.js` handles navigation and UI
- **Cloudflare Pages** - Static hosting, no build step

## Project Structure

```
/
├── index.html      # Main page: layout, styles, sidebar nav, welcome screen, iframe
├── assets/         # Good Heart Tech brand kit copies: logo, favicons, og-card.png, ght-variables.css
├── script.js       # Navigation, sidebar toggle, mobile menu, iframe loading, deep linking
├── sitemap.xml     # Sitemap for SEO
├── robots.txt      # Points crawlers to sitemap
├── LICENSE
└── AGENTS.md
```

- **Navigation**: Sidebar nav in `index.html`; each `.nav-item` has `data-url`, `data-label`, and `data-slug` (for embedded tools)
- **Embedded tools**: Loaded in `#content-frame` iframe when `data-url` is present and no `target="_blank"`
- **External links**: Items with `target="_blank"` open in new tab (e.g., PDF Editor, Password Pusher, Nonprofit Tech Navigator)
- **Deep linking**: `?tool=<slug>` in the URL loads that tool; back/forward and bookmarking work (e.g., `?tool=ocr`)

## Do

- Use vanilla HTML, CSS, and JavaScript only
- Keep styles in `index.html`; colors and fonts come from `assets/ght-variables.css` (`--ght-palette-*`, `--ght-font-*`). Never add new hex values or fonts
- Follow the Good Heart Tech brand kit (https://github.com/Good-Heart-Tech/Good-Heart-Tech-Branding-Marketing, private): system UI font stack (no Google Fonts), white page, richBlack headings, charcoal body text, huduPrimary for small links, huduLight washes and borders, dark richBlack footer with lightBlue text, 8px button radius
- White text on `primary` only for large, bold button labels; otherwise use huduPrimary (hover) or richBlack. Never use lightBlue, huduLight, or neutral as text on white
- Do not rely on color alone (active nav item also has a bar and heavier weight)
- To refresh the brand, copy the updated `variables.css` from the kit's `tokens/exports/css/` over `assets/ght-variables.css`
- Preserve existing structure: sidebar, welcome screen, iframe, mobile menu
- Add new tools by adding `.nav-item` links with `data-url`, `data-label`, and `data-slug` (for embedded tools)
- Use inline SVG icons from the `<symbol>` sprite at the top of `<body>` (`<svg class="icon"><use href="#i-name"/></svg>`); add a new `<symbol>` for new tools. Font Awesome and Noto Sans were removed
- Use the official logo and favicon files in `assets/`; do not redraw or recolor the logo
- Keep the site static and deployable to Cloudflare Pages with no build step

## Don't

- Do not add build tools, bundlers, or frameworks (React, Vue, etc.)
- Do not set up local dev servers or emulators; assume remote-only workflows
- Do not change tool URLs without verifying they exist and work
- Do not remove Sentry or other third-party scripts without explicit approval
- Do not add em dashes to any text
- Do not add new heavy dependencies or external libraries without approval

## Cloudflare Pages Deployment

- **Build command**: (empty)
- **Build output directory**: `/`
- The repo root is served as-is; no build or compilation

## Safety and Permissions

**Allowed without explicit approval:**
- Read and edit `index.html`, `script.js`, `sitemap.xml`, `robots.txt`
- Add or update nav items, styles, and minor UI changes

**Ask first:**
- Adding or removing third-party scripts (Sentry, analytics, etc.)
- Changing deployment configuration or domain references
- Structural changes to the layout or navigation model

## When Stuck

- Ask a clarifying question or propose a short plan before making large changes
- Do not push broad speculative changes without confirmation
