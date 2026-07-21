# Granted Grant — landing site

Marketing site + service-request flow for **Granted Grant** (Jordi Ferrer Orri, self-employed sole
trader). Static HTML, no build step. **No bank details or home address are anywhere in this repo.**

- `index.html` — landing page
- `invoice.html` — public **request form** (emails the request to Jordi; shows no payment details)
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
