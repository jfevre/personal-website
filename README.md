# Portfolio

Personal CV/portfolio website built with Astro. Inspired by [brittanychiang.com](https://brittanychiang.com).

## Architecture

```
                                    ┌─────────────────────────────────────────────────────────┐
                                    │                        AWS                              │
                                    │                                                         │
┌──────────┐      HTTPS             │  ┌─────────────┐         ┌─────────────┐               │
│  User    │ ───────────────────────┼─▶│ CloudFront  │────────▶│  S3 Bucket  │               │
│ Browser  │                        │  │             │         │ (Static)    │               │
└──────────┘                        │  │  ┌──────────┤         └─────────────┘               │
                                    │  │  │ /api/*   │                                        │
                                    │  │  │ behavior │         ┌─────────────┐               │
                                    │  │  └────┬─────┤         │ API Gateway │               │
                                    │  │       │     │────────▶│  (REST)     │               │
                                    │  └───────┼─────┘         └──────┬──────┘               │
                                    │          │                      │                       │
                                    │          │ X-Origin-Verify      │                       │
                                    │          │ header injected      ▼                       │
                                    │          │               ┌──────────────┐               │
                                    │          │               │   Lambda     │               │
                                    │          │               │  Authorizer  │◀──┐           │
                                    │          │               └──────┬───────┘   │           │
                                    │          │                      │           │           │
                                    │          │               ┌──────▼───────┐   │ Read      │
                                    │          │               │   Lambda     │   │ Secret    │
                                    │          │               │  (Visitor)   │   │           │
                                    │          │               └──────┬───────┘   │           │
                                    │          │                      │           │           │
                                    │          │               ┌──────▼───────┐   │           │
                                    │          │               │   DynamoDB   │   │           │
                                    │          │               │  (Visitors)  │   │           │
                                    │          │               └──────────────┘   │           │
                                    │          │                                  │           │
                                    │          │               ┌──────────────┐   │           │
                                    │          │               │     SSM      │───┘           │
                                    │          │               │  Parameter   │               │
                                    │          │               │    Store     │◀──┐           │
                                    │          │               └──────────────┘   │           │
                                    │          │                                  │ Rotate    │
                                    │          │               ┌──────────────┐   │ Secret    │
                                    │          └──────────────▶│   Lambda     │───┘           │
                                    │            Weekly        │  (Rotator)   │───┐           │
                                    │            rotation      └──────────────┘   │           │
                                    │                                 ▲           │ Update    │
                                    │                                 │           │ Header    │
                                    │                          ┌──────┴───────┐   │           │
                                    │                          │ EventBridge  │   │           │
                                    │                          │  Scheduler   │   ▼           │
                                    │                          └──────────────┘ CloudFront    │
                                    │                                                         │
                                    └─────────────────────────────────────────────────────────┘
```

### Request Flow

1. **Static Content:** User requests `john.fevre.uk/*` → CloudFront serves from S3
2. **Visitor API:** User requests `/api/visitors` → CloudFront injects `X-Origin-Verify` header → API Gateway
3. **Authorization:** Lambda authorizer validates header against secret in SSM Parameter Store
4. **Response:** Visitor Lambda queries DynamoDB, returns country-level statistics

### Secret Rotation

Weekly EventBridge schedule triggers the rotation Lambda which:
1. Generates new secret
2. Updates SSM Parameter Store
3. Updates CloudFront origin custom header

## Stack

- **Framework:** [Astro](https://astro.build) (static output)
- **Styling:** CSS custom properties
- **Mapping:** [Leaflet](https://leafletjs.com) for visitor map
- **Hosting:** AWS S3 + CloudFront
- **API:** API Gateway + Lambda + DynamoDB
- **Infrastructure:** Terragrunt / OpenTofu
- **CI/CD:** GitHub Actions

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
  countries.geojson  # Country boundaries for visitor map
```

## Updating Content

Edit the JSON files in `src/content/` - never modify components directly for personal info changes.

## License

[MIT](LICENSE)
