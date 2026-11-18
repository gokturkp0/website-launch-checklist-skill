---
name: website-launch-checklist
description: Interactive 20-question website launch and audit checklist, plus an optional 12-question security deep dive and platform-specific guidance (WordPress, Shopify, site builders, JS and server frameworks), covering security (HTTPS, headers, updates, auth, access control, APIs, backups, DNS/email), legal and privacy (privacy policy, cookie consent, GDPR, KVKK, CCPA, terms, e-commerce, email marketing), accessibility (WCAG 2.2 AA), performance, mobile, SEO and operations. Asks the user one question at a time, then produces a prioritized gap report with concrete fixes. Use when someone is launching, redesigning or auditing a website, or asks things like "is my website ready to go live", "website checklist", "pre-launch checklist", "what is my site missing", "is my site secure", "is my site GDPR compliant", "cookie banner requirements", "website security audit", or "security deep dive" - even if they don't say "checklist".
---

# Website Launch Checklist

Guide the user through 20 core questions about their website (plus an optional 12-question Security Deep Dive for higher-risk sites), then write a prioritized report of what is missing and what to do about it. This is an informational checklist, not a legal or security audit. Say so at the start and again at the end of the report (see "Disclaimer" below).

## Files in this skill

- `references/questions.md`: the 20 core questions. Each has its severity, the sub-checks a full "Yes" requires, and the remediation steps. Read it before asking anything.
- `references/security-deep-dive.md`: the optional Security Deep Dive (S1–S12), with rules for when to offer it, automatic N/A by platform, and separate scoring. Read it when the profile shows higher risk or the user asks for a deeper security check.
- `references/platform-addenda.md`: platform-specific checks and fixes (WordPress/WooCommerce, Shopify, hosted builders, JavaScript frameworks, custom server frameworks). Read the matching section once you know the platform.
- `references/report-template.md`: the exact structure of the final report. Read it before writing the report.
- `references/sources.md`: authoritative sources for each area. Cite from it when the user asks "why" or "where does this come from".
- `DISCLAIMER.md`: the full disclaimer. Share it if the user asks about liability or the limits of the checklist.

## Workflow

### Step 1: Introduce and set expectations
In two or three sentences, tell the user:
- you will ask 20 short questions (about 10 minutes), grouped into Security, Legal & Privacy, Accessibility, Performance/SEO and Operations;
- the answers can be **Yes / Partly / No / Not sure / N/A**, and "Not sure" is fine and common;
- the result is a checklist-based report, **not legal advice and not a security audit** (point to the disclaimer).

Mention that they can answer several questions in one message, or say "quick mode" to see all 20 at once. If the user asks for a full or in-depth security check up front, tell them the Security Deep Dive will follow the core questions.

### Step 2: Short profile (context, not scored)
Before the 20 questions, ask these in **one** message. The answers decide which questions apply and how severe gaps are:
1. What kind of site is it? (brochure/portfolio, blog/content, SaaS/web app with logins, online shop, other)
2. Where are your visitors or customers? (e.g. EU/UK, Turkey, US/California, worldwide)
3. What platform is it built on? (e.g. WordPress, Shopify, Wix, Next.js, custom)
4. Do you collect personal data (forms, accounts, newsletters, analytics), take payments, or send marketing emails?

Use the profile to:
- mark questions **N/A** automatically when they clearly don't apply (e.g. Q13 for a site that sells nothing) and say you skipped them;
- show only the jurisdiction-specific sub-checks that matter (KVKK/İYS/ETBİS for Turkey, GDPR/ePrivacy/EAA for EU, CCPA/GPC/CAN-SPAM for the US);
- skip sub-checks the platform handles for them (e.g. hosted Shopify checkout covers PCI card handling, but not the legal pages);
- open the matching section of `references/platform-addenda.md` and use it for follow-ups and for tailoring actions;
- note whether the Security Deep Dive trigger conditions (in `references/security-deep-dive.md`) are met.

### Step 3: Ask the 20 questions
- Ask **one section at a time** (Security Q1–Q8, then Legal & Privacy Q9–Q14, then Accessibility Q15, Performance & SEO Q16–Q18, Operations Q19–Q20). Ask questions one by one within a section unless the user prefers batches.
- Use the **Ask** wording from `references/questions.md`, written in plain language. If the user seems non-technical, add a one-line explanation of *why it matters*.
- If the user answers **"Partly"** or **"Not sure"**, you may ask **one** short follow-up based on the sub-checks to find the actual gap. Don't interrogate; move on after one follow-up.
- If an answer contradicts the profile or an earlier answer (e.g. "no personal data" but "we have a newsletter"), point this out gently and record the more cautious interpretation.
- Keep a running tally internally. Don't print the report until all applicable questions are answered, unless the user asks to stop early. In that case, report on what was answered and list the unanswered questions as "Not assessed".

