# Portfolio

Personal CV/portfolio website built with Astro. Inspired by [brittanychiang.com](https://brittanychiang.com).

## Stack

- **Framework:** [Astro](https://astro.build) (static output)
- **Styling:** CSS custom properties + Tailwind CSS
- **Mapping:** [Leaflet](https://leafletjs.com) for visitor statistics
- **Deployment:** Netlify via GitHub Actions

## Quick Start

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## Project Structure

```
src/
  components/
    layout/       # Nav, Sidebar, Footer
    sections/     # Hero, About, Experience, Projects, Contact, Visitors
    ui/           # Reusable components
  content/        # JSON data files (edit these, not components)
    meta.json     # Name, role, bio, social links
    jobs.json     # Work experience
    projects.json # Projects
  styles/
    global.css    # Design tokens and base styles
  pages/
    index.astro   # Single-page layout
public/
  resume.pdf      # Downloadable CV
```

## Updating Content

Edit the JSON files in `src/content/` - never modify components directly for personal info changes.

## License

[MIT](LICENSE)
