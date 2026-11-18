# The 20 Questions

Each question has:

- **Area** and **Severity if missing** (Critical / High / Medium / Low)
- **Ask** – the question to put to the user, in plain language
- **What "Yes" means** – the sub-checks that must all be true for a full "Yes"
- **If missing** – the gaps to report and the concrete actions to recommend
- **Applies when** – skip (mark N/A) if the profile says it does not apply

Severity guide:
- **Critical** – exploitable or legally exposed right now; fix before launch.
- **High** – significant risk or likely complaint; fix before or immediately after launch.
- **Medium** – quality, trust or ranking problem; fix within the first weeks.
- **Low** – polish; schedule it.

Severity can be raised one level when the profile increases the risk and the question's own severity line doesn't already account for it (e.g. Q2/Q5 for a site with user accounts, Q12 for sites handling health, financial or children's data).

---

## Section A – Security

### Q1. HTTPS and TLS
- **Area:** Security · **Severity if missing:** Critical
- **Ask:** "Is every page served over HTTPS, with HTTP automatically redirected to HTTPS, and do your certificates renew automatically?"
- **What "Yes" means:**
  - Valid certificate on the apex domain and `www` (and every public subdomain).
  - `http://` → `https://` 301 redirect on the same host.
  - `Strict-Transport-Security` header (ramp up `max-age`, e.g. 1 week → 1 month, then `max-age=31536000`; add `includeSubDomains` only when all subdomains serve HTTPS; `preload` only after careful review – removal is slow).
  - Only TLS 1.2 and 1.3 enabled; SSL Labs grade A or better.
  - Certificate renewal is automated (ACME/hosting provider). Public CA certificate lifetimes are shrinking (Let's Encrypt is moving from 90 to 45 days), so manual renewal is not sustainable.
  - No mixed content (HTTP images/scripts on HTTPS pages).
- **If missing:**
  - Enable HTTPS through the host/CDN or an ACME client (e.g. Certbot, Caddy).
  - Add a permanent redirect and the HSTS header.
  - Test with SSL Labs Server Test; fix any grade below A.
  - Search the codebase for hard-coded `http://` asset URLs.

### Q2. Security headers and cookies
- **Area:** Security · **Severity if missing:** High
- **Ask:** "Does your site send standard security headers (such as Content-Security-Policy), and are cookies set with Secure, HttpOnly and SameSite?"
- **What "Yes" means:**
  - `Content-Security-Policy` without `'unsafe-inline'` for scripts where possible (nonces/hashes); `frame-ancestors` set (or `X-Frame-Options: DENY/SAMEORIGIN`).
  - `X-Content-Type-Options: nosniff`, `Referrer-Policy: strict-origin-when-cross-origin` (or stricter), `Permissions-Policy` limiting unused features.
  - `Server` / `X-Powered-By` version banners removed.
  - Session/auth cookies: `Secure; HttpOnly; SameSite=Lax` (or `Strict`); `__Host-` prefix where possible.
  - Third-party scripts/styles from CDNs use Subresource Integrity (`integrity` + `crossorigin`).
- **If missing:**
  - Add headers at the web server/CDN/framework level.
  - Roll out CSP as `Content-Security-Policy-Report-Only` first, review reports, then enforce.
  - Verify with Mozilla HTTP Observatory and securityheaders.com.

### Q3. Updates and dependencies
- **Area:** Security · **Severity if missing:** Critical
- **Ask:** "Are your CMS, plugins, themes, frameworks and libraries up to date, and do you get automatic alerts about vulnerable dependencies?"
- **What "Yes" means:**
  - No end-of-life runtime (PHP, Node.js, Python, etc.) or CMS version.
  - Unused plugins/themes removed, not just disabled.
  - Dependency alerts enabled (Dependabot, Renovate, `npm audit`, `composer audit`, WordPress update notifications, etc.).
  - A named person and a routine (e.g. weekly) for applying updates; security patches applied within days.
- **If missing:**
  - Inventory all components and versions; upgrade anything end-of-life.
  - Enable automated dependency scanning in the repository/hosting panel.
  - Remove abandoned plugins; prefer well-maintained alternatives.
  - Relates to OWASP Top 10 2025 *A03 Software Supply Chain Failures*.

### Q4. Authentication and access control
- **Area:** Security · **Severity if missing:** Critical (if the site has logins/admin) · High otherwise
- **Ask:** "Are admin and user logins protected with multi-factor authentication, strong password rules and protection against brute-force attacks – and can each user only see and change what they are allowed to?"
- **What "Yes" means:**
  - MFA (or passkeys) required for every admin/editor/hosting/registrar account.
  - Password rules follow NIST SP 800-63B-4: long passwords allowed (at least 64 chars), minimum 15 chars for password-only login (8 when used with MFA), checked against breached-password lists, no forced periodic rotation, no arbitrary composition rules.
  - Passwords hashed with Argon2id, scrypt or bcrypt – never stored in plain text or with fast hashes (MD5/SHA-1).
  - Rate limiting/lockout on login, password reset and OTP endpoints.
  - Session ID regenerated after login; sessions expire.
  - Admin panel not on a predictable default path or restricted by IP/VPN/SSO where practical.
  - Least privilege: each person has their own account with only the role they need; ex-staff removed.
  - **Access control (OWASP Top 10 2025 A01, the #1 web risk):** permissions are checked on the server for every request, not just hidden in the UI; deny by default; a user cannot view or change another user's orders, files or profile by changing an ID in the URL or request (IDOR).
- **If missing:**
  - Turn on MFA everywhere (site admin, hosting, DNS/registrar, email, Git).
  - Add rate limiting (framework middleware, WAF rule, or plugin).
  - Audit user accounts and roles; delete shared and stale accounts.
  - If you build auth yourself, review the OWASP Authentication and Password Storage cheat sheets – or use a proven provider.
  - Test access control with two ordinary accounts on staging: log in as user A and try to open user B's records by changing IDs. Any success is a Critical gap. (Security Deep Dive S1 covers this in depth.)

### Q5. Forms and user input
- **Area:** Security · **Severity if missing:** High
- **Ask:** "Are all forms and inputs (contact, search, login, uploads, comments) validated on the server and protected against spam bots and CSRF?"
- **What "Yes" means:**
  - Server-side validation of every input (client-side validation alone is not enough).
  - Parameterized queries / ORM – no string-built SQL.
  - Output encoding to prevent XSS; rich text sanitized.
  - CSRF protection on state-changing requests.
  - Bot protection on public forms (e.g. Cloudflare Turnstile, hCaptcha, reCAPTCHA, honeypots) plus rate limiting.
  - File uploads: type and size checked server-side, stored outside the web root or on object storage, never executed.
  - Error pages do not leak stack traces or SQL errors.
- **If missing:**
  - Add server-side validation and use framework-provided CSRF protection.
  - Add bot protection to every public form.
  - Run an automated scan (e.g. OWASP ZAP baseline) against a staging copy **you own**.

### Q6. Secrets and exposed files
- **Area:** Security · **Severity if missing:** Critical
- **Ask:** "Have you made sure no secrets (API keys, passwords) are in your code or frontend, and that files like `.env`, `.git/`, backups or debug pages are not publicly reachable?"
- **What "Yes" means:**
  - Secrets live in environment variables or a secrets manager, not in the repository or client-side JavaScript.
  - `/.env`, `/.git/`, `/backup.zip`, `/phpinfo.php`, database dumps, etc. return 403/404.
  - Debug mode / verbose errors are off in production.
  - Directory listing is disabled.
  - Staging/dev environments are password-protected and `noindex`.
  - Secret scanning enabled on the repository (e.g. GitHub secret scanning, gitleaks).
- **If missing:**
  - Rotate **any** key that was ever committed or shipped to the browser – deleting it from the repo is not enough.
  - Block dotfiles and backup extensions at the web server.
  - Turn off debug mode; add HTTP auth to staging.

### Q7. Backups and recovery
- **Area:** Security / Reliability · **Severity if missing:** High
- **Ask:** "Do you have automatic backups of your files and database, stored somewhere separate, and have you actually tested restoring one?"
- **What "Yes" means:**
  - Automated, scheduled backups of database **and** uploaded files/code/config.
  - 3-2-1 approach: 3 copies, 2 different media/providers, 1 off-site (ideally immutable or versioned against ransomware).
  - Backups encrypted and access-restricted.
  - A restore has been tested end-to-end in the last few months; recovery time is known.
- **If missing:**
  - Enable host-level backups **and** an independent off-site copy.
  - Schedule a restore test and write down the steps.

### Q8. Domain, DNS and email security
- **Area:** Security · **Severity if missing:** High
- **Ask:** "Are your domain and DNS accounts locked down (MFA, auto-renew, registrar lock), and is your email domain protected with SPF, DKIM and DMARC?"
- **What "Yes" means:**
  - Domain on auto-renew with a valid payment method; registrar lock enabled.
  - MFA on the registrar and DNS provider accounts.
  - SPF, DKIM (2048-bit) and DMARC records published (Gmail/Yahoo bulk-sender rules require them); DMARC moving from `p=none` towards `quarantine`/`reject`.
  - CAA record listing the CAs allowed to issue for the domain.
  - DNSSEC enabled if the registrar/DNS provider supports it.
  - Domains that send no email have a "null" SPF (`v=spf1 -all`) and DMARC `p=reject`.
- **If missing:**
  - Enable auto-renew, registrar lock and MFA today – domain loss takes the whole site down.
  - Add SPF/DKIM via the email provider; publish DMARC with a reporting address; review reports and tighten.
  - Add CAA records.

---

## Section B – Legal and privacy

> Laws differ by country and change often. Report legal gaps as "likely required – confirm with a qualified lawyer", never as definitive legal advice.

### Q9. Privacy policy
- **Area:** Legal · **Severity if missing:** Critical (if any personal data is collected – including analytics, forms or server logs)
- **Ask:** "Do you have an up-to-date privacy policy that is linked from every page and accurately describes what personal data you collect, why, and who you share it with?"
- **What "Yes" means:**
  - Linked in the footer of every page and near every data-collection form.
  - States: who the controller is and how to contact them (and DPO if any); what data and why; legal basis (GDPR); recipients/processors (hosting, analytics, email, payment); international transfers and safeguards; retention periods; user rights and how to exercise them; right to complain to a regulator; use of cookies; automated decision-making if any.
  - Matches reality – every tool actually on the site is covered.
  - **Turkey (KVKK):** a separate *aydınlatma metni* (information notice) is provided; explicit consent (*açık rıza*), where needed, is asked separately and not bundled with the notice or with acceptance of terms.
  - **California (CCPA/CPRA):** categories of personal information, "Do Not Sell or Share"/"Your Privacy Choices" link if applicable.
- **If missing:**
  - List every tool/service that touches personal data, then write or update the policy from that list.
  - Have it reviewed by a lawyer for each market you target. Policy generators are a starting point, not an endpoint.

### Q10. Cookie and tracking consent
- **Area:** Legal · **Severity if missing:** High (Critical for EU/UK/Turkey audiences using ads or analytics)
- **Ask:** "If you use analytics, ads, pixels or other non-essential cookies, do visitors have to opt in before they load, with a 'Reject all' option as easy as 'Accept all'?"
- **What "Yes" means:**
  - Non-essential cookies/scripts (analytics, ads, social pixels, heatmaps) do **not** load before consent (EU/UK ePrivacy, KVKK).
  - Banner offers "Reject all" on the first layer, as prominent as "Accept all"; no pre-ticked boxes.
  - Consent can be withdrawn as easily as given (e.g. a "Cookie settings" link in the footer).
  - Consent choices are recorded.
  - If using Google tags: Consent Mode v2 configured (`ad_user_data`, `ad_personalization` default to denied for EEA/UK).
  - **US/California:** Global Privacy Control (GPC) signals honored as an opt-out of sale/sharing.
  - A cookie list (name, provider, purpose, duration) is published.
- **If missing:**
  - Scan the site to inventory cookies and third-party scripts.
  - Install a consent management platform (CMP) and block tags until consent.
  - Consider privacy-friendly, cookieless analytics to reduce consent needs.

### Q11. Legal pages and business identity
- **Area:** Legal / Trust · **Severity if missing:** High
- **Ask:** "Does your site have Terms of Use, clear contact details and, where required, a legal notice (imprint) identifying the business?"
- **What "Yes" means:**
  - Terms of use / terms of service (mandatory in practice for accounts, SaaS, user content, shops).
  - A real contact method (email/form, address; phone where expected).
  - **Germany/Austria:** Impressum reachable within two clicks (Germany: § 5 DDG, which replaced § 5 TMG).
  - EU businesses / e-commerce: company name, registered address, registration number, VAT ID where applicable.
  - Image, font, icon and code licences cover how they are used; third-party credits where required.
  - **AI disclosure (EU AI Act Art. 50, from 2 Aug 2026):** chatbots tell users they are talking to an AI; AI-generated or manipulated media (deepfakes) is labelled. May apply – check scope with a professional (duties differ for providers and deployers, and timing may be changed by EU "Digital Omnibus" proposals).
- **If missing:**
  - Add Terms, Contact and (where relevant) Imprint pages linked in the footer.
  - Audit media/font licences; replace anything unlicensed.
  - Add an "AI assistant" label to chatbots and mark AI-generated imagery where required.

### Q12. Personal data handling
- **Area:** Legal / Privacy · **Severity if missing:** High
- **Ask:** "Do you collect only the data you need, use unticked consent checkboxes where consent is required, and have a process to answer data access or deletion requests and to report data breaches?"
- **What "Yes" means:**
  - Forms ask only for data actually needed (data minimization); optional fields marked.
  - Consent checkboxes (marketing, special categories) are unticked by default and separate from accepting terms.
  - A way for people to request access, correction, deletion and export of their data, and a person responsible for answering within the legal deadline (GDPR: one month).
  - Data processing agreements (DPAs) with processors (hosting, email, CRM, analytics).
  - Retention periods defined and old data deleted.
  - A breach response plan (GDPR: notify the supervisory authority within 72 hours where required).
  - **Children:** if the site targets or knowingly collects data from children, COPPA (US, under 13) and GDPR Art. 8 (EU, 13–16 depending on member state) parental-consent rules are handled.
  - **Turkey:** VERBİS registration checked (obligation depends on size/type), KVKK breach notification process.
- **If missing:**
  - Remove unnecessary form fields; unbundle consents.
  - Create a simple "privacy request" inbox/form and an internal procedure.
  - Sign DPAs with vendors (most offer standard ones).
  - Write a one-page breach response plan with contacts.

### Q13. E-commerce, payments and consumer rights
- **Area:** Legal / Security · **Severity if missing:** Critical (if the site sells online)
- **Applies when:** the site sells goods, services, subscriptions or takes payments.
- **Ask:** "If you sell online, do you use a PCI-compliant payment provider and clearly show total prices, delivery, returns/withdrawal and refund terms before purchase?"
- **What "Yes" means:**
  - Card data handled only by a PCI DSS-compliant provider (hosted checkout or tokenized fields); the site never stores card numbers.
  - Total price including taxes and delivery shown before the order button; order button clearly labelled (e.g. "Order with obligation to pay" in the EU).
  - Pre-contract information: seller identity, product characteristics, delivery, payment, complaint handling.
  - **EU:** 14-day right of withdrawal explained, with a model withdrawal form, and (from 19 June 2026) an easy-to-find online withdrawal function ("withdraw from contract here" button) – Directive (EU) 2023/2673.
  - **Turkey:** distance sales contract (*mesafeli satış sözleşmesi*) and preliminary information form (*ön bilgilendirme formu*) presented and accepted before payment; ETBİS registration; 14-day withdrawal rules (updated rules in force since 1 Jan 2026).
  - Subscriptions: cancellation as easy as sign-up; renewal terms shown clearly.
  - Order confirmation sent by email with the contract terms.
- **If missing:**
  - Move to a hosted checkout/payment provider if card data touches your server.
  - Add the required pre-purchase information, policies and contract documents.
  - Have the checkout flow reviewed against consumer law in each target market.

### Q14. Email marketing and communications
- **Area:** Legal · **Severity if missing:** Medium (High if you send marketing email/SMS)
- **Applies when:** the site has a newsletter, marketing emails, SMS or push notifications.
- **Ask:** "If you send newsletters or marketing messages, do you have recorded opt-in consent and a one-click unsubscribe in every message?"
- **What "Yes" means:**
  - Opt-in (double opt-in recommended) for EU/UK/Turkey; consent date, source and wording stored.
  - **Turkey:** commercial electronic message consents registered in İYS (İleti Yönetim Sistemi).
  - **US (CAN-SPAM):** working unsubscribe honored within 10 business days, physical postal address in emails.
  - One-click unsubscribe headers (`List-Unsubscribe`, `List-Unsubscribe-Post`) – required by Gmail/Yahoo for bulk senders.
  - Transactional and marketing emails separated.
- **If missing:**
  - Switch to an email platform that records consent and handles unsubscribes.
  - Re-permission or remove contacts without provable consent.

---

## Section C – Accessibility

### Q15. Accessibility (WCAG 2.2 AA)
- **Area:** Accessibility / Legal · **Severity if missing:** High (Critical for EU e-commerce and services in scope of the European Accessibility Act)
- **Ask:** "Has your site been checked against WCAG 2.2 AA – for example, can it be fully used with only a keyboard, do images have alt text, and does text have enough color contrast?"
- **What "Yes" means:**
  - Meaningful images have alt text; decorative images have empty `alt=""`.
  - Text contrast at least 4.5:1 (3:1 for large text and UI components).
  - Everything works with keyboard only; visible focus indicator; focus not entirely hidden behind sticky headers or banners (2.4.11).
  - Form fields have labels and clear error messages.
  - `<html lang="...">` set; logical heading order.
  - Videos have captions.
  - Click/tap targets at least 24×24 CSS px (2.5.8); dragging actions have a single-pointer alternative (2.5.7).
  - Login doesn't require cognitive tests such as solving puzzles or retyping a code from memory; password managers and paste work (3.3.8).
  - Zoom not blocked (`user-scalable=no` not used).
  - An accessibility statement is published (the EAA requires accessibility information for in-scope services; micro-enterprises are exempt for services).
- **If missing:**
  - Run Lighthouse, axe DevTools and WAVE; fix all automated errors.
  - Do a manual keyboard-only and screen-reader pass (automated tools catch only part of the issues).
  - Publish an accessibility statement with a feedback contact.
  - Context: the European Accessibility Act applies from 28 June 2025 to in-scope services (e.g. e-commerce); in the US, ADA Title III lawsuits commonly use WCAG 2.1 AA as the benchmark.

---

## Section D – Performance, mobile and SEO

### Q16. Performance (Core Web Vitals)
- **Area:** Performance · **Severity if missing:** Medium
- **Ask:** "Does your site pass Core Web Vitals on mobile – LCP 2.5 s or less, INP 200 ms or less, CLS 0.1 or less – in PageSpeed Insights?"
- **What "Yes" means:**
  - All three Core Web Vitals in the "good" range for mobile.
  - Images in modern formats (WebP/AVIF), correctly sized, with `width`/`height` set; below-the-fold images lazy-loaded; the LCP image **not** lazy-loaded (consider `fetchpriority="high"`).
  - Text compression (Brotli/gzip), caching headers on static assets, a CDN for global audiences.
  - Unused JavaScript/CSS and heavy third-party scripts minimized.
- **If missing:**
  - Run PageSpeed Insights; address the top opportunities listed.
  - Convert and resize images; enable compression and caching at the host/CDN.
  - Audit third-party tags; remove unused ones.

### Q17. Mobile and cross-browser
- **Area:** Usability · **Severity if missing:** High
- **Ask:** "Have you tested the site on real phones and on the main browsers (Chrome, Safari, Firefox, Edge), and does it use a responsive layout?"
- **What "Yes" means:**
  - `<meta name="viewport" content="width=device-width, initial-scale=1">`.
  - No horizontal scrolling at 320 px width; readable text without zooming.
  - Same main content on mobile and desktop (Google uses mobile-first indexing).
  - Tested on iOS Safari and Android Chrome, plus desktop Chrome, Safari, Firefox and Edge.
  - Forms usable on mobile (right input types, autofill works).
- **If missing:**
  - Test at common breakpoints and on real devices; fix layout breaks.
  - Use browser developer tools' device mode for quick checks, real devices for final checks.

### Q18. SEO foundations
- **Area:** SEO · **Severity if missing:** Medium (High if the staging `noindex` is still on production)
- **Ask:** "Does every page have a unique title and meta description, and do you have a sitemap, a correct robots.txt and Google Search Console set up?"
- **What "Yes" means:**
  - Unique `<title>` and meta description per page; one clear `<h1>`.
  - `rel="canonical"` on pages (absolute, self-referencing where appropriate).
  - `robots.txt` does not block important pages or CSS/JS; it references the sitemap.
  - XML sitemap with only canonical, indexable URLs; submitted to Google Search Console (and Bing Webmaster Tools).
  - **No leftover `noindex`** from staging on production.
  - Open Graph tags (`og:title`, `og:type`, `og:image`, `og:url`) for link previews; favicon set.
  - Structured data (JSON-LD, e.g. `Organization`, `Product`) that matches visible content.
  - Old URLs 301-redirected after a migration or redesign.
- **If missing:**
  - Generate titles/descriptions from the CMS; add a sitemap plugin or generator.
  - Verify the site in Search Console; submit the sitemap; check the Pages/indexing report.

---

## Section E – Operations

### Q19. Monitoring, logging and incident response
- **Area:** Reliability / Security · **Severity if missing:** High
- **Ask:** "Will you be alerted automatically if the site goes down, throws errors or gets attacked – and do you know who does what if something goes wrong?"
- **What "Yes" means:**
  - Uptime monitoring with alerts (email/SMS/chat) on the homepage and key flows.
  - Error tracking/logging in production, without logging passwords, tokens or unnecessary personal data.
  - Security-relevant events logged (logins, admin changes, failed logins) and retained for a defined period (OWASP Top 10 2025 *A09 Security Logging and Alerting Failures*).
  - Certificate and domain expiry monitoring.
  - WAF/CDN with DDoS protection and rate limiting for public-facing sites.
  - A short incident response plan: contacts, steps, who informs users/regulators.
  - `/.well-known/security.txt` (RFC 9116) with a `Contact` and an `Expires` date, so researchers can report vulnerabilities.
- **If missing:**
  - Set up a free/low-cost uptime monitor and error tracking.
  - Put the site behind a CDN/WAF.
  - Publish `security.txt`; write the one-page incident plan.

### Q20. Launch readiness and content quality
- **Area:** Quality · **Severity if missing:** Medium
- **Ask:** "Have you done a final pre-launch pass – no broken links or placeholder text, a helpful 404 page, working forms and emails, analytics firing correctly, and staging separated from production?"
- **What "Yes" means:**
  - No broken links (internal and external); no "lorem ipsum", test products or test accounts.
  - Custom 404 page returning HTTP 404 (not a 200 "soft 404").
  - Every form submits and the notification/confirmation email arrives (and isn't in spam).
  - Analytics and conversion tracking verified – and respecting consent (Q10).
  - Favicon, page titles, social share images look right.
  - Staging is not publicly indexable; production does not point to staging APIs/databases.
  - A rollback plan exists for launch day.
- **If missing:**
  - Run a link checker crawl; walk through every form and checkout path end-to-end.
  - Remove test data; verify analytics in real-time view.
  - Write a short launch-day checklist with a rollback step.
