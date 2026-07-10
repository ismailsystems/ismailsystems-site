# DEPLOY.md — ismailsystems.com deploy record (Cloudflare Pages + Cloudflare DNS + Zoho Mail)

This file records the stack **as deployed and observed** (dig/curl-verified 2026-07-07),
plus the runbook steps that depend on it. Page content and gate state derive from the
markers in `index.html` per CLAUDE.md — never from this file. If this file and
CLAUDE.md's **Stack** section disagree, CLAUDE.md wins and this file is stale.

## 0. Hosting — Cloudflare Pages (as deployed)
- The site is a Cloudflare Pages project connected to
  `github.com/ismailsystems/ismailsystems-site`; **every push to `main` auto-deploys**
  within about a minute. Git history is the deploy log; rollback = `git revert` + push.
- Custom domains on the project: apex `ismailsystems.com` and `www` (301 → apex).
  `http` 301s to `https`; the certificate is Cloudflare-managed and auto-renews.
- Cloudflare Pages serves clean URLs (`/privacy.html` 308s to `/privacy`) and, with
  `404.html` present in the repo root, returns real 404s for unknown paths (without
  that file it soft-404s: index.html with a 200).
- The `CNAME` file in the repo is a leftover from a GitHub Pages plan that was never
  enabled. GitHub Pages is OFF for this repo. The file is inert on Cloudflare Pages.
- **AI crawler policy:** Cloudflare Managed `robots.txt` is intentionally OFF in
  AI Crawl Control (changed and curl-verified 2026-07-09), so the repo's
  `robots.txt` is served as-is. Keep it off if AI search / grounding citation is a
  goal; if this changes, re-check `https://ismailsystems.com/robots.txt` for
  Cloudflare-prepended `Disallow` rules before touching the repo file.

## 1. DNS — Cloudflare zone (as observed)

| Type | Host | Value | Notes |
|---|---|---|---|
| A/AAAA | @ | Cloudflare-proxied (managed by the Pages project) | do not hand-edit |
| A/AAAA | www | Cloudflare-proxied (managed by the Pages project) | 301 → apex |
| MX | @ | `mx.zoho.com` (10), `mx2.zoho.com` (20), `mx3.zoho.com` (50) | Zoho Mail |
| TXT | @ | `v=spf1 include:zohomail.com ~all` | SPF |
| TXT | @ | `zoho-verification=…` | Zoho domain verification |
| TXT | zoho._domainkey | `v=DKIM1; k=rsa; p=…` | DKIM, published |
| TXT | _dmarc | `v=DMARC1; p=none; rua=mailto:sam@ismailsystems.com` | see ramp below |

## 2. Email — Zoho Mail (as deployed) + DMARC ramp (to do)
- Mailbox: `sam@ismailsystems.com` on Zoho. MX/SPF/DKIM live per the table above.
- **DMARC ramp (pending):** currently `p=none` (monitor-only). After ~2 weeks of clean
  aggregate reports, move to `p=quarantine`; after another clean stretch, `p=reject`.
- Test both directions with an outside account after any DNS change; both must land
  in inbox, not spam.

## 3. Variant domains ×3 (as observed + verify list)
The three misspelling variants (esmail / ismael / esmael forms — full domains are never
written in this repo, per CLAUDE.md law 8):
- All three are registered, on the same Cloudflare account, and **301-redirect to
  `https://ismailsystems.com/` with valid HTTPS** (verified live 2026-07-07).
- **Unverified:** inbound mail forwarding on the variants (whether mail to any address
  at a variant reaches sam@). Verify before printing a variant address anywhere;
  if needed, configure Cloudflare Email Routing (catch-all → sam@) per variant, with
  `v=spf1 -all`-style lockdown only if Cloudflare's routing docs say no include is
  required (their routing rewrites differ from registrar forwarders — check current docs).

## 4. Verification pass (after any infra change)
- `https://ismailsystems.com/` and `/privacy` load over HTTPS; `www` and `http` both
  301 to the apex; a bogus path returns a real 404 page.
- One variant domain 301s to the canonical with a valid certificate.
- Toggle OS dark mode; the pages keep their light scheme (deliberate — `color-scheme:
  light`, no dark variant per design law 4).
- Tap the `tel:` links on a phone; (847) 906-2560 connects. The `sms:` link opens a text.
- Lighthouse (Chrome DevTools, **mobile**): ≥95 in all categories, per design law 3.
- Text the URL to yourself; the link preview shows the og.jpg card.

## Appendix — `/assets/og.jpg` spec (gate G-OG resolved 2026-07; kept for future rebuilds)
1200×630 JPEG: photo left, wordmark + one-liner + phone right, stripe on top. Keep it
lean (target ≤150 KB; it's fetched only by link-preview scrapers, never by the page
itself, so it doesn't count against page weight).
