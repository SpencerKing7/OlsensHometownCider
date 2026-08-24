<!-- Status: done | Tier: 1 | Created: 2026-08-20 | Completed: 2026-08-24 | Picked: - | Branch: - | Verify: npm run build -->

# Restore dependencies so the build runs

## Why

`npm run build` — this repo's verify command — failed with a `MODULE_NOT_FOUND` out of
`react-scripts/config/webpack.config.js`. `node_modules/` existed but was incomplete, so there was
no way to prove a change here was good.

## Outcome — 2026-08-24

Fixed, by the route the item predicted. The broken `node_modules/` belonged to the old
`SpencerKing7/OlsensCider` clone, retired during the repo consolidation; this repo was cloned
fresh, so a clean install was the fix by construction.

- `npm install` — 1571 packages, no errors (deprecation warnings only, all transitive CRA 5 deps)
- `npm run build` — **Compiled successfully**, 132.42 kB gzipped main bundle

**The Node version worry didn't materialize.** The item flagged Node 25 against CRA 5 and
proposed an `.nvmrc` pin as the fallback. Not needed — Node v25.9.0 with npm 11.12.1 builds this
project cleanly. Don't add a pin without a failure to justify it.

Two cosmetic notes, neither blocking:

- `caniuse-lite` is outdated; `npx update-browserslist-db@latest` silences the warning.
- The build logs *"assuming it is hosted at /"* despite `homepage` being
  `https://www.olsenscider.com/` — that resolves to `/`, so the message is correct and harmless.

The sibling repos named in the original item — `ypride`, `lumahairstudio`, `lumahairstudio-dev` —
have the same stale-install problem and their own items. This fix does not carry across to them.
