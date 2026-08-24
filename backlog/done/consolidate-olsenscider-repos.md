<!-- Status: done | Created: 2026-08-24 | Completed: 2026-08-24 | Picked: 2026-08-24 | Branch: - | PR: - | Verify: gh api repos/SpencerKing7/OlsensHometownCider --jq .owner.login -->

# Consolidate Olsen's Cider into one repo under SpencerKing7

## Why

Two repos held the same project and only one was real:

| Repo | Last feature work | Serves the domain |
|---|---|---|
| `olsenscider/website` | 2024-11-11 | **yes** — www.olsenscider.com |
| `SpencerKing7/OlsensCider` | 2024-09-04 | no |

`website` was two months ahead on content and is the live site, so `website` is canon and
`OlsensCider` was deleted. **This repo is the survivor**, and this item moved here with the work.

## What happened — 2026-08-24

1. **`SpencerKing7/OlsensCider` deleted** first, out of the original order. Harmless: the names
   didn't collide, so the sequence was free.
2. **Cloudflare SSL/TLS was already `Full`**, not `Full (strict)` — the 526 precaution was
   already satisfied, nothing to change.
3. **Transferred** `olsenscider/website` → `SpencerKing7`. Pages came through intact: `cname`
   still `www.olsenscider.com`, `status: built`, source `gh-pages`. The domain-verification
   failure mode never appeared. The old `olsenscider/website` path now redirects.
4. **Repointed** both Cloudflare CNAMEs (apex and `www`) to `spencerking7.github.io`, still
   proxied. Site verified 200 on `www` and 301 on the apex, against a cache-buster so the check
   couldn't pass on stale cache.
5. **Scaffolded** this repo to the workspace standard and moved both backlog items in.

6. **Retired the local clone** at `~/Developer/OlsensHometownCider`, which pointed at the deleted
   repo, and replaced it with a fresh clone of this one. `npm install` + `npm run build` pass
   there, which also closed [restore-dependencies](restore-dependencies.md).

## What's still open

- **Optional rename** `website` → something meaningful. `OlsensCider` is free now that the old
  repo is gone. Left undecided deliberately; it blocks nothing, and GitHub redirects the old path
  if it ever happens. Note `.claude/project.json` already carries `name: OlsensHometownCider`,
  which is what the local directory is called.

## Notes — worth keeping

**The "nothing but scaffolding" claim in the original item was wrong, and it nearly mattered.**
The deleted repo carried a commit `f1b8400 "Sync local working copy"` touching 21 source files —
a Footer rewrite, Navbar changes, a `services/` → `cidertips/` refactor, a new `IconSources`
page, theme changes. Since the GitHub copy was already deleted, that local clone was the only
copy. Diffing it against `website` before deleting anything showed the trees **byte-identical**
(`src/`, `public/`, `package.json`, `tsconfig.json`, `README.md`, `.gitignore`,
`package-lock.json`) — the commit was the local clone catching *up*, not new work. Nothing was
lost, but the conclusion was only safe because it was checked rather than assumed.

**GitHub will not issue an HTTPS certificate for the custom domain while Cloudflare proxies it.**
`https_certificate` is `null` and stays that way: GitHub needs the hostname to resolve to Pages
to validate, and proxied records resolve to Cloudflare. The origin therefore presents
`CN=*.github.io`, which does **not** match `www.olsenscider.com`.

Consequence, and the thing most likely to break this site later: **`Full (strict)` will return
526.** Cloudflare must stay on `Full`. Visitors are unaffected — Cloudflare serves its own valid
Universal SSL cert (`CN=olsenscider.com`, Google Trust Services). To earn `Full (strict)`, both
records have to be un-proxied (grey cloud) long enough for GitHub to provision, then re-proxied.
