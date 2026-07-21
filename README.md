# Granted Grant — landing site

Marketing site + service-request flow for **Granted Grant** (Jordi Ferrer Orri, self-employed sole
trader). Static HTML, no build step. **No bank details or home address are anywhere in this repo.**

- `index.html` — landing page
- `invoice.html` — public **request form** for full-application / Agent enquiries (emails the request to Jordi; shows no payment details)
- `upload.html` — **post-payment page** for the £249 review: confidentiality note + Tally upload-form embed. Used as the Stripe Payment Link success URL.
- `admin/invoice-maker.html` — **PRIVATE, local-only** invoice generator with bank details + address. Git-ignored via `admin/`. Never commit or publish it.
- `.nojekyll` — serve files as-is

## Live

**https://jordiferrero.github.io/grantedgrant/** — repo `jordiferrero/grantedgrant`.

## The flow (no backend, secrets stay off the web)

1. Client picks a service on `invoice.html` and fills the form.
2. On submit, the form POSTs to **FormSubmit** (`formsubmit.co`), which emails the request to
   `jordibiomr@gmail.com`. The client sees a "request received" confirmation and gets an
   auto-response. **They never see any bank details.**
3. Jordi opens `admin/invoice-maker.html` **locally** (double-click the file), pastes the client's
   details from the email, clicks Generate, then **Print → Save as PDF**, and forwards the invoice.
   Bank details + address are baked into that local file only.

### One-time setup (required before the form works)

FormSubmit needs the destination email activated once:
1. Submit the live form yourself once.
2. FormSubmit emails `jordibiomr@gmail.com` an activation link — click it.
3. Done. Future submissions arrive as a formatted table email. (If it ever fails, the form shows a
   `mailto:` fallback so no request is lost.)

Optional: replace the email in `invoice.html` (`ENDPOINT`) with the FormSubmit random alias from the
activation email, so the address isn't in the page source.

## £249 review — Stripe payment + upload flow (no-code)

Automates payment for the review; **fulfilment stays manual** (Jordi runs the review and emails it back).
Payment (Stripe) and files (Tally) are separate steps stitched together — Stripe Checkout can't take
file uploads. Nothing here needs a server; the site stays on GitHub Pages.

**Flow:** Review CTA → Stripe Payment Link (card) → success redirect to `upload.html` → client uploads
draft in the embedded Tally form → Tally emails the file to Jordi → Jordi reviews + emails report back.

### Status

- ✅ **Stripe (LIVE) — done.** Created via the Stripe MCP on account `acct_1Tvj6XJd8PcMbTal` (Jordi Ferrer):
  - Review £249 → `https://buy.stripe.com/aFadRa3lxdQR4StaHc4AU00` (product `prod_UvaKGMIOdToxCN`, price `price_1TvjB0Jd8PcMbTalRuRb2QtO`)
  - Review + 24h rush £328 → `https://buy.stripe.com/aFaaEYg8j6opacN6qW4AU01` (product `prod_UvaKdA4SjUFNRP`, price `price_1TvjB1Jd8PcMbTall4IqUsLG`)
  - Both are **live**, card payments, GBP, one-time, with after-payment redirect to `upload.html`.
- ✅ **Site CTAs — done.** All review CTAs now point to the £249 link; the review card also links the £328 rush link.
- ⏳ **Tally — still to do (you).** Create a form (fields **Name, Email, Competition, Deadline, File upload**),
  turn on email notifications to `jordibiomr@gmail.com`, Share → **Embed**, and send me the embed link.
  I'll paste it into the `iframe src` in `upload.html` (TODO comment marks the spot) and remove the interim
  email-only fallback. **Until then**, `upload.html` tells paying customers to email their draft to you — so
  the flow already works end-to-end (pay → redirect → email files).

> Note: the account was in LIVE mode (no test-mode connection available via this MCP), so these are real
> links. To trial the flow, do one £249 self-purchase and refund it (Stripe keeps ~£3.95 in fees), or just
> click through to confirm checkout + redirect without paying.

Confidentiality: Stripe never sees the files; the draft goes via Tally to Jordi's inbox (stated on
`upload.html`, with a 14-day deletion note and an NDA-on-request offer). Full-application and Agent
sales stay on the manual request-form / email / call route by design.

## Fully-automated version (optional, later)

To have the finished PDF **auto-generated and attached** to your email with bank details kept in a
secret — the thing GitHub Pages/Actions can't do — you need a small serverless function
(Cloudflare Workers / Netlify / Vercel free tier): the form POSTs to it, the function holds the bank
details in an environment secret, builds the invoice, and emails it to you. Ask and it can be added
without changing hosts for the rest of the site.

## Notes

- No testimonials section yet (removed until real, attributable quotes exist — never fabricate).
- **Not VAT registered** — the invoice tool correctly states "no VAT is charged." Revisit if/when registered.

## Vanity URL / custom domain

Current URL is `jordiferrero.github.io/grantedgrant`. For `grantedgrant.github.io` you'd need a
separate GitHub account named `grantedgrant` with a repo `grantedgrant.github.io`. Or buy a domain,
add a `CNAME` file, and point DNS at Pages.

## Redeploy

```bash
git add . && git commit -m "update" && git push   # admin/ is ignored automatically
```
