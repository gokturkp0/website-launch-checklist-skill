# Website Launch Checklist: a Claude Skill

> An interactive, 20-question pre-launch checklist for websites, with an optional **12-question Security Deep Dive** and **platform-specific guidance**. Claude asks the questions, then writes a **prioritized gap report** with concrete fixes covering security, privacy and legal pages, accessibility, performance, SEO and operations.

> [!IMPORTANT]
> This is **only a checklist**. It is **not** legal advice, a security audit or a compliance certification, and the authors **accept no responsibility** for how it is used. Read the [DISCLAIMER](DISCLAIMER.md) first.

---

## Why

Most website launches miss something: a cookie banner that loads trackers before consent, a leftover `noindex` from staging, no tested backups, an admin account without MFA. This skill turns a scattered set of security, legal and quality requirements into a 10-minute conversation, and ends with a to-do list sorted by what matters most.

## What it covers

| # | Section | Questions |
|---|---|---|
| A | **Security** | Q1 HTTPS & TLS · Q2 Security headers & cookies · Q3 Updates & dependencies · Q4 Authentication & access control · Q5 Forms & input · Q6 Secrets & exposed files · Q7 Backups · Q8 Domain, DNS & email security |
| B | **Legal & privacy** | Q9 Privacy policy · Q10 Cookie consent · Q11 Legal pages & business identity · Q12 Personal data handling · Q13 E-commerce & payments · Q14 Email marketing |
| C | **Accessibility** | Q15 WCAG 2.2 AA |
| D | **Performance, mobile & SEO** | Q16 Core Web Vitals · Q17 Mobile & cross-browser · Q18 SEO foundations |
| E | **Operations** | Q19 Monitoring & incident response · Q20 Launch readiness |

### Security Deep Dive (optional, S1–S12)

Offered automatically for higher-risk sites (user accounts, payments, APIs, self-managed servers, or a critical core security gap), or on request:

S1 Authorization & IDOR · S2 API security · S3 Account & session flows · S4 Database & data protection · S5 Server hardening · S6 Cloud storage · S7 Supply chain & CI/CD · S8 File uploads & SSRF · S9 Error handling (fail closed) · S10 Payments & webhooks · S11 Security testing · S12 Team & account hygiene

Questions that don't fit the platform are skipped automatically (for example, server hardening on Shopify). The deep dive is scored separately, so the core readiness score stays comparable.

### Platform addenda

Platform-specific checks and fixes for **WordPress/WooCommerce**, **Shopify**, **hosted builders** (Wix, Squarespace, Webflow), **JavaScript frameworks** (Next.js, Nuxt, SvelteKit, SPAs) and **server frameworks** (Laravel, Django, Rails, Express…). They turn generic advice into concrete steps for your stack.

### Profile

Before the 20 questions, a short profile (site type, audience region, platform, data collected) tailors the checklist. Jurisdiction-specific checks cover:

- 🇪🇺 **EU/UK**: GDPR, ePrivacy cookie consent, European Accessibility Act, Impressum, 14-day withdrawal, EU AI Act transparency
- 🇹🇷 **Turkey**: KVKK *aydınlatma metni* and *açık rıza*, İYS, ETBİS, distance-sales contract
- 🇺🇸 **US**: CCPA/CPRA and Global Privacy Control, CAN-SPAM, COPPA, ADA

Each requirement is backed by a primary source. See [`sources.md`](website-launch-checklist/references/sources.md).

## How it works

```
Profile (4 context questions)
      │
      ▼
20 questions, one section at a time  ──►  answers: Yes / Partly / No / Not sure / N/A
      │
      ▼
(optional) Security Deep Dive: 12 more questions for higher-risk sites
      │
      ▼
(optional) passive check of your own URL: headers, robots.txt, security.txt
      │
      ▼
Scoring: Pass / Partial / Gap / Unknown  ×  severity (Critical → Low)
      │
      ▼
Markdown report: summary, readiness %, prioritized actions, 30-day plan, disclaimer
```

See a full example in [`examples/sample-report.md`](examples/sample-report.md).

## Installation

### Claude Code
```bash
git clone https://github.com/gokturkp0/website-launch-checklist-skill.git
```
```bash
cp -r website-launch-checklist-skill/website-launch-checklist ~/.claude/skills/
```
For a single project, copy it to `.claude/skills/` in the project instead.

### Claude.ai / Claude desktop app
Zip the `website-launch-checklist` folder and upload it under **Settings → Capabilities → Skills**. Skills must be enabled for your plan.

### Claude API
Upload the folder as a custom skill via the Skills API. See Anthropic's [Agent Skills documentation](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview).

## Usage

Just ask in natural language:

- "Is my website ready to launch?"
- "Run the website launch checklist for my Shopify store."
- "Audit my WordPress site for security and GDPR gaps."
- "Quick mode: show me all 20 questions at once."

## Repository structure

```
website-launch-checklist-skill/
├── README.md
├── DISCLAIMER.md                    # Checklist only, no liability (also copied into the skill folder)
├── LICENSE                          # MIT
├── examples/
│   └── sample-report.md             # Example output
└── website-launch-checklist/        # ← the skill itself
    ├── SKILL.md                     # Workflow, scoring, tone
    ├── DISCLAIMER.md                # Ships with the skill
    └── references/
        ├── questions.md             # The 20 core questions, sub-checks, remediation
        ├── security-deep-dive.md    # Optional S1–S12 module + when to offer it
        ├── platform-addenda.md      # WordPress, Shopify, builders, JS & server frameworks
        ├── report-template.md       # Report structure + disclaimer block
        └── sources.md               # Authoritative sources
```

The skill uses **progressive disclosure**. Only the short `SKILL.md` frontmatter stays in context. The workflow loads when the skill triggers, and the reference files load only when they're needed.

## Design decisions

- **Severity, not just pass/fail.** A missing privacy policy and a missing favicon aren't equally urgent, so every gap is ranked Critical, High, Medium or Low, adjusted for the site's profile.
- **"Not sure" counts as a gap.** An unknown control is usually a missing one.
- **Depth on demand.** Twenty questions keep a brochure-site owner engaged. Higher-risk sites get the 12-question security deep dive instead of everyone answering 32 questions.
- **Passive checks only.** The optional URL verification reads public headers and well-known files. It never scans or probes, and it's meant only for sites you own.
- **Legal humility.** Legal items are always phrased as "likely required, confirm with a lawyer". The skill never declares a site "compliant".

## Contributing

Issues and pull requests are welcome, especially:
- jurisdiction-specific checks for other countries;
- updates when laws or standards change (please link the primary source);
- translations of the question wording.

## License

[MIT](LICENSE). Use it at your own risk; see the [DISCLAIMER](DISCLAIMER.md).
