# Report Template

Use this structure for the final report. Replace `{…}` placeholders. Never delete a priority section; if it would be empty, write "None 🎉" under the heading so the user sees it was checked. Only the "Observed during passive checks" section may be omitted.

---

```markdown
# Website Launch Checklist Report – {site name or URL}

**Date:** {YYYY-MM-DD} · **Site type:** {profile type} · **Platform:** {platform} · **Audience:** {regions}

## Summary

**Readiness: {score}%** ({pass} passed · {partial} partial · {gap} gaps · {unknown} unknown · {na} not applicable)
**Security Deep Dive: {score}%** ({counts}) – or "Not taken – recommended for your site" / omit this line if not relevant

{2–3 sentences: overall state, the single most urgent issue, and what to do first.}

| Area | Status |
|---|---|
| Security (Q1–Q8) | {✅ / ⚠️ / ❌ with count, e.g. "⚠️ 5/8 passed"} |
| Legal & Privacy (Q9–Q14) | {…} |
| Accessibility (Q15) | {…} |
| Performance & SEO (Q16–Q18) | {…} |
| Operations (Q19–Q20) | {…} |
| Security Deep Dive (S1–S12) | {…, only if taken} |

Legend: ✅ all applicable items pass · ⚠️ some items not passing · ❌ at least one Critical-priority item not passing

## 🔴 Critical – fix before launch

### {Qn or Sn. Title} – {Gap / Partial / Unknown}
**Why it matters:** {one sentence}
**What to do:**
- [ ] {concrete action, tailored to platform}
- [ ] {concrete action}
**Verify with:** {tool or check, e.g. "SSL Labs Server Test"}

{repeat for each Critical item}

## 🟠 High – fix before or right after launch

{same block format; "Why it matters" and "Verify with" may be omitted to keep it short}

## 🟡 Medium – fix in the first weeks

{same block format, may be shorter: title + action bullets}

## 🟢 Low – polish

{bullets}

## ✅ What you already have in place

- {Qn or Sn. Title} – {one-line note}

{If the deep dive was taken: one line listing deep-dive items marked N/A and why.}

## ❓ Not sure / not assessed

{List "Not sure" and unanswered items with how to find out, e.g. "Check your hosting panel for a Backups section" or "Run `curl -I https://example.com` and look for Strict-Transport-Security".}

## Observed during passive checks
{Only if Step 4 was performed: list what was fetched and what was observed. Otherwise omit this section.}

## Suggested 30-day plan

- **Week 1:** {Critical items}
- **Week 2:** {High items; spill remaining High items into Weeks 3–4 if needed}
- **Weeks 3–4:** {Medium items}
- **Ongoing:** monthly updates, quarterly restore test, yearly policy and accessibility review.

---

> **Disclaimer:** This report was generated from your own answers to a general-purpose checklist. It is provided for informational purposes only and does not constitute legal, security, compliance or other professional advice. It is not an audit, penetration test or certification and does not guarantee that your website is secure, accessible or compliant with any law or standard. Laws and best practices vary by jurisdiction and change over time; the checklist may be incomplete or out of date. You remain solely responsible for your website and for any decisions you make based on this report. The authors and contributors accept no liability for any loss or damage arising from its use. Consult a qualified lawyer and security professional before relying on it. See DISCLAIMER.md in the skill folder.
```

---

## Writing rules

- Actions are **checkboxes** (`- [ ]`) so the user can track progress.
- For legal items, end the action list with: `- [ ] Confirm with a qualified lawyer for {jurisdiction}`.
- For security items that suggest scanning: add "(only on systems you own or are authorised to test)".
- Don't repeat the full sub-check list for passed items. A one-line note is enough.
- Keep the whole report readable in about 5 minutes. When an item has more than 5 actions, keep the 5 most impactful and add "See also: {source}".
