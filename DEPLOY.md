# DEPLOY.md — ismailsystems.com launch runbook (~90 min of account actions)

Everything in this repo is finished. What remains is account work only you can do.
Reference: ismailsystems-website-implementation.md (the guide) for full rationale.

## 0. Repo (10 min)
1. Create private GitHub repo `ismailsystems-site`; commit `index.html`, `privacy.html`, `/assets/favicon.svg`, this file.
2. Every future edit is a commit on `main`. The site is code; git history is your deploy log and rollback.

## 1. Cloudflare — canonical zone (20 min)
1. Add site `ismailsystems.com` to Cloudflare (free plan); at your registrar, switch the domain's nameservers to the two Cloudflare gives you. (Repeat the add-site step for the 3 variants — §3.)
2. **Pages:** Workers & Pages → Create → Pages → connect the GitHub repo → framework preset "None" → deploy. Then Custom domains → add `ismailsystems.com` and `www.ismailsystems.com`.
3. **Redirect rule** (Rules → Redirect Rules): `www.ismailsystems.com/*` → `https://ismailsystems.com/$1`, 301, preserve query.
4. **Web Analytics** (optional, cookieless): Analytics → Web Analytics → add site → it gives a snippet; per doctrine you may skip entirely. If used, paste the single script tag just before `</body>` — the only JS on the page, and it's cookieless.

## 2. Email — canonical only (20 min)
1. Google Workspace Business Starter on ismailsystems.com; create `sam@ismailsystems.com`.
2. In Cloudflare DNS add exactly what the Workspace wizard prescribes: the MX record(s), then
   - TXT `@` : `v=spf1 include:_spf.google.com ~all`
   - DKIM: Admin console → Apps → Gmail → Authenticate email → generate 2048-bit → add the TXT it gives you.
   - TXT `_dmarc` : `v=DMARC1; p=quarantine; rua=mailto:sam@ismailsystems.com; adkim=s; aspf=s`
     (**Ramp:** after 2 weeks of clean reports, change `p=quarantine` → `p=reject`.)
3. Test: send to and from sam@ with a personal account; both directions must land in inbox, not spam.

## 3. Variant zones ×3 (esmail / ismael / esmael) (15 min total)
For each of the three, in its Cloudflare zone:
1. **Email Routing** → enable → it auto-inserts its MX + SPF → add catch-all rule: `*@<variant>` → forward to `sam@ismailsystems.com` → click the verification email.
2. TXT `_dmarc` : `v=DMARC1; p=reject`   (variants never send; reject spoofing).
3. **Redirect rule:** `<variant>/*` and apex → `https://ismailsystems.com` 301 (drop path).
4. Test one: email `anything@esmailsystems.com` from a personal account → arrives at sam@; open `https://ismaelsystems.com` → lands on the canonical site.

## 4. Verification pass (10 min)
- `https://ismailsystems.com` and `/privacy.html` load; dark mode looks right (toggle OS theme).
- Phone links: tap (847) 906-2560 on your phone — it should ring your cell via the Twilio forward. **If the forward isn't live-tested yet, do that first** — this same test clears the GBP gate.
- Lighthouse (Chrome DevTools → Lighthouse, mobile): expect ≥95 across the board; the architecture makes failure hard.
- Text the URL to yourself: the iMessage preview shows title + description (image comes later — §6).

## 5. Hook-ups (5 min)
- Paste `https://ismailsystems.com` into the GBP website field (the wizard you had open).
- Add the site to your LinkedIn contact-info section. (The LLC *position* on LinkedIn stays gated on the agreement — unchanged.)

## 6. Swap-point registry (all future edits are one-line changes, marked in-file)
| Marker in index.html | Trigger | Action |
|---|---|---|
| `G-PHOTO` comment | daylight photo exists | export WebP ≤120KB → `/assets/sam.webp` → uncomment the `<img>` |
| `G-OG` comment | photo exists | build 1200×630 `/assets/og.jpg` (photo left, "Ismail Systems" + one-liner right) → uncomment the two meta tags → re-text yourself the URL |
| `G1` comment (footer) | **LLC filed** | swap footer line to the full legal string with the SOS file number |
| `G2` comment (footer) | M2 cutover (agent answers 906-2560) | swap "rings me directly" clause for the agent sentence |
| `G3` + `DEMO_NUMBER` | M4 demo pool live | unhide `#hear`, fill the number in both places |
| JSON-LD | LinkedIn/GBP URLs final | add `"sameAs":["<GBP URL>","<LinkedIn URL>"]` |

## 7. Rituals (from the guide)
- Monthly: Google your name, "Ismail Systems," and (847) 906-2560 (incl. a spam-lookup site). Target SERP: LinkedIn + this site + GBP — three congruent results.
- Quarterly: truth-audit every sentence on the page against reality.

## Letter gate reminder
The site may be live today. **Letters do not mail** until: G1 footer real (LLC filed), photo live, brother-test passed, and the audit shortlist exists. The LLC filing waits on the employment-agreement read — the standing two-minute version: paste the invention-assignment and outside-activities paragraphs into chat.
