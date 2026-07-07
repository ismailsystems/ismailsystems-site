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
5. Semantic HTML; visible focus states; `tel:`/`mailto:` are the only CTAs.
6. First person singular throughout. Never "we".
7. **Truth-audit rule:** every sentence must be true on the day it ships. Copy versions with
   external reality (see swap registry) — never aspirationally.
8. Variant domains (esmail/ismael/esmael-systems.com) are never referenced in any file here.
   Canonical is `ismailsystems.com`, apex form.

## State-derivation rule (the markers are the ledger)
Current state is derived by grepping `index.html` for gate markers — not from this file,
not from DEPLOY.md, not from chat. A marker present = gate still open.
```
grep -n "G-PHOTO\|G-OG\|G1:\|G2:\|G3:\|DEMO_NUMBER" index.html
```
Markers are **resolved, never deleted-in-passing**: perform the swap the comment describes,
remove the comment in the same commit, and name the gate in the commit message
(e.g., `g1: footer legal line — LLC filed`). Prose can go stale; markers cannot.

## Swap registry (trigger → action)
| Marker | Trigger (owner will state it) | Action |
|---|---|---|
| `G-PHOTO` | ~~daylight photo exists~~ **resolved 2026-07** | shipped as `/assets/sam.jpg` (17KB; webp spec waived — no encoder on build machine) |
| `G-OG` | photo exists | build `/assets/og.jpg` 1200×630, uncomment the two meta tags |
| `G1` | ~~LLC filed~~ **resolved 2026-07** | LLC filed; owner dropped the visible footer legal line entirely — the LLC name now stands in the JSON-LD and privacy.html (no file number displayed) |
| `G2` | agent answers (847) 906-2560 | swap "rings me directly" clause per comment |
| `G3` + `DEMO_NUMBER` | demo line live (owner provides number) | unhide `#hear`, fill number twice |
| JSON-LD `sameAs` | owner provides final LinkedIn/GBP URLs | add the array |

## Stack (settled; DEPLOY.md must match this)
- **Hosting:** GitHub Pages from this repo, `main` branch. Custom domain: apex
  `ismailsystems.com` (4 GH Pages A records at the DNS host) + `www` CNAME → `<owner>.github.io`;
  Enforce HTTPS once the cert issues.
- **DNS + email:** Namecheap. Canonical domain: Google Workspace MX/SPF/DKIM per provider
  wizard; DMARC `p=quarantine` ramping to `p=reject`. Variants: registrar catch-all
  *receive-only* forwarding to sam@; `v=spf1 -all` + DMARC `p=reject` (they never send).
- **Variant redirects:** three stub repos (one `index.html` each: instant `meta refresh` to
  `https://ismailsystems.com/` + canonical link + plain anchor fallback), each with the
  variant as its Pages custom domain — this closes the HTTPS-redirect gap registrar
  forwarding leaves. (If the owner has chosen registrar-forwarding instead, he will say so;
  default is stubs.)

## Pre-commit checks (run before any commit touching HTML)
```
python3 - <<'EOF'   # tag balance + JSON-LD validity + weight
# (reuse the validation approach from repo history; assert total bytes <= 150_000)
EOF
grep -riE '\bwe\b' index.html privacy.html && echo "FAIL: plural voice" 
grep -ri "esmailsystems\|ismaelsystems\|esmaelsystems" . --include='*.html' && echo "FAIL: variant leaked"
```
After deploy: Lighthouse mobile ≥95; text the URL to yourself and check the link preview.

## Known-stale items (first session resolves)
- `DEPLOY.md` describes a prior hosting stack (Cloudflare). Regenerate it to match the
  **Stack** section above, including the three stub repos' contents and DNS record tables.
- Photo / OG / deploy status: derive from markers and `git log`, then report.

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
