# Security Deep Dive (optional module)

Twelve extra security questions (S1–S12) for sites that carry more risk. They use the same format as `questions.md`: Area, Severity if missing, Ask, What "Yes" means, If missing, Applies when.

## When to offer it

After the 20 core questions, **offer** the deep dive (never force it) when any of these is true:
- the site has user accounts, a customer area or multiple admin users;
- it takes payments, or stores orders or other sensitive personal data (health, financial, children's, ID documents);
- it has a custom backend, a public or mobile API, or runs on a server the user manages (VPS, cloud VM, containers);
- any core security question (Q1–Q8) came back **Gap** or **Unknown** with Critical priority;
- the user asks for a "security deep dive", "full security check" or "security audit".

Typical phrasing: "Your site handles logins and payments, so I recommend 12 more security questions (about 5 minutes). Want to do them now, or get the report first?"

For a simple brochure site or blog on a hosted builder, don't offer it unless the user asks.

## Automatic N/A by platform

Mark these N/A without asking and say so in one line:
- **Fully hosted builders (Wix, Squarespace, Webflow, hosted Shopify):** S4, S5 and S6 are run by the platform (people-and-access points are covered by S12). S8 is N/A unless custom code handles uploads.
- **No user accounts:** S1 and S3.
- **No API and no JavaScript app talking to a backend:** S2.
- **No payments:** S10.
- **No CI/CD pipeline or code repository:** S7 is limited to third-party scripts and plugins.

---

### S1. Authorization and access control in depth
- **Area:** Security · **Severity if missing:** Critical
- **Applies when:** the site has user accounts or more than one admin role.
- **Ask:** "Have you tested that a logged-in user cannot reach other users' data or admin functions, for example by changing an ID in the URL or calling an admin URL directly?"
- **What "Yes" means:**
  - Every request that reads or changes data checks on the server that *this* user may access *this* record (object-level authorization).
  - Admin and staff functions check the role on the server; hiding a button is not protection.
  - Deny by default: new routes and endpoints require authorization unless explicitly public.
  - Hard-to-guess identifiers (UUIDs) help but are **not** a substitute for checks.
  - Authorization is tested: two-account tests on staging, and ideally automated tests for key endpoints.
  - Multi-tenant SaaS: every query is scoped to the tenant.
- **If missing:**
  - Centralize authorization in middleware or policies (framework guards, policy classes) instead of ad-hoc checks.
  - Add automated tests: user A must get 403/404 on user B's resources.
  - Review the OWASP Authorization Cheat Sheet.

### S2. API security
- **Area:** Security · **Severity if missing:** High (Critical if the API returns personal or payment data)
- **Applies when:** the site has a REST/GraphQL API, a mobile app backend, or a JavaScript frontend calling its own backend.
- **Ask:** "Does every API endpoint require proper authentication, return only the fields the client needs, have rate limits, and avoid a wildcard CORS policy?"
- **What "Yes" means:**
  - Authentication on every non-public endpoint, and authorization per object (see S1).
  - Responses return only needed fields. No password hashes, internal flags or other users' data, even if the UI doesn't display them.
  - Clients can't set fields they shouldn't (mass assignment), such as `role`, `price` or `isAdmin`.
  - Rate limits and pagination limits on all endpoints. GraphQL depth/complexity limits and introspection disabled in production when not needed.
  - CORS lists specific trusted origins. Never `Access-Control-Allow-Origin: *` together with credentials, and never reflecting any origin.
  - An inventory of all API versions and hosts. Old versions and test endpoints are retired.
  - API keys are scoped, can be rotated and are never embedded in mobile apps or frontend code as if they were secrets.
- **If missing:**
  - Walk through the OWASP API Security Top 10 (2023) for each endpoint group.
  - Add response schemas/serializers that whitelist fields.
  - Add a gateway, middleware or WAF rate limiting.

### S3. Account and session flows
- **Area:** Security · **Severity if missing:** High
- **Applies when:** the site has user accounts.
- **Ask:** "Are your password reset, sign-up, email change and logout flows secure – for example, single-use expiring reset links, no 'this email isn't registered' messages, and sessions that end on logout?"
- **What "Yes" means:**
  - Password reset tokens are random, single-use, short-lived (e.g. under 1 hour) and invalidated after use or after a password change.
  - Reset, login and sign-up responses don't reveal whether an email exists ("If an account exists, we've sent an email").
  - Logout invalidates the session on the server. Changing the password or email invalidates other sessions and notifies the user.
  - Email change requires re-authentication and confirmation from the new address, with a notice to the old one.
  - Idle and absolute session timeouts are defined. "Remember me" uses a separate, revocable token.
  - Session tokens live in `HttpOnly` cookies rather than `localStorage` where possible.
  - Sensitive actions (change payout details, delete account, add an admin) require re-authentication.
- **If missing:**
  - Follow the OWASP Forgot Password and Session Management cheat sheets.
  - Prefer a mature auth library or provider over custom code.

### S4. Data protection and database security
- **Area:** Security · **Severity if missing:** Critical
- **Applies when:** the site stores personal or business data in a database you control.
- **Ask:** "Is your database unreachable from the public internet, accessed with a least-privilege account, and is sensitive data encrypted?"
- **What "Yes" means:**
  - The database listens only on localhost or a private network. Ports like 3306, 5432 and 27017 are not open to the internet, and admin tools such as phpMyAdmin or Adminer are not publicly exposed.
  - The application uses a database user with only the permissions it needs (not root/superuser). Migrations use a separate account.
  - Encryption at rest is enabled (disk or managed-database encryption). Particularly sensitive fields (ID numbers, health data, tokens) are additionally encrypted or tokenized.
  - Connections to the database are encrypted when they leave the host.
  - Database backups are encrypted and access-controlled (see Q7).
  - Production data isn't copied to dev/staging without anonymization.
  - Data you don't need isn't stored (ties to Q12 data minimization).
- **If missing:**
  - Close database ports in the firewall or security group. Use a private network or SSH tunnel for admin access.
  - Create a least-privilege application user. Rotate the old credentials.
  - Turn on encryption at rest in the hosting/cloud console.

### S5. Server and hosting hardening
- **Area:** Security · **Severity if missing:** High
- **Applies when:** you manage the server (VPS, cloud VM, dedicated server, self-managed containers). N/A on managed/hosted platforms.
- **Ask:** "If you run your own server, is it hardened – SSH keys only, root login disabled, a firewall allowing only needed ports, and automatic security updates?"
- **What "Yes" means:**
  - SSH password login disabled. Key-based login only, root login disabled, MFA or a bastion/VPN for admin access where possible.
  - A firewall allows only the needed ports (typically 80/443 publicly, SSH restricted).
  - Automatic security updates for the OS (e.g. `unattended-upgrades`). Reboots for kernel updates are scheduled.
  - The application runs as a non-root user. Containers don't run as root and use minimal, regularly rebuilt base images.
  - Brute-force protection on SSH (e.g. fail2ban) or SSH not exposed at all.
  - Unused services and default accounts are removed. Cloud metadata endpoints are protected (e.g. IMDSv2 on AWS).
  - A hardening baseline is used, such as the CIS Benchmarks for the OS.
- **If missing:**
  - Apply a CIS Benchmark or provider hardening guide.
  - If server administration isn't your core skill, consider a managed platform. It removes most of this list.

### S6. Cloud storage and file hosting
- **Area:** Security · **Severity if missing:** Critical (if buckets contain personal or private data) · High otherwise
- **Applies when:** the site uses object storage (AWS S3, Google Cloud Storage, Azure Blob, Cloudflare R2, DigitalOcean Spaces, etc.).
- **Ask:** "Are your cloud storage buckets private by default, with private files served only through short-lived signed URLs?"
- **What "Yes" means:**
  - Account-level public-access blocking enabled (e.g. S3 Block Public Access). Only intentionally public assets live in a separate public bucket.
  - Private user files (invoices, uploads, ID scans) are served through short-lived signed URLs after an authorization check.
  - Bucket listing isn't publicly possible.
  - Access keys are scoped to specific buckets and actions, and rotated. Workload identity or roles are preferred over long-lived keys.
  - Access logging or versioning is enabled for important buckets.
- **If missing:**
  - Enable public-access blocking, move public assets to a dedicated bucket, and switch private files to signed URLs.
  - Review bucket policies and ACLs in the cloud console's security or access analyzer.

### S7. Supply chain, third-party scripts and CI/CD
- **Area:** Security · **Severity if missing:** High
- **Applies when:** always (the scope shrinks to third-party scripts and plugins if there is no code repository).
- **Ask:** "Do you keep an inventory of third-party scripts and packages, use lockfiles, and keep secrets and deployment permissions in your CI/CD pipeline locked down?"
- **What "Yes" means:**
  - An inventory of third-party scripts on the site (analytics, chat, ads, A/B testing) with an owner for each. Unused ones are removed. Payment pages carry the minimum (where it applies to you, PCI DSS v4.0.1 requirement 6.4.3 expects payment-page scripts to be inventoried, authorized and integrity-checked; for SAQ A merchants, PCI SSC's January 2025 update replaced 6.4.3/11.6.1 with an eligibility confirmation about script attacks; ask your payment provider).
  - Lockfiles are committed (`package-lock.json`, `composer.lock`, `poetry.lock`…) and builds install from the lockfile.
  - New dependencies are reviewed (maintenance, popularity, typosquatting) before adding.
  - CI/CD secrets are stored in the platform's secret store and scoped to the jobs that need them. Deployments to production require protected branches or reviews.
  - CI workflow tokens have minimal permissions (e.g. GitHub Actions `permissions:` set to read-only by default). Third-party actions are pinned to a commit SHA.
  - Branch protection and required reviews on the main branch. MFA on all code-hosting accounts.
- **If missing:**
  - List all third-party scripts and remove what you don't need.
  - Enable branch protection, set default workflow permissions to read-only, and pin third-party actions.
  - See GitHub's "Security hardening for GitHub Actions" guide, or your CI vendor's equivalent.

### S8. File uploads and server-side requests (SSRF)
- **Area:** Security · **Severity if missing:** High
- **Applies when:** users can upload files, or the server fetches URLs that users provide (link previews, image import, webhooks to user URLs, PDF generation from URLs).
- **Ask:** "If users can upload files or make your server fetch a URL, do you validate file types and sizes, store uploads safely, and block requests to internal addresses?"
- **What "Yes" means:**
  - **Uploads:** allow-list of extensions *and* content type checked on the server; size limits; files renamed with random names; stored outside the web root or on object storage; never executed (no PHP or scripts in upload folders); served with a correct `Content-Type` and `Content-Disposition`; images re-encoded where practical; malware scanning for files shared with other users.
  - **SSRF:** user-supplied URLs are validated against an allow-list where possible. Requests to private, loopback and link-local ranges (`127.0.0.0/8`, `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `169.254.0.0/16`, `0.0.0.0/8`, `100.64.0.0/10`, IPv6 equivalents including IPv4-mapped addresses) and cloud metadata addresses are blocked after DNS resolution. Redirects are limited.
- **If missing:**
  - Follow the OWASP File Upload and SSRF Prevention cheat sheets.
  - Prefer direct-to-object-storage uploads with signed upload URLs.

### S9. Error handling and failing safely
- **Area:** Security · **Severity if missing:** Medium
- **Applies when:** custom code exists (your own app, custom plugins or theme logic). N/A for hosted builders and unmodified CMS/plugin setups.
- **Ask:** "When something goes wrong – a timeout, a failed payment callback, a crashed service – does your app fail safely without granting access or exposing details?"
- **What "Yes" means:**
  - Errors return generic messages to users. Details go only to server logs (OWASP Top 10 2025 A10 Mishandling of Exceptional Conditions).
  - Authorization and validation "fail closed": if a check errors, the request is denied, not allowed.
  - External-service failures (payment provider, email, auth provider) don't leave orders, accounts or permissions in an inconsistent or elevated state. Transactions are rolled back.
  - Global exception handlers exist. No unhandled promise rejections or crashes that restart into an insecure default.
  - Resource limits (timeouts, request size, query limits) prevent one bad request from taking the service down.
- **If missing:**
  - Review critical flows (login, checkout, payment callback, permission changes) for "what happens if this step throws?"
  - Add tests for failure paths, not only happy paths.

### S10. Payments, webhooks and business logic
- **Area:** Security · **Severity if missing:** Critical
- **Applies when:** the site takes payments, issues credits or coupons, or receives webhooks.
- **Ask:** "Do you verify webhook signatures, calculate prices and totals on the server only, and know which PCI DSS self-assessment applies to you?"
- **What "Yes" means:**
  - Prices, discounts, shipping and totals are calculated on the server. Values sent from the browser are never trusted.
  - Orders are marked paid only after a **verified** payment event: the webhook signature is checked with the provider's secret, events are de-duplicated (idempotency) and old or replayed events are rejected.
  - Coupons, gift cards, referral credits and stock can't be abused by race conditions or repeated submission (atomic updates, idempotency keys).
  - Card data never touches your servers (hosted checkout or provider fields). The applicable PCI DSS self-assessment questionnaire (often SAQ A for fully hosted checkout) is identified and completed.
  - Refunds and payout-detail changes require elevated permissions and are logged.
  - Fraud tools offered by the provider (3-D Secure/SCA in the EU, risk scoring) are enabled.
- **If missing:**
  - Follow your payment provider's webhook-verification guide.
  - Move all price logic server-side. Add idempotency keys.
  - Ask your payment provider which SAQ applies, and complete it.

### S11. Security testing before and after launch
- **Area:** Security · **Severity if missing:** Medium (High for sites with accounts or payments)
- **Applies when:** the site has accounts or payments, or custom code exists.
- **Ask:** "Is your code checked by automated security tools, and has the site had an independent security test (penetration test) appropriate to its risk?"
- **What "Yes" means:**
  - Static analysis (SAST) and dependency scanning run in CI on every change (e.g. CodeQL, Semgrep, framework scanners).
  - A dynamic scan (DAST) such as an OWASP ZAP baseline runs against **staging**, not production, regularly or before releases.
  - Sites with accounts, payments or sensitive data have had a penetration test by a qualified tester before launch or major changes, and findings are fixed and re-tested.
  - Security requirements are drawn from a standard such as OWASP ASVS 5.0 (Level 1 at minimum, Level 2 for sites with sensitive data).
  - A vulnerability disclosure channel exists (security.txt, Q19). Reports are triaged within a defined time.
- **If missing:**
  - Add a free SAST tool and dependency scanning to CI.
  - Schedule a ZAP baseline scan against staging. Only test systems you own or are authorised to test.
  - Budget for a pentest proportional to the risk.

### S12. Team and account hygiene
- **Area:** Security · **Severity if missing:** High
- **Applies when:** always.
- **Ask:** "Does everyone with access use their own account with MFA and a password manager, and do you remove access immediately when someone leaves or a contractor finishes?"
- **What "Yes" means:**
  - An access list of who can reach the site admin, hosting, DNS/registrar, code repository, email, analytics, payment provider and backups.
  - Individual accounts only. No shared logins. Credentials are shared through a password manager, never chat or email.
  - MFA on all of the above. Phishing-resistant MFA (passkeys or security keys) for the most powerful accounts.
  - Offboarding checklist: remove access on the last day, rotate any shared secrets and API keys the person knew.
  - Contractors and agencies get time-limited, least-privilege access.
  - Basic phishing awareness, plus a rule that payment-detail or DNS changes requested by email are verified by another channel.
  - Recovery codes for critical accounts are stored securely, and at least two trusted people can recover the registrar and hosting accounts.
- **If missing:**
  - Build the access list this week and remove anything stale.
  - Adopt a team password manager. Turn on MFA everywhere.
  - Write a 10-line offboarding checklist.

---

## Scoring the deep dive

- Score the deep dive **separately**: Deep-dive score = S-items passed ÷ (12 − N/A) × 100. Don't mix it into the core readiness score, so results stay comparable between users who did and didn't take the module.
- S-items assess only what goes beyond the core questions. A sub-check already scored under a core question (e.g. IDOR in Q4, uploads in Q5, unused plugins in Q3) is not re-scored in the deep dive. In the report, put the action in one place and cross-reference it ("see Q4").
- Prioritize each S-item exactly like a core item (severity, adjusted by profile) and put it in the **same** Critical/High/Medium/Low sections of the report, labelled with its S-number, so the user gets one prioritized to-do list.
