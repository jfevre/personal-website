---
inclusion: always
---

# Project Overview

This is a personal portfolio site for a DevOps/Cloud Engineer. It's a single-page static site built with Astro.

## Stack

- Framework: Astro (static output, `output: 'static'`)
- Styling: Plain CSS with custom properties — no Tailwind, no CSS-in-JS
- Interactivity: Vanilla JS in inline `<script>` blocks inside `.astro` files
- Fonts: Roboto (sans) + JetBrains Mono (mono) via Google Fonts
- Mapping: Leaflet (visitor map section)
- Deployment: AWS S3 + CloudFront via GitHub Actions on push to `main`

## Key Constraints

- No component libraries (shadcn, MUI, etc.)
- No Tailwind unless adopted everywhere
- No client-side routing — single page, anchor links only
- No `localStorage` or cookies — no user state to persist
- All personal data lives in JSON files under `src/content/` — never hardcode it in components

## Running Locally

```bash
npm install
npm run dev      # http://localhost:4321
npm run build    # outputs to /dist
npm run preview  # preview built output
```
