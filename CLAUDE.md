# Portfolio — CLAUDE.md

This file tells Claude how this project is structured so it can help effectively.
Read this before making any changes.

---

## What this is

A personal CV/portfolio website inspired by brittanychiang.com (v4).
Built with **Astro** (static output), plain CSS custom properties, and vanilla JS.
No framework UI libraries. No CSS-in-JS. No Tailwind (unless you add it).

Hosted on **Netlify**, deployed via GitHub Actions on push to `main`.

---

## Stack

| Concern        | Tool                              |
|----------------|-----------------------------------|
| Framework      | Astro (static, `output: 'static'`)|
| Styling        | Plain CSS with custom properties  |
| Interactivity  | Vanilla JS (inline `<script>` tags in `.astro` files) |
| Fonts          | Google Fonts — Inter + Fira Code  |
| Deployment     | Netlify via GitHub Actions        |

---

## Project structure

```
src/
  components/
    layout/         # Nav, Sidebar (fixed socials), Footer — used on every page
    sections/       # One file per scroll section: Hero, About, Experience, Projects, Contact
    ui/             # Small reusable bits: Button, JobCard, ProjectCard (add as needed)
  content/          # ← ALL personal data lives here as JSON. Edit these, not components.
    meta.json       # Name, role, email, bio, social links, resumeUrl
    jobs.json       # Work experience (role, company, range, bullets[])
    projects.json   # Projects (featured bool, name, description, tech[], github, external)
  styles/
    global.css      # CSS custom properties (design tokens), base resets, shared utilities
  pages/
    index.astro     # Single page — imports and composes all sections
public/
  resume.pdf        # Linked from the nav Resume button
  profile.jpg       # Used in the About section
  og-image.png      # Social share preview (1200×630)
```

---

## How to update content

**Never edit components to change personal info.** Use the JSON files instead:

- **Personal details** (name, email, bio, social URLs) → `src/content/meta.json`
- **Work history** → `src/content/jobs.json`
- **Projects** → `src/content/projects.json`
  - Set `"featured": true` for the large alternating showcase cards (max ~3)
  - Set `"featured": false` for the smaller grid cards at the bottom

---

## Design tokens

All colours, fonts, spacing, and transitions are CSS custom properties in `global.css`.
**To retheme the site, only edit the `:root` block in `src/styles/global.css`.**

Key tokens:
```css
--color-accent     /* teal highlight — the main brand colour */
--color-navy       /* page background */
--color-navy-light /* card/panel background */
--color-slate      /* body text */
--color-white      /* headings */
--font-mono        /* used for labels, nav numbers, tech lists */
--transition       /* standard cubic-bezier easing */
```

---

## Page sections (in order)

| Section    | File                            | Anchor      | data-number |
|------------|---------------------------------|-------------|-------------|
| Hero       | `sections/Hero.astro`           | `#hero`     | —           |
| About      | `sections/About.astro`          | `#about`    | `01.`       |
| Experience | `sections/Experience.astro`     | `#experience` | `02.`     |
| Projects   | `sections/Projects.astro`       | `#projects` | `03.`       |
| Contact    | `sections/Contact.astro`        | `#contact`  | `04.`       |

Nav links in `Nav.astro` are generated from a `navLinks` array — update that array if sections change.

---

## Scroll animations

Any element with class `fade-up` will animate in when it enters the viewport.
The IntersectionObserver is in `index.astro`'s inline `<script>` block.

Usage:
```html
<div class="fade-up">This will fade up on scroll</div>
```

The `.visible` class is added by the observer. Both states are defined in `global.css`.

---

## Layout: Sidebar

`Sidebar.astro` renders two fixed elements (hidden below 1080px):
- **Left** — vertical list of social icon links (from `meta.json`)
- **Right** — vertical email address (from `meta.json`)

Replace the `GH` / `LI` text placeholders with SVG icons when ready.
A good source: [Simple Icons](https://simpleicons.org/) or inline SVGs.

---

## Section heading convention

Use the `.section-heading` class with `data-number` attribute for consistent numbered headings:

```html
<h2 class="section-heading" data-number="01.">About Me</h2>
```

The number and decorative line are added via CSS `::before` / `::after` pseudo-elements.

---

## Adding a new section

1. Create `src/components/sections/NewSection.astro`
2. Import and add `<NewSection />` in `src/pages/index.astro`
3. Add the section name to `navLinks` array in `Nav.astro`
4. Follow the `section-heading` convention above

---

## Running locally

```bash
npm install
npm run dev        # http://localhost:4321
npm run build      # outputs to /dist
npm run preview    # preview the built output
```

---

## Deployment

Push to `main` → GitHub Actions runs `npm ci && npm run build` → deploys `/dist` to Netlify.

Required GitHub secrets:
- `NETLIFY_AUTH_TOKEN`
- `NETLIFY_SITE_ID`

Both found in your Netlify dashboard under Site Settings → Build & Deploy.

---

## What NOT to do

- Don't install a component library (shadcn, MUI, etc.) — keep it lean and fast
- Don't add Tailwind unless you're committing to it everywhere
- Don't hardcode personal info in components — always use the JSON content files
- Don't add client-side routing — this is a single-page site, anchor links are enough
- Don't use `localStorage` or cookies — there's no user state to persist
