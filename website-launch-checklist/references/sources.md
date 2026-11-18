# Sources

Authoritative references behind the checklist. When a user asks "why?" or "says who?", cite the relevant source. Rules change: prefer the live source over the summary in `questions.md`, and say so if they disagree.

_Last reviewed: November 2026._

## Security

| Topic | Source |
|---|---|
| OWASP Top 10 (2025) | https://top10.owasp.org/2025 |
| OWASP ASVS 5.0 | https://asvs.owasp.org/ |
| OWASP Cheat Sheet Series (Authentication, Password Storage, CSRF, XSS, File Upload) | https://cheatsheetseries.owasp.org/ |
| NIST SP 800-63B-4 – passwords and authenticators | https://pages.nist.gov/800-63-4/sp800-63b.html |
| HSTS header | https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Strict-Transport-Security |
| HSTS preload list requirements | https://hstspreload.org/ |
| Content Security Policy | https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP |
| Cookies (Secure, HttpOnly, SameSite, prefixes) | https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies |
| Subresource Integrity | https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity |
| Mozilla server-side TLS recommendations | https://wiki.mozilla.org/Security/Server_Side_TLS |
| Let's Encrypt – shorter certificate lifetimes | https://letsencrypt.org/2025/12/02/from-90-to-45/ |
| security.txt (RFC 9116) | https://www.rfc-editor.org/rfc/rfc9116 |
| CAA records (RFC 8659) | https://www.rfc-editor.org/rfc/rfc8659 |
| Gmail email sender guidelines (SPF/DKIM/DMARC, one-click unsubscribe) | https://support.google.com/a/answer/81126 |

### Security Deep Dive

| Topic | Source |
|---|---|
| OWASP Authorization Cheat Sheet | https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html |
| OWASP API Security Top 10 (2023) | https://owasp.org/API-Security/editions/2023/en/0x11-t10/ |
| OWASP Forgot Password Cheat Sheet | https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html |
| OWASP Session Management Cheat Sheet | https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html |
| OWASP File Upload Cheat Sheet | https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html |
| OWASP SSRF Prevention Cheat Sheet | https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html |
| CIS Benchmarks (OS/server hardening) | https://www.cisecurity.org/cis-benchmarks |
| AWS S3 Block Public Access | https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html |
| GitHub Actions security hardening | https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions |
| PCI DSS document library (SAQs) | https://www.pcisecuritystandards.org/document_library/ |
| Django deployment checklist | https://docs.djangoproject.com/en/stable/howto/deployment/checklist/ |
| WordPress hardening guide | https://developer.wordpress.org/advanced-administration/security/hardening/ |
| Next.js middleware advisory (CVE-2025-29927) | https://github.com/vercel/next.js/security/advisories/GHSA-f82v-jwr5-mffw |

**Free self-check tools** (use only on sites you own or are authorised to test):
SSL Labs Server Test (https://www.ssllabs.com/ssltest/), Mozilla HTTP Observatory (https://developer.mozilla.org/en-US/observatory), securityheaders.com, OWASP ZAP (https://www.zaproxy.org/).

## Legal and privacy

| Topic | Source |
|---|---|
| GDPR full text (Art. 8, 13, 28, 33) | https://eur-lex.europa.eu/eli/reg/2016/679/oj |
| ePrivacy Directive (cookies, Art. 5(3)) | https://eur-lex.europa.eu/eli/dir/2002/58/oj |
| EDPB guidelines (consent, cookie banners) | https://www.edpb.europa.eu/our-work-tools/general-guidance/guidelines-recommendations-best-practices_en |
| UK ICO – cookies and similar technologies | https://ico.org.uk/for-organisations/direct-marketing-and-privacy-and-electronic-communications/guide-to-pecr/cookies-and-similar-technologies/ |
| CNIL – cookie recommendations (reject as easy as accept) | https://www.cnil.fr/en/cookies-and-other-tracking-devices-cnil-publishes-new-guidelines |
| KVKK (Turkey) – law, guides and board decisions | https://www.kvkk.gov.tr/ |
| İYS – Turkey commercial electronic message system | https://iys.org.tr/ |
| ETBİS – Turkey e-commerce information system | https://www.eticaret.gov.tr/ |
| CCPA/CPRA regulations (California) | https://cppa.ca.gov/regulations/ |
| Global Privacy Control | https://globalprivacycontrol.org/ |
| FTC – CAN-SPAM compliance guide | https://www.ftc.gov/business-guidance/resources/can-spam-act-compliance-guide-business |
| FTC – COPPA rule | https://www.ftc.gov/legal-library/browse/rules/childrens-online-privacy-protection-rule-coppa |
| Germany – Impressum (§ 5 DDG) | https://www.gesetze-im-internet.de/ddg/__5.html |
| EU Consumer Rights Directive (14-day withdrawal) | https://eur-lex.europa.eu/eli/dir/2011/83/oj |
| Directive (EU) 2023/2673 – online withdrawal function (from 19 June 2026) | https://eur-lex.europa.eu/eli/dir/2023/2673/oj |
| EU AI Act – transparency obligations (Art. 50) | https://eur-lex.europa.eu/eli/reg/2024/1689/oj |
| PCI DSS | https://www.pcisecuritystandards.org/ |
| Google Consent Mode | https://developers.google.com/tag-platform/security/guides/consent |

## Accessibility

| Topic | Source |
|---|---|
| WCAG 2.2 | https://www.w3.org/TR/WCAG22/ |
| What's new in WCAG 2.2 | https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/ |
| WCAG quick reference | https://www.w3.org/WAI/WCAG22/quickref/ |
| European Accessibility Act (Directive 2019/882) | https://eur-lex.europa.eu/eli/dir/2019/882/oj |
| Evaluation tools list | https://www.w3.org/WAI/test-evaluate/tools/list/ |

## Performance, mobile and SEO

| Topic | Source |
|---|---|
| Core Web Vitals | https://web.dev/articles/vitals |
| PageSpeed Insights | https://pagespeed.web.dev/ |
| Image formats | https://web.dev/articles/choose-the-right-image-format |
| Mobile-first indexing | https://developers.google.com/search/docs/crawling-indexing/mobile/mobile-sites-mobile-first-indexing |
| Viewport meta tag | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/meta/name/viewport |
| Canonical URLs | https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls |
| Sitemaps | https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap |
| robots meta / noindex | https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag |
| Redirects and HTTP status codes | https://developers.google.com/search/docs/crawling-indexing/http-network-errors |
| Structured data policies | https://developers.google.com/search/docs/appearance/structured-data/sd-policies |
| Open Graph protocol | https://ogp.me/ |
| Search Console | https://search.google.com/search-console |

## Confidence notes

Some details move quickly. Before relying on them, check these against the primary source:
- Exemption scope for micro-enterprises under the European Accessibility Act.
- Turkish distance-sales rules (amended, in force 1 Jan 2026) and KVKK board decisions on separating the information notice from the consent text.
- The EU–US Data Privacy Framework's legal status (keep SCCs as a fallback).
- Certificate lifetime schedules from public CAs.
