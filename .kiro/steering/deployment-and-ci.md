---
inclusion: always
---

# Deployment & CI/CD

## How Deployment Works

Push to `main` → GitHub Actions builds the site → syncs `/dist` to S3 → invalidates CloudFront cache.

The workflow uses OIDC (no static AWS keys). The S3 bucket name and CloudFront distribution ID are pulled from SSM Parameter Store at deploy time — they are not hardcoded in the workflow.

SSM parameters:
- `/portfolio-site/s3-bucket-name`
- `/portfolio-site/cloudfront-distribution-id`

AWS region: `eu-west-2`

## Manual Deploy

Trigger via `workflow_dispatch` on the `Deploy to S3` workflow in GitHub Actions.

## Security Automation

- `security.yml` runs every Monday at 9am UTC
- Runs `npm audit --audit-level=moderate`
- If vulnerabilities are found, attempts `npm audit fix` and opens a PR automatically
- Dependabot is configured to group minor/patch npm updates weekly (Mondays), and also updates GitHub Actions versions

## Build Commands

```bash
npm ci           # clean install
npm run build    # outputs to /dist
```

The build must pass before deploy. The security workflow also runs a build verification after applying fixes.

## Required GitHub Secrets / Permissions

- `GITHUB_TOKEN` — used by the security workflow to create PRs (auto-provided)
- IAM role `arn:aws:iam::251200161642:role/github` — assumed via OIDC, no secrets needed

## Static Assets in `public/`

Files in `public/` are copied as-is to `/dist`. Key files:
- `resume.pdf` — linked from the nav Resume button
- `headshot.png` — used in the About section
- `og-image.png` — 1200×630 social share image (update when rebranding)
- `favicon.png` / `favicon-light.png` — theme-aware favicons
- `countries.geojson` — used by the Leaflet visitor map
