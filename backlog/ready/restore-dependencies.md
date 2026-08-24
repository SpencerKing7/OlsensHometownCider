<!-- Status: ready | Tier: 1 | Created: 2026-08-20 | Picked: - | Branch: - | Verify: npm run build -->

# Restore dependencies so the build runs

## Why

`npm run build` — this repo's verify command — fails with a `MODULE_NOT_FOUND` out of
`react-scripts/config/webpack.config.js`. `node_modules/` exists but is incomplete, so there is
currently no way to prove a change here is good.

Fix is likely `rm -rf node_modules && npm install`, then confirm `npm run build` passes. Note this
repo is on Node 25 while CRA 5 predates it — if a clean install still fails, a Node version pin
(via `.nvmrc`) is the next thing to try.

The same problem affects ypride, lumahairstudio, and lumahairstudio-dev — all CRA repos with stale
installs. Worth fixing together.

## Notes
