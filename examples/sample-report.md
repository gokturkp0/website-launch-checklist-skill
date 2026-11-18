# Website Launch Checklist Report – example-bakery.com

> *Fictional example. The site and answers are made up to show what the skill produces.*

**Date:** 2026-11-18 · **Site type:** Online shop · **Platform:** WordPress + WooCommerce · **Audience:** Turkey, EU

## Summary

**Readiness: 50%** (10 passed · 4 partial · 4 gaps · 2 unknown · 0 not applicable)
**Security Deep Dive: 43%** (3 passed · 2 partial · 2 gaps · 5 not applicable)

The basics are in place: HTTPS, a responsive theme and a privacy policy. Two issues should block launch: analytics and the Meta Pixel load before cookie consent, and the WordPress admin has no multi-factor authentication. Start with those two, then add the Turkish distance-sales documents to checkout and set a WooCommerce webhook secret. Because the shop takes payments and has customer accounts, the Security Deep Dive was included.

| Area | Status |
|---|---|
| Security (Q1–Q8) | ❌ 4/8 passed |
| Legal & Privacy (Q9–Q14) | ❌ 2/6 passed |
| Accessibility (Q15) | ⚠️ 0/1 passed |
| Performance & SEO (Q16–Q18) | ✅ 3/3 passed |
| Operations (Q19–Q20) | ⚠️ 1/2 passed |
| Security Deep Dive (S1–S12) | ❌ 3/7 passed |

## 🔴 Critical – fix before launch

### Q10. Cookie and tracking consent – Gap
**Why it matters:** Loading Google Analytics and the Meta Pixel before consent likely breaches ePrivacy/KVKK consent rules for EU and Turkish visitors.
**What to do:**
- [ ] Install a consent management plugin that blocks scripts until opt-in (look for one with Google Consent Mode v2 support).
- [ ] Add "Reject all" to the first layer, equal in prominence to "Accept all".
- [ ] Add a "Cookie settings" link to the footer.
- [ ] Publish a cookie list (name, provider, purpose, duration).
- [ ] Confirm with a qualified lawyer for Turkey and the EU.
**Verify with:** Open the site in a private window and check the browser DevTools Network tab: no analytics or pixel requests should fire before you click Accept.

### Q4. Authentication and access control – Partial
**Why it matters:** The shop stores customer data. A guessed or leaked admin password would expose all of it.
**What to do:**
- [ ] Enable 2FA for every WordPress administrator and shop manager (via a reputable 2FA plugin).
- [ ] Enable MFA on the hosting panel, domain registrar and admin email accounts.
- [ ] Add login rate limiting.
- [ ] Remove the shared `admin` account; give each person their own account with the lowest role they need.
- [ ] Disable XML-RPC if no app uses it, and block username discovery via `/?author=N`.
**Verify with:** Log in as each admin and confirm a second factor is required.

### Q13. E-commerce, payments and consumer rights – Partial
**Why it matters:** Turkish consumer law requires a preliminary information form and a distance-sales contract before payment. Without them the 14-day withdrawal period may not start running.
**What to do:**
- [ ] Add the *ön bilgilendirme formu* and *mesafeli satış sözleşmesi* to checkout, with acceptance before the pay button.
- [ ] Check your ETBİS registration.
- [ ] Show the total price including delivery before the order button.
- [ ] Confirm with a qualified lawyer for Turkey and the EU.

### S10. Payments, webhooks and business logic – Partial
**Why it matters:** Without a webhook secret, a forged "payment completed" request could mark unpaid orders as paid.
**What to do:**
- [ ] Set a secret on every WooCommerce webhook and make sure the receiving side verifies the signature.
- [ ] Confirm the payment gateway plugin marks orders paid only after the provider's verified callback.
- [ ] Ask your payment provider which PCI DSS self-assessment questionnaire applies (often SAQ A for hosted/redirect checkout) and complete it.
- [ ] Turn off unused payment gateways.
**Verify with:** Your payment provider's webhook test tool, on staging.

## 🟠 High – fix before or right after launch

### Q7. Backups and recovery – Unknown
- [ ] Check the hosting panel for automatic backups and their retention.
- [ ] Add an independent off-site backup (e.g. a backup plugin sending to object storage).
- [ ] Test a full restore to a staging site.

### Q12. Personal data handling – Gap
- [ ] Make the newsletter checkbox on checkout unticked and separate from accepting the terms.
- [ ] Separate the KVKK *aydınlatma metni* from the *açık rıza* text.
- [ ] Create a privacy-request email address and a one-page breach response plan.
- [ ] Sign DPAs with your hosting, email and payment providers.
- [ ] Confirm with a qualified lawyer for Turkey and the EU.

