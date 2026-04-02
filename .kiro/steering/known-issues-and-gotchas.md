---
inclusion: always
---

# Known Issues & Gotchas

## Content Not in JSON (Inconsistency)

`src/components/sections/About.astro` has skills and certifications hardcoded in the frontmatter, not in `src/content/`. This breaks the content-as-JSON pattern used everywhere else.

When updating skills or certifications, edit the arrays directly in `About.astro` until this is migrated.

Ideal fix: move both arrays into `src/content/about.json` and import them.

## Stale Tailwind Config

`tailwind.config.mjs` exists in the root but Tailwind is not installed or used. Do not reference it or add Tailwind classes — the site uses plain CSS custom properties exclusively. The file can be deleted safely.

## Visitor Map — Top Country Shows Code Not Name

`Visitors.astro` displays the top country as a raw ISO-2 code (e.g. `GB`) rather than a full country name. There is no country code → name lookup table implemented yet.

## About Section Skills Are Hardcoded

Skills list in `About.astro` is a plain JS array in the component frontmatter. If you add a new skill, edit that array directly. Same applies to certifications.

## Sidebar Email Link Missing

`Sidebar.astro` only renders social icon links on the left. The original design called for a vertical email address on the right side — this was never implemented.

## No `og-image` Generation

`public/og-image.png` is a static file. It is not auto-generated. If name, role, or branding changes, the image must be manually updated and re-uploaded.

## Scroll Snap on Mobile

The site uses `scroll-snap-type: y mandatory` on `html`. This can feel aggressive on mobile, particularly on the Visitors section which has a tall map. Be cautious when adjusting section heights on small viewports.

## Local Dev Uses Production API

`Visitors.astro` detects `localhost` and falls back to the production API endpoint (`https://john.fevre.uk/api/visitors`) for local development. This means local dev records real visits. A mock endpoint or env var override would be cleaner.
