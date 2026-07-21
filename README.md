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

### Setup checklist (needs your Stripe + Tally logins)

1. **Stripe** (as a sole trader — use the same bank account):
   - Create two **Payment Links**: `Review — £249` and `Review + 24h rush — £328`.
   - Optionally add custom fields (Competition, Deadline) and enable customer email + receipts.
   - Set each link's **after-payment redirect** to `https://jordiferrero.github.io/grantedgrant/upload.html`.
   - Copy the two Payment Link URLs.
2. **Tally** (free): create a form with fields **Name, Email, Competition, Deadline, File upload**;
   turn on **email notifications** to `jordibiomr@gmail.com`; Share → **Embed** → copy the embed link.
   - Paste it into the `iframe src` in `upload.html` (see the TODO comment there).
3. **Send me the two Stripe Payment Link URLs** and I'll swap every review CTA
   (`invoice.html?service=review`) over to the £249 link and wire the rush link in. Until then the CTAs
   keep pointing at the request form so nothing is broken.

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
