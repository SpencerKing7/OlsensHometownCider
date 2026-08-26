<!-- Status: ready | Tier: 1 | Created: 2026-08-25 | Picked: - | Branch: - | PR: - | Verify: npm run build -->

# Deploy to Vercel instead of gh-pages

## Why

Workspace-wide decision (2026-08-25): web apps deploy to Vercel, not GitHub Pages. This site
currently serves `www.olsenscider.com` from the `gh-pages` branch via the `gh-pages` npm package.
This is a live business site, so the DNS cutover deserves care: verify before cleanup.

## What's already known

- Current setup: `homepage` in `package.json` points at the domain, `public/CNAME` holds
  `www.olsenscider.com`, deploys push a build to the `gh-pages` branch.
- Migration steps, in order:
  1. Create the Vercel project (`vercel link` / import the GitHub repo) with the GitHub
     integration, so pushes to the default branch auto-deploy.
  2. Add `www.olsenscider.com` as the project's domain and update DNS at the registrar to
     Vercel's records. Verify the domain serves the Vercel deployment before touching anything else.
  3. Only then clean up: remove the `gh-pages` devDependency and any deploy scripts, remove
     `homepage` from `package.json` (CRA derives `PUBLIC_URL` from it; Vercel serves at root),
     delete `public/CNAME`.
- The `gh-pages` branch is a deployment branch: it stays until the cutover is verified, and even
  then deleting it is a separate act taken only with explicit confirmation — never as part of this
  item's routine work.

## Notes
