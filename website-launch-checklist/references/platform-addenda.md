# Platform Addenda

These aren't extra questions. Use the section that matches the platform from the profile in two ways:
1. **During questions:** when an answer is "Partly" or "Not sure", use the platform-specific checks below as your single follow-up.
2. **In the report:** turn generic actions into platform-specific ones (e.g. "add rate limiting" → "enable login rate limiting in your security plugin or WAF").

Recommend **categories** of plugins/apps or the platform's built-in settings rather than one specific vendor, unless the user asks for names. If the platform isn't listed, use the closest section ("Custom / self-hosted frameworks" for most server-side stacks).

---

## WordPress (including WooCommerce)

**What WordPress leaves to you:** core, plugin and theme updates; plugin quality; hardening; backups; hosting.

Extra checks:
- **Plugins and themes:** only from wordpress.org or reputable vendors; **never "nulled"/pirated** copies (a common malware source). Remove deactivated plugins and unused themes (keep one default theme as a fallback). Check each plugin's last update date and known vulnerabilities in a WordPress vulnerability database (e.g. WPScan, Patchstack, Wordfence Intelligence).
- **Auto-updates:** keep automatic minor core updates on (default since 3.7) and enable plugin/theme auto-updates where safe (UI since WordPress 5.5). Test major updates on staging.
- **`wp-config.php`:** `define('DISALLOW_FILE_EDIT', true);` to disable the built-in theme/plugin editor; unique authentication keys and salts; `WP_DEBUG` false in production; file permissions restrictive (e.g. 600/640 where the host allows).
- **Uploads:** block PHP execution in `/wp-content/uploads/` (server rule or security plugin).
- **Login:** 2FA for all administrator, editor and shop-manager roles; login rate limiting; consider disabling **XML-RPC** if no app uses it (a frequent brute-force target).
- **User enumeration:** prevent username discovery via `/?author=N` and the REST endpoint `/wp-json/wp/v2/users` (security plugin or server rule). Don't use `admin` as a username.
- **Roles:** give content people the Editor/Author role, not Administrator. Review Application Passwords under each user profile.
- **Backups:** a backup plugin sending to off-site storage, *in addition to* host backups; test a restore to staging.
- **Security plugin / WAF:** a reputable security plugin with firewall and malware scanning, or a CDN-level WAF.
- **WooCommerce:** keep customer and order data access limited to Shop Manager/Admin; review plugins that export orders or customers; use the payment gateway's hosted fields or redirect; turn off unused payment gateways; set a webhook secret for WooCommerce webhooks.
- **Privacy/legal:** WordPress has a built-in Privacy Policy page and personal-data export/erase tools (Tools → Export/Erase Personal Data). Use them for Q12 requests.

---

## Shopify

**What Shopify handles:** hosting, TLS, server patching, PCI DSS compliance for Shopify Checkout, DDoS protection.
**What stays with you:** accounts and staff permissions, apps, theme code, third-party scripts, legal pages, consent, email domain.

Extra checks:
- **Staff accounts:** each person gets their own staff account with only the permissions they need; **require two-step authentication** for all staff; remove ex-staff and old collaborator accounts. Require a collaborator request code so agencies must ask for access.
- **Apps:** review installed apps and their requested permissions (access scopes); **uninstall** unused apps (this revokes their API access; turning off an app embed or removing its block in the theme does not), then remove any code they left in the theme; prefer apps with recent updates and good reviews; remember that apps can inject scripts into your storefront.
- **Theme code:** review third-party snippets in theme files (e.g. `theme.liquid`); remove leftovers from uninstalled apps; customize checkout only through Shopify's supported checkout extensibility, not legacy checkout code.
- **Consent:** configure Shopify's customer privacy/cookie banner settings or a compatible consent app, and make sure tracking pixels respect it (Q10).
- **Legal pages:** Shopify's policy generators are a starting point only; for Turkey add the distance-sales documents (Q13); for the EU add the withdrawal information.
- **Email:** authenticate your sender domain (SPF/DKIM) for Shopify Email/notifications; publish DMARC (Q8).
- **Domains:** if the domain is registered elsewhere, lock it and enable MFA at that registrar.

---

## Hosted site builders (Wix, Squarespace, Webflow and similar)

