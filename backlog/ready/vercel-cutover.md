<!-- Status: ready | Tier: 3 | Created: 2026-08-24 | Picked: - | Branch: - | PR: - | Verify: curl -sI https://www.olsenscider.com | grep -iE '^HTTP|^server' -->

# Move the site to Vercel

## Why

Consolidating hosting onto one surface instead of GitHub Pages plus Cloudflare. This site is the
same shape as the Luma pair — CRA, Pages, Cloudflare-proxied DNS, `public/CNAME`, a `homepage`
field that only exists for Pages — so the flow proven there transfers here almost verbatim.

**This is a rider on the Luma outcome, not an independent decision.** See "Notes" — the cost
question that gates the Luma cutover does not get asked twice.

## Blocked on

- **`vercel-pilot-dev`**, a backlog item in the `lumahairdev` repo. It exists to work the flow out
  against a dark host. This site has no dark host — see below — so it must not go first.
- **The Hobby-vs-Pro decision** on the Luma production cutover. Vercel's Hobby plan prohibits
  commercial use, and Olsen's is a commercial site, so Hobby is off the table here too.

## What's different here from the Luma cutover

**There is no pilot host.** Luma got to rehearse on `dev.lumahairstudio.com` while it was
`NXDOMAIN`. Olsen's has one hostname and it is live. Every step here happens against the
production site, which is the whole reason this waits for the pilot rather than leading.

**The apex redirect lives in GitHub Pages, not Cloudflare.** Verified 2026-08-24 by requesting
the apex directly against a Pages IP with Cloudflare bypassed: `Server: GitHub.com`,
`Location: http://www.olsenscider.com/`. So the Luma trap — a Cloudflare Redirect Rule that only
fires on proxied traffic and dies silently on grey-cloud — does **not** apply. The redirect here
dies for a different reason: it is a function of `public/CNAME`, so it stops the moment Pages
stops serving. It still has to be rebuilt in Vercel; there is just no Cloudflare rule to find
first.

**Cost is probably zero.** Vercel Pro is priced per account, not per project. If Luma goes Pro,
adding this project to the same account is $0 incremental. If Luma decides Pro isn't worth it and
stays on Pages, this item should be dropped rather than done — the reasoning is identical and the
answer would be too.

**It inverts three rules currently written into `CLAUDE.md`.** They are correct today and wrong
the moment this lands, so they get rewritten as part of the work, not left to rot:

- `## External services` describes the records as **proxied** and explains why
  `https_certificate` is `null`. On Vercel the records are grey-clouded and Vercel issues its own
  cert — the whole section is replaced.
- `## Never` → *"Never set Cloudflare SSL/TLS to `Full (strict)`"*. That rule exists because the
  origin is `CN=*.github.io`. Once Vercel is the origin the rule is obsolete.
- `## Never` → *"Never delete or overwrite `public/CNAME`"*. Step 4 below deliberately deletes it.

## What to do

1. Create the Vercel project from `SpencerKing7/OlsensHometownCider`. CRA needs no config —
   build `npm run build`, output `build/`.
2. Confirm the **preview deploy renders** before touching DNS. Check the hash routes, not just
   `/` — this is a `HashRouter` SPA (`/#/CiderTips`, `/#/ContactUs`, `/#/IconSources`), so a
   broken router shows a fine-looking homepage and nothing else.
3. Add **both** hostnames to the Vercel project and set the apex to redirect to `www`, replacing
   the Pages-provided redirect.
4. Repoint both Cloudflare CNAMEs to Vercel's values, **grey-clouded (DNS only)**. Vercel serves
   its own certificate; leaving the orange cloud on puts Cloudflare's cert in front of Vercel's
   and produces cert mismatches or redirect loops.
5. Only then remove the custom domain from GitHub Pages settings and delete `public/CNAME`.
   Leaving either in place means the repo keeps claiming `www.olsenscider.com` and the two fight
   over it.
6. Reset the `homepage` field in `package.json` — a Pages artifact, meaningless on Vercel.
7. Rewrite `CLAUDE.md` per the three rules above, and drop `npm run deploy` / the `gh-pages`
   dependency if nothing else uses them.

## Notes

**Verify is `200` plus `server: Vercel`.** A bare 200 proves nothing — this site already returns
200 from Pages today, so the header is the only part of the check that can actually fail.

`olsenscider.com` has **2 DNS records and no MX** — Cloudflare's own dashboard flags that mail
cannot reach the domain — so no email is riding on any of this.

Registration and DNS stay at **Cloudflare** either way. That was decided 2026-08-24 for Luma and
the reasoning is domain-agnostic: Cloudflare Registrar requires its own nameservers, so moving
DNS to Vercel would mean moving the registration too, and Cloudflare sells at cost while Vercel
resells at a margin.

Repo history worth carrying: transferred from `olsenscider` to `SpencerKing7` and renamed from
`website` on 2026-08-24 with no outage — see
[consolidate-olsenscider-repos](../done/consolidate-olsenscider-repos.md).
