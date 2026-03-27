# Portfolio — CLAUDE.md

This file tells Claude how this project is structured so it can help effectively.
Read this before making any changes.

---

## What this is

A personal CV/portfolio website inspired by brittanychiang.com (v4).
Built with **Astro** (static output), plain CSS custom properties, and vanilla JS.
No framework UI libraries. No CSS-in-JS. No Tailwind (unless you add it).

Hosted on **S3 & Cloudfront**, deployed via GitHub Actions on push to `main`.

---

## Stack

| Concern        | Tool                              |
|----------------|-----------------------------------|
| Framework      | Astro (static, `output: 'static'`)|
| Styling        | Plain CSS with custom properties  |
| Interactivity  | Vanilla JS (inline `<script>` tags in `.astro` files) |
| Fonts          | Google Fonts — Roboto + JetBrains Mono  |
| Mapping        | Leaflet (for visitor map)         |
| Deployment     | AWS s3 via GitHub Actions        |

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
| Visitors   | `sections/Visitors.astro`       | `#visitors` | `05.`       |

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

## Security Guidelines

A comprehensive security audit has been completed. See **`SECURITY-AUDIT.md`** for the full report.

### Critical Security Rules

**ALWAYS follow these security practices when making changes:**

1. **XSS Prevention:**
   - NEVER use `innerHTML` or template literals to inject external data into DOM
   - Use `textContent` or `document.createElement()` for dynamic content
   - Validate and sanitize ALL external data (API responses, GeoJSON, etc.)

2. **API Security:**
   - NEVER expose API keys or secrets in client-side code
   - Validate all API responses before rendering
   - Implement rate limiting and caching for public APIs
   - Use server-side proxies to hide real API endpoints

3. **Input Validation:**
   - Validate country codes: `/^[A-Z]{2}$/`
   - Validate numbers with bounds checking
   - Sanitize user input before use in selectors or queries

4. **Security Headers (when deploying):**

5. **Dependencies:**
   - Run `npm audit` before every deploy
   - Keep dependencies updated monthly
   - Review security advisories on GitHub

### Known Security Issues (To Fix)

See SECURITY-AUDIT.md for complete list. Priority fixes needed:

1. **CRITICAL:** Leaflet popup XSS vulnerability (Visitors.astro:252-257)
2. **HIGH:** Unconfigured visitor API endpoint exposure
3. **MEDIUM:** Missing Content Security Policy headers

### Security Checklist for New Features

Before adding new features:

- [ ] No hardcoded secrets or API keys
- [ ] All external data is validated
- [ ] No `innerHTML` or unsafe DOM manipulation
- [ ] API endpoints use authentication if needed
- [ ] Proper error handling (don't leak stack traces)
- [ ] External URLs validated with regex
- [ ] Security headers configured for new routes

### Reporting Security Issues

If you discover a security vulnerability:
1. Check if it's documented in SECURITY-AUDIT.md
2. DO NOT commit sensitive information
3. Fix critical issues immediately
4. Update SECURITY-AUDIT.md with remediation status

---

## What NOT to do

- Don't install a component library (shadcn, MUI, etc.) — keep it lean and fast
- Don't add Tailwind unless you're committing to it everywhere
- Don't hardcode personal info in components — always use the JSON content files
- Don't add client-side routing — this is a single-page site, anchor links are enough
- Don't use `localStorage` or cookies — there's no user state to persist