**What the platform handles:** hosting, TLS, server security, patching, most performance infrastructure.
**What stays with you:** account security, contributor roles, custom code and embeds, forms and the data they collect, consent, legal pages, SEO settings.

Extra checks:
- **Account security:** 2FA on the owner account and every contributor; contributors get the lowest role that works; remove old contributors and agencies.
- **Custom code / embeds:** inventory every "custom code", header/footer injection and embed block; remove unused ones; never paste API secrets into custom code.
- **Forms:** know where form submissions are stored and who receives them; delete old submissions according to your retention policy (Q12); enable the platform's spam protection.
- **Security headers:** often can't be customized on these platforms. Note it as a platform limitation, not a user failure, and don't count Q2 as a gap for headers the platform doesn't let you set. Cookie flags are also platform-controlled.
- **Consent:** use the built-in cookie banner settings and make sure third-party embeds (YouTube, maps, chat, pixels) are blocked until consent where required.
- **Backups:** most builders keep site history but not always form data or member data; export these periodically.
- **Deep dive:** S4, S5 and S6 are N/A; S8 only if custom code handles uploads.

---

## JavaScript frameworks (Next.js, Nuxt, SvelteKit, Remix, React/Vue SPAs with an API)

Extra checks:
- **Environment variables:** variables with public prefixes (`NEXT_PUBLIC_`, `VITE_`, `NUXT_PUBLIC_`, `PUBLIC_`, `REACT_APP_`, `EXPO_PUBLIC_`) are **exposed to the browser** (bundled into client code or sent in the page payload). Never put secrets there. Search the built output for keys before launch.
- **Server-side authorization:** check auth and permissions inside every API route, server action and data loader, not only in middleware or in the UI. Keep the framework patched; for example, CVE-2025-29927 allowed bypassing Next.js middleware-only checks on unpatched versions.
- **Rendering untrusted content:** avoid `dangerouslySetInnerHTML` / `v-html` / `{@html}` with user content, or sanitize it with a maintained sanitizer (e.g. DOMPurify).
- **Tokens:** prefer `HttpOnly`, `Secure`, `SameSite` session cookies over storing tokens in `localStorage`, where any XSS can steal them.
- **CSP:** use nonce-based CSP support where the framework offers it.
- **Source maps:** don't publish production source maps publicly unless you intend to (they reveal source and sometimes comments or internal URLs); upload them privately to your error tracker instead.
- **Preview deployments:** preview/branch deployments on Vercel, Netlify, Cloudflare Pages, etc. are often public. Enable deployment protection and make sure previews don't use production secrets or data, and are `noindex`.
- **Dependencies:** commit the lockfile, run `npm audit`/`pnpm audit` or equivalent in CI, review new packages before adding them, and be cautious with packages that run install scripts.
- **API:** treat the backend as a public API even if "only our frontend calls it" (Deep dive S2).

---

## Custom / self-hosted frameworks (Laravel, Symfony, Django, Rails, Express, ASP.NET, etc.)

Extra checks:
- **Production settings:** debug off (`APP_DEBUG=false` in Laravel, `DEBUG = False` in Django, production environment in Rails/Express/ASP.NET); allowed hosts configured (e.g. Django `ALLOWED_HOSTS`); HTTPS enforced at the framework level (e.g. Rails `config.force_ssl`, Django `SECURE_SSL_REDIRECT`).
- **Built-in deployment checks:** run the framework's own checks where available, e.g. Django `python manage.py check --deploy`; use framework-specific static analysers (e.g. Brakeman for Rails).
- **App secrets:** framework secret keys (Laravel `APP_KEY`, Django `SECRET_KEY`, Rails credentials) are unique per environment, never committed, and rotated if leaked.
- **Framework protections on:** CSRF middleware, ORM/parameterized queries, template auto-escaping, secure session cookie settings.
- **Dependencies:** `composer audit`, `pip-audit`, `bundle audit`, `npm audit`, `dotnet list package --vulnerable` (or equivalents) in CI.
- **Server:** the whole of Deep dive S5 applies if you run the server; S4 if you run the database.
- **Process:** run the app as a non-root user behind a reverse proxy (Nginx, Caddy, a managed load balancer) that terminates TLS and sets security headers.
