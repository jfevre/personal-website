---
inclusion: always
---

# Structure & Conventions

## Directory Layout

```
src/
  components/
    layout/     # Nav, Sidebar, Footer — used on every page
    sections/   # One .astro file per scroll section
    ui/         # Small reusable components (Button, JobCard, ProjectCard, etc.)
  content/      # ALL personal data as JSON — edit these, not components
    meta.json   # Name, role, email, bio, social links, resumeUrl
    jobs.json   # Work history (role, company, range, bullets[])
    projects.json # Projects (featured, name, description, tech[], github, external)
    about.json  # Skills (string[]) and certifications ({name, url}[])
  styles/
    global.css  # Design tokens, resets, shared utilities
  pages/
    index.astro # Single page — imports and composes all sections
public/
  resume.pdf
  headshot.png
  og-image.png  # 1200×630 social share image
```

## Content Updates

Never edit components to change personal info. Use the JSON files:

- Name, email, bio, social URLs → `src/content/meta.json`
- Work history → `src/content/jobs.json`
- Projects → `src/content/projects.json`
  - `"featured": true` → large alternating showcase cards (max ~3)
  - `"featured": false` → smaller grid cards

## Page Sections (in order)

| Section    | File                        | Anchor      | data-number |
|------------|-----------------------------|-------------|-------------|
| Hero       | sections/Hero.astro         | #hero       | —           |
| About      | sections/About.astro        | #about      | 01.         |
| Experience | sections/Experience.astro   | #experience | 02.         |
| Projects   | sections/Projects.astro     | #projects   | 03.         |
| Contact    | sections/Contact.astro      | #contact    | 04.         |
| Visitors   | sections/Visitors.astro     | #visitors   | 05.         |

## Adding a New Section

1. Create `src/components/sections/NewSection.astro`
2. Import and add `<NewSection />` in `src/pages/index.astro`
3. Add the section to the `navLinks` array in `Nav.astro`
4. Use the section heading convention (see below)

## Section Heading Convention

```html
<h2 class="section-heading fade-up" data-number="06.">Section Title</h2>
```

The number and decorative line are rendered via CSS `::before` / `::after` — don't add them manually.

## Scroll Animations

Add `class="fade-up"` to any element that should animate in on scroll. The IntersectionObserver in `index.astro` handles the rest by toggling the `.visible` class.

## Design Tokens

All colours, spacing, and transitions are CSS custom properties in `global.css`. To retheme, only edit the `:root` block — never hardcode values in components.

Key tokens:
```css
--color-accent      /* orange (dark) / forest green (light) */
--color-navy        /* page background */
--color-navy-light  /* card/panel background */
--color-slate       /* body text */
--color-white       /* headings */
--font-mono         /* labels, nav numbers, tech lists */
--transition        /* standard cubic-bezier easing */
--nav-height        /* 70px — use for top padding on full-height sections */
--max-width         /* 1100px — max content width */
--border-radius     /* 4px */
```
