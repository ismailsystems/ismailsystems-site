# DEPLOY.md — ismailsystems.com deploy runbook (GitHub Pages + Namecheap)

Hosting, DNS, and email only. Page content and gate state derive from the markers in
`index.html` per CLAUDE.md — never from this file. If this file and CLAUDE.md's
**Stack** section ever disagree, CLAUDE.md wins and this file is stale.

## 0. Repo & hosting — GitHub Pages (15 min)
1. Create the repo `github.com/ismailsystems/ismailsystems-site` and push `main` to it
   (`git push -u origin main`). The repo must be **public** — hence the content rule at
   the top of CLAUDE.md: nothing enters this repo beyond what the deployed site itself
   reveals.
2. Settings → Pages → Source: **Deploy from a branch** → `main` / `/ (root)` → Save.
   The site serves at `https://ismailsystems.github.io/ismailsystems-site/` — usually
   within a minute, up to ~10 minutes per GitHub's docs.
3. Settings → Pages → Custom domain: `ismailsystems.com` → Save. GitHub commits a `CNAME`
   file to `main` — expected; `git pull` before your next local commit.
4. After the §1 DNS propagates and the certificate issues (minutes to ~24 h), check
   **Enforce HTTPS**.

Every future edit is a commit on `main`; Pages redeploys automatically. Git history is the
deploy log and the rollback mechanism.

## 1. Canonical DNS — Namecheap, `ismailsystems.com` (10 min)
Domain List → Manage → **Advanced DNS**. Remove any parking/URL-redirect records on `@`
and `www` first — they conflict. Then:

| Type  | Host | Value                       | Notes                                        |
|-------|------|-----------------------------|----------------------------------------------|
| A     | @    | `185.199.108.153`           | GitHub Pages                                 |
| A     | @    | `185.199.109.153`           | GitHub Pages                                 |
| A     | @    | `185.199.110.153`           | GitHub Pages                                 |
| A     | @    | `185.199.111.153`           | GitHub Pages                                 |
| CNAME | www  | `ismailsystems.github.io.`  | GitHub redirects www → apex once §0.3 is set |

## 2. Email — Google Workspace, canonical domain only (20 min)
1. Google Workspace on `ismailsystems.com`; create `sam@ismailsystems.com`.
2. **MX / SPF / DKIM** — add exactly what the Workspace setup wizard prescribes in
   Namecheap Advanced DNS. Current prescription (defer to the wizard if it differs):

| Type | Host               | Value                                                                    |
|------|--------------------|--------------------------------------------------------------------------|
| MX   | @                  | `smtp.google.com` (priority 1)                                           |
| TXT  | @                  | `v=spf1 include:_spf.google.com ~all`                                    |
| TXT  | google._domainkey  | DKIM value: Admin console → Apps → Gmail → Authenticate email → 2048-bit |

   After the DKIM TXT propagates, return to **Authenticate email** and click
   **Start authentication**; the status must read "Authenticating email" before
   the DMARC ramp below begins (unsigned mail + strict alignment = DMARC failures).
3. **DMARC** — fixed policy per CLAUDE.md's Stack, *not* wizard-prescribed (Google's
   guided setup starts at `p=none`; don't take that). Strict alignment is this
   runbook's deliberate choice:

| Type | Host    | Value                                                                       |
|------|---------|------------------------------------------------------------------------------|
| TXT  | _dmarc  | `v=DMARC1; p=quarantine; rua=mailto:sam@ismailsystems.com; adkim=s; aspf=s` |

4. **DMARC ramp:** after ~2 weeks of clean aggregate reports, change `p=quarantine` →
   `p=reject`.
5. Test with a personal account, both directions; both must land in inbox, not spam.

## 3. Variant domains ×3 — redirect stubs + receive-only mail (30 min)
The three misspelling variants (the esmail / ismael / esmael forms — full domains are
never written in this repo, per CLAUDE.md law 8). Each gets an HTTPS redirect stub and
receive-only mail forwarding. Variants never send mail.

**Per variant — redirect stub** (closes the HTTPS-redirect gap registrar forwarding
leaves: registrar redirects don't carry a certificate for the variant, so `https://`
visits would fail without this):

1. Create a public repo (any name; the variant domain itself must not appear in repo
   *contents*, though GitHub will write it into that stub repo's own `CNAME` file —
   that's the Pages mechanism, not this repo). Its entire content is one `index.html`:

   ```html
   <!doctype html>
   <html lang="en">
   <meta charset="utf-8">
   <meta http-equiv="refresh" content="0; url=https://ismailsystems.com/">
   <link rel="canonical" href="https://ismailsystems.com/">
   <title>ismailsystems.com</title>
   <p><a href="https://ismailsystems.com/">Continue to ismailsystems.com</a></p>
   </html>
   ```

2. Enable Pages (branch `main`, root), set that variant's apex as the custom domain,
   and at Namecheap give the variant the same four A records as the §1 table
   (plus, optionally, `www` CNAME → `ismailsystems.github.io.` for www-form typos).
   Enforce HTTPS once the certificate issues.

**Per variant — email (Namecheap, receive-only):**

| Setting        | Value                                                                 |
|----------------|-----------------------------------------------------------------------|
| Email forwarding | Domain tab → Redirect Email → catch-all `*` → `sam@ismailsystems.com` (Namecheap auto-inserts its forwarding MX; requires Namecheap's own nameservers — BasicDNS/PremiumDNS/FreeDNS, not third-party DNS) |
| TXT `@`        | `v=spf1 include:spf.efwd.registrar-servers.com -all` — the include authorizes only Namecheap's forwarders, which resend under this domain; their KB says forwarding breaks without it. The variant itself still never sends, and all other senders hard-fail. |
| TXT `_dmarc`   | `v=DMARC1; p=reject`                                                   |

**Test one variant end-to-end:** mail sent to any address at it arrives at sam@;
`https://` on the bare variant lands on `https://ismailsystems.com/` with a valid
certificate.

## 4. Verification pass (10 min)
- `https://ismailsystems.com/` and `/privacy.html` load over HTTPS; `www` and the
  `*.github.io` URL both end up at the apex.
- Toggle OS dark mode; the pages keep their light scheme (deliberate — `color-scheme: light`, no dark variant per design law 4).
- Tap the `tel:` links on a phone; (847) 906-2560 connects.
- Lighthouse (Chrome DevTools, **mobile**): ≥95 in all categories, per design law 3.
- Text the URL to yourself; the link preview shows title + description (the image
  arrives when G-OG closes).

## Appendix — `/assets/og.jpg` spec (gate G-OG resolved 2026-07; kept for future rebuilds)
1200×630 JPEG: photo left, "Ismail Systems" wordmark + one-liner right. Keep it lean
(target ≤150 KB; it's fetched only by link-preview scrapers, never by the page itself,
so it doesn't count against page weight). The swap action itself lives at the G-OG
marker in index.html and CLAUDE.md's registry — not here.