### Q14. Email marketing – Gap
- [ ] Register commercial message consents in İYS.
- [ ] Use double opt-in and store the consent date, source and wording.
- [ ] Add one-click unsubscribe to all marketing emails.
- [ ] Confirm with a qualified lawyer for Turkey.

### Q15. Accessibility – Partial
*(Priority lowered one level from Critical: the EAA micro-enterprise exemption may apply to this shop – confirm with a qualified lawyer.)*
- [ ] Run axe DevTools and WAVE on the home, product, cart and checkout pages; fix all errors.
- [ ] Add alt text to product images.
- [ ] Do a keyboard-only test of the checkout.
- [ ] Publish an accessibility statement (likely expected under the European Accessibility Act for EU e-commerce, unless the micro-enterprise exemption applies; confirm with a qualified lawyer).

### Q8. Domain, DNS and email security – Partial
- [ ] Publish a DMARC record (start with `p=none` and a reporting address, then tighten).
- [ ] Enable registrar lock and MFA on the registrar account.

### Q2. Security headers – Unknown
*(Unknown: priority not raised for customer accounts until the headers are checked.)*
- [ ] Check the site in Mozilla HTTP Observatory (only on systems you own or are authorised to test).
- [ ] Add `X-Content-Type-Options`, `Referrer-Policy` and `Permissions-Policy`; roll out CSP in report-only mode first.

### Q19. Monitoring and incident response – Gap
- [ ] Set up uptime monitoring with alerts.
- [ ] Publish `/.well-known/security.txt` with a contact and an expiry date.
- [ ] Write a one-page incident response plan.

### S12. Team and account hygiene – Gap
- [ ] List everyone with access to WordPress, hosting, the registrar, the payment provider and email.
- [ ] Move shared passwords into a team password manager.
- [ ] Write a short offboarding checklist (remove access, rotate shared keys).

### S7. Supply chain and third-party scripts – Partial
- [ ] Inventory the scripts on checkout pages and remove any not needed for payment.
- [ ] Give each third-party script an owner and remove any that no one owns.

### S11. Security testing – Gap
- [ ] Run an OWASP ZAP baseline scan against a staging copy (only on systems you own or are authorised to test).
- [ ] For a shop with customer accounts, consider a small penetration test before peak season.

## 🟡 Medium – fix in the first weeks

None 🎉

## 🟢 Low – polish

None 🎉

## ✅ What you already have in place

- Q1. HTTPS and TLS – Let's Encrypt via host, auto-renew, HTTP redirect.
- Q3. Updates – weekly plugin updates with alerts.
- Q5. Forms – Turnstile on contact form, WooCommerce nonces.
- Q6. Secrets – no keys in theme; debug off; staging password-protected.
- Q9. Privacy policy – present and linked in the footer.
- Q11. Legal pages – terms, contact and company details present.
- Q16. Performance – passes Core Web Vitals on mobile.
- Q17. Mobile – tested on iOS and Android.
- Q18. SEO – sitemap submitted, Search Console verified.
- Q20. Launch readiness – link check and test orders done.
- S1. Access control – tested with two customer accounts; no cross-account access.
- S3. Account flows – WooCommerce defaults, reset links expire.
- S4. Database – managed hosting, database not publicly reachable.

**Not applicable (deep dive):** S2 API (no custom API), S5 server hardening (managed hosting), S6 cloud storage (none used), S8 uploads/SSRF (no user uploads), S9 error handling (no custom code beyond stock WooCommerce).

## ❓ Not sure / not assessed

- **Q2 Security headers:** run `curl -I https://example-bakery.com` and look for `content-security-policy`, `x-content-type-options` and `referrer-policy`.
- **Q7 Backups:** look for a "Backups" section in your hosting control panel.

## Suggested 30-day plan

- **Week 1:** Cookie consent (Q10), admin MFA (Q4), checkout legal documents (Q13), webhook secrets (S10)
- **Week 2:** Backups (Q7), data handling (Q12), İYS and email consent (Q14), DMARC (Q8), security headers (Q2)
- **Weeks 3–4:** Accessibility fixes (Q15), monitoring and security.txt (Q19), team hygiene (S12), script inventory (S7), security testing (S11)
- **Ongoing:** monthly updates, quarterly restore test, yearly policy and accessibility review.

---

> **Disclaimer:** This report was generated from your own answers to a general-purpose checklist. It is provided for informational purposes only and does not constitute legal, security, compliance or other professional advice. It is not an audit, penetration test or certification and does not guarantee that your website is secure, accessible or compliant with any law or standard. Laws and best practices vary by jurisdiction and change over time; the checklist may be incomplete or out of date. You remain solely responsible for your website and for any decisions you make based on this report. The authors and contributors accept no liability for any loss or damage arising from its use. Consult a qualified lawyer and security professional before relying on it. See DISCLAIMER.md in the skill folder.