### Step 3b (optional): Security Deep Dive
After Q20, if the trigger conditions in `references/security-deep-dive.md` are met (or the user asked for it), **offer** the 12 deep-dive questions in one sentence, explaining why their site warrants it and that it takes about 5 more minutes. If they accept:
- mark platform-based N/A items automatically and say which were skipped;
- ask the remaining S-questions in the same way as the core questions (same answer options, at most one follow-up each);
- if they decline, continue to the report and add one line in it recommending the deep dive.

Don't offer it to low-risk sites (e.g. a brochure site on a hosted builder) unless the user asks.

### Step 4 (optional): Passive verification
If the user gives a URL **and explicitly confirms they own it or are authorised to assess it** (ask; if they don't confirm, skip this step), and you have a web-fetch or shell tool, you may cross-check a few answers with **at most 5 passive, read-only GET/HEAD requests**:
- the response headers of the homepage (HTTPS redirect, HSTS, CSP and other security headers, `Server`/`X-Powered-By`);
- `/robots.txt`, `/sitemap.xml`, `/.well-known/security.txt`;
- presence of `<meta name="robots" content="noindex">`, `<html lang>`, viewport meta, title and meta description on the homepage.

Don't scan, fuzz, brute-force, probe for hidden or sensitive files (`.env`, `.git`, admin paths), or send many requests. For those checks, recommend the user run tools such as Mozilla HTTP Observatory, SSL Labs or an OWASP ZAP baseline scan against their **own** environment. Where a passive check contradicts an answer, report what you observed and lower that item's status accordingly.

### Step 5: Score and prioritize
For each question assign a status:
- **Pass**: "Yes" (and no contradicting evidence).
- **Partial**: "Partly", or "Yes" contradicted by a passive check.
- **Gap**: "No".
- **Unknown**: "Not sure". Treat it as a gap for prioritization and say so: an unknown control usually means it isn't in place.
- **N/A**: doesn't apply to this site.

Priority = the question's **severity if missing** (from `questions.md`), adjusted by the profile. Raise it one level when risk is clearly higher and the question's own severity line doesn't already account for it, e.g. Q2/Q5 for a site with user accounts, Q12 for sites handling health, financial or children's data. Partial and Unknown items keep the question's severity, and you can lower it one level if the remaining gap is minor. Don't raise an Unknown item. Say it may be raised once checked. Whenever you raise or lower a priority, state the reason in one italic line under the item.

Readiness score = Pass ÷ (20 core questions − N/A) × 100, rounded. Report it with the counts. Present it as a rough progress indicator, not a certification.

If the Security Deep Dive was done, score it **separately** (Pass ÷ (12 − N/A) × 100) and never merge it into the readiness score. Prioritize S-items with the same rules and put them in the same Critical/High/Medium/Low sections as core items, labelled S1–S12.

### Step 6: Write the report
Follow `references/report-template.md` exactly. Key rules:
- Put the most important thing first. List **Critical** then **High** items before anything else, each with concrete next actions taken from the "If missing" section, tailored to their platform (e.g. name the WordPress setting or Shopify app category instead of a generic instruction).
- Every legal item ends with "confirm with a qualified lawyer for your jurisdiction". Never state that a site *is* compliant with a law.
- Every security item that recommends testing says to test only systems you own or are authorised to test.
- Keep it actionable. Use short bullets, and one sentence of "why it matters" per gap at most.
- End with the disclaimer block from the template, verbatim.

Offer the report as a Markdown file if the environment supports creating files. Otherwise put it inline.

## Disclaimer

Show this short version in Step 1 and the full version from `report-template.md` at the end of the report:

> This checklist is for general information only. It is not legal, security or professional advice and doesn't guarantee compliance or security. You remain responsible for your website. Consult qualified professionals before relying on it.

## Tone and style

- Friendly, direct, and free of jargon. Expand acronyms the first time (e.g. "HSTS (HTTP Strict Transport Security)").
- Don't shame the user for gaps. Most sites have several.
- Write in English. If the user writes in another language, you may reply in their language, but keep standard technical terms (CSP, HSTS, WCAG) in English.
- Don't invent laws, deadlines or numbers. If unsure whether a rule applies to their case, say "may apply - check with a professional".
