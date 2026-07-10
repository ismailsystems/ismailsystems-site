# CLAUDE.md — ismailsystems-site

Context for any coding agent working in this repo. Read fully before editing.
**This repo may be public. Nothing enters it beyond what the deployed site itself reveals:
no business strategy, no personal or third-party context, no credentials, ever.**

## What this is
One hand-written page (`index.html`) + `privacy.html` + `/assets/` for ismailsystems.com.
v1 is complete. Work here is **event-driven maintenance at marked swap points** — not feature
development. If a request implies adding a page, a framework, a form, or a script, stop and
ask; the constraint is the product.

## Design laws (non-negotiable)
1. Zero JavaScript. (Sole permissible future exception: one cookieless analytics tag, if ever.)
2. No forms, cookies, webfonts, frameworks, builders, or external CSS. One inline `<style>`.
3. Total page weight ≤150KB; Lighthouse (mobile) ≥95 across all categories.
4. System font stack; palette: ink/paper plus navy `#1e3a5f` and brick red `#a63a2b`
   (owner decision 2026-07, replacing the single amber accent); 65ch measure; light
   scheme only (`color-scheme: light`). Do not reintroduce a dark scheme.
5. Semantic HTML; visible focus states; `tel:`/`mailto:` plus a single `sms:` link in
   the closer are the only CTAs (sms permitted by owner decision 2026-07).
6. First person singular throughout. Never "we".
7. **Truth-audit rule:** every sentence must be true on the day it ships. Copy versions with
   external reality (see swap registry) — never aspirationally.
8. Variant domains (esmail/ismael/esmael-systems.com) are never referenced in any file here.
   Canonical is `ismailsystems.com`, apex form.

## State-derivation rule (the markers are the ledger)
Current state is derived by grepping `index.html` for gate markers — not from this file,
not from DEPLOY.md, not from chat. A marker present = gate still open.
```
grep -n "G2:\|G3:\|DEMO_NUMBER\|SLOT:\|G-SAMEAS" index.html
```
Markers are **resolved, never deleted-in-passing**: perform the swap the comment describes,
remove the comment in the same commit, and name the gate in the commit message
(e.g., `g1: footer legal line — LLC filed`). Prose can go stale; markers cannot.

## Swap registry (trigger → action)
| Marker | Trigger (owner will state it) | Action |
|---|---|---|
| `G-PHOTO` | ~~daylight photo exists~~ **resolved 2026-07** | shipped as `/assets/sam.jpg` (17KB; webp spec waived — no encoder on build machine) |
| `G-OG` | ~~photo exists~~ **resolved 2026-07** | og.jpg shipped (photo left, wordmark + one-liner + phone right); meta tags live |
| `G1` | ~~LLC filed~~ **resolved 2026-07** | LLC filed; owner dropped the visible footer legal line entirely — the LLC name now stands in the JSON-LD and privacy.html (no file number displayed) |
| `G2` | agent answers (847) 906-2560 | swap "rings me directly" clause per comment |
| `G3` + `DEMO_NUMBER` | demo line live (owner provides number) | unhide `#hear`, fill number twice |
| `G-SAMEAS` | owner provides final LinkedIn/GBP URLs | add the `sameAs` array (marker now in index.html head) |

**Keep-in-sync (not a gate — a standing rule):** the QUOTE figures ($3,000 set-up /
$1,500 per 30 days / $4,500 guarantee) now live in three places — the visible `#deal`
table (**source of truth**), the JSON-LD `makesOffer` in the head (carries a `PRICE:`
comment), and `llms.txt`. If the owner re-quotes, change all three in the same commit,
or the structured data / AI-facing summary goes false while the page looks fine.

## Stack (as deployed — verified against live DNS and serving 2026-07-07; DEPLOY.md must match)
- **Hosting:** Cloudflare Pages, connected to this GitHub repo; pushes to `main`
  auto-deploy. Apex + `www` are Cloudflare-proxied (`www` 301 → apex; `http` 301 →
  `https`; Cloudflare-managed certificate). `404.html` in the repo root serves real
  404s (without it, Pages falls back to index.html with a 200). The `CNAME` file is a
  leftover from an earlier GitHub Pages plan that was never enabled — harmless.
- **DNS:** Cloudflare nameservers (the zone lives in the owner's Cloudflare account).
- **Email:** Zoho Mail on the canonical domain — MX `mx/mx2/mx3.zoho.com`; SPF
  `v=spf1 include:zohomail.com ~all`; DKIM published at `zoho._domainkey`. DMARC is
  currently `p=none` (rua → sam@); ramp to `p=quarantine` then `p=reject` once
  reports run clean.
- **Variant domains:** all three are registered, on the same Cloudflare account, and
  301-redirect to `https://ismailsystems.com/` with valid HTTPS (verified live).
  Their inbound-mail forwarding state is unverified — check before relying on it.
- **AI crawler policy:** Cloudflare Managed `robots.txt` is intentionally OFF in
  AI Crawl Control (changed and curl-verified 2026-07-09), so the repo's
  `robots.txt` is the live response. Keep it off if AI search / grounding citation is
  a goal; if the live file ever shows Cloudflare-prepended `Disallow` rules again,
  fix that in the dashboard before editing repo robots policy.

## Pre-commit checks (run before any commit touching HTML)
```
python3 - <<'EOF'   # tag balance + JSON-LD validity + weight
# (reuse the validation approach from repo history; assert total bytes <= 150_000)
EOF
grep -riE '\bwe\b' index.html privacy.html && echo "FAIL: plural voice" 
grep -riE "(esmail|ismael|esmael)systems" . --include='*.html' --include='*.md' --exclude='*.local.md' && echo "FAIL: variant leaked"
```
After deploy: Lighthouse mobile ≥95; text the URL to yourself and check the link preview.

## Known-stale items
- (none as of 2026-07-09 — the Stack section above records *observed* reality, verified
  with dig/curl. If infra docs and observation ever disagree again, re-verify live and
  update the docs; never document a plan as if deployed.)

## Session prompts (paste into Claude Code at repo root)
**Kickoff / any session:**
> Read CLAUDE.md fully. Derive current gate state via the state-derivation rule and
> `git log --oneline -10`; report it in one table before changing anything. Then do the
> highest-priority item: resolve any swap whose trigger I've stated in this session,
> else the topmost known-stale item. Run the pre-commit checks; commit with the gate
> name in the message. One concern per session. If a request conflicts with a design
> law, refuse and cite the law.

**Truth-audit (quarterly):**
> Read every sentence of index.html and privacy.html as claims. List any that could be
> false today given the gate states you derived. Propose minimal edits; change nothing
> until I confirm.
