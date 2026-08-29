# CLAUDE.md — OlsensHometownCider

## What this is

Marketing and information site for Olsen's Hometown Cider — a custom cider-pressing service. Create
React App + TypeScript with MUI, published to GitHub Pages at https://www.olsenscider.com
(`public/CNAME` holds the custom domain).

**This repo is public** (ControlHome is the other public one). Anything committed here is
world-readable once pushed. (Work items live in Linear, which is private.)

## Layout

- `src/pages/` — one per route: `HomePage`, `AboutUsPage`, `ServicesPage`, `CiderTipsPage`,
  `FoodSafetyPage`, `ContactUsPage`, `IconSources`
- `src/components/` — grouped by page (`aboutus/`, `cidertips/`, `contactus/`, `foodsafety/`,
  `homepage/`, `services/`), plus shared `Navbar`, `Footer`, and `allpages/`
- `src/GlobalState.tsx` — app-wide state
- `src/theme.ts` — MUI theme
- `public/CNAME` — the custom domain; deleting it breaks the live site
- Work items live in Linear (project `Olsen's Hometown Cider`); use `/backlog`

## Commands

```bash
npm install
npm start        # http://localhost:3000
npm run build
npm run deploy   # predeploy builds, then publishes build/ to gh-pages
```

## Verify

```bash
npm run build
```

Only the default CRA smoke test exists, so a clean production build is the gate.

## Architecture

A static content site — no API, no auth, no data layer. Pages compose their own component folder
plus the shared `Navbar` and `Footer`. `GlobalState.tsx` carries whatever cross-page state exists;
`theme.ts` drives the MUI look.

Icon attributions live on the `IconSources` page — if you add a third-party icon, add its
attribution there.

## External services

Cloudflare fronts the domain; GitHub Pages serves the build from `gh-pages`. Both CNAMEs (apex
and `www`) point at `spencerking7.github.io` and are **proxied**.

Because they are proxied, GitHub cannot validate the hostname and so never issues a certificate
for the custom domain — `https_certificate` is `null` by design, and the origin presents
`CN=*.github.io`. Visitors are unaffected: Cloudflare serves its own valid cert. Check the state
with `gh api repos/SpencerKing7/OlsensHometownCider/pages`.

## Never

- **Never delete or overwrite `public/CNAME`.** It points the GitHub Pages build at
  www.olsenscider.com; losing it takes the live site down.
- **Never set Cloudflare SSL/TLS to `Full (strict)`.** The origin cert is `CN=*.github.io`, which
  doesn't match the custom domain, so strict mode serves visitors 526. It must stay on `Full`
  unless the records are first un-proxied long enough for GitHub to provision a real cert.
- **Never commit anything sensitive here.** This repo is public — treat every file as published.
