---
name: write-email-campaign
description: Draft email campaigns — single emails or full sequences (welcome, launch, nurture, re-engagement, abandoned-cart, onboarding, customer-success check-in, etc.) — in the user's brand voice, grounded in real customer language pulled fresh from their connected tools. Use when the user asks to "write an email", "draft a launch sequence", "create a welcome email series", "write a re-engagement campaign", "draft an onboarding flow", "write a nurture sequence", or any similar request for marketing or lifecycle email copy. Always shows receipts — the customer quotes and themes that grounded the draft.
---

# Write email campaign

Draft single emails or full sequences that sound like the brand and use real customer language pulled fresh.

## Pre-flight

### 1. Check for required workspace files

Look for `brand_voice.md` and `customer_sources.md` in the user's workspace.

- **If either is missing**: tell the user "I need to learn your brand first — about 3 minutes — then I'll come right back to your email." Hand off to the `setup` skill, passing along the original request and any details already provided. Resume here once setup completes.
- **If both are present**: read them both before doing anything else.

### 2. Clarify only what you can't infer

- **Single email or sequence?** If sequence: how many emails, over what cadence (e.g. "5 emails over 2 weeks", "3 emails over 3 days").
- **Campaign type** — launch, nurture, re-engagement, welcome, onboarding, abandoned-cart, customer-success check-in, win-back, etc.
- **Trigger or offer** — what's the email about? A new feature, a discount, a re-engagement attempt, a usage milestone, a lapsed-account warning?
- **Audience segment** if not obvious from `brand_voice.md`.

Keep clarification to one round.

## Pull customer voice (just-in-time)

Invoke `pull-customer-voice` internally as a primitive (embedded mode), scoped to the campaign topic. For a sequence, also request **theme variation across the sequence** — e.g. "objections" themes for a re-engagement step 2, "wins" themes for a nurture step 3, "first-week pain points" for an onboarding email 1.

Wait for results before drafting.

If `pull-customer-voice` returns nothing usable, give the user the same paste-or-proceed choice from the ad-copy skill:

> "I couldn't find recent customer language about [topic] in your connected sources. Want to paste a few customer quotes I should ground this in? Or should I draft from brand voice alone?"

## Draft

For each email in the campaign, output the following structure:

### Email N: [purpose in 3–5 words]

- **Send timing & purpose** — one line. E.g. "Day 0, immediately after signup. Confirm signup + set first-week expectation."
- **3 subject line options** — vary the angle (curiosity, value-first, urgency, plain). Each must honor the brand voice.
- **Preview text** — one line, complements the subject without repeating it.
- **Body** — match `brand_voice.md` rhythm and vocabulary tier exactly. Open with a hook that draws on a customer pain or insight from the pulled voice when possible. Body grounded in pulled customer language (adapt, don't quote verbatim in the body itself).
- **CTA** — pull from the `Common CTAs` list in `brand_voice.md` if applicable, or write a new one that fits the voice. State both the button text and the destination.
- **Design notes** — brief, e.g. "1 image at top, 1 button, no header bar. Mobile-first."
- **Success metric to watch** — one line. E.g. "Open rate" / "CTR to pricing page" / "Reply rate".

For sequences, after all emails, add a **Sequence-level notes** section:
- How the sequence escalates (or de-escalates)
- Drop-off points to watch
- Whether to branch based on engagement (e.g. "If no open by email 2, send the cold-reset version")

## Show receipts

Below the email(s), output a `## Grounded in` section, identical in shape to `write-ad-copy`:

**Themes** (3–5 themes from the customer-voice pull that informed the campaign):
- *[Theme name]* — [one-sentence description] — [count] quotes

**Verbatim quotes** (2–5 quotes that directly shaped specific phrases or framings):
- > "[Verbatim quote]" — [Source: ~~chat #feedback, 2026-04-28]
- > "[Verbatim quote]" — [Source: ~~call recordings, deal "Acme Corp", 2026-04-22]

**Voice anchors matched** (which anchors from `brand_voice.md` you mirrored):
- Email 1 opening mirrors: "[anchor sentence]"
- Email 3 CTA pattern: "[CTA pattern]"

This section is **non-negotiable**. Never skip. Never fabricate quotes. If a section is empty, say so plainly.

## Offer to refine the brand profile

If the user edits the draft and you can detect a pattern (consistent shortening, swapped words, changed CTAs), surface it once at the end:

> "I noticed you simplified 'effortless' → 'simple' twice. Want me to remember that?"

If they say yes, append the rule to `brand_voice.md` under taboo words / vocabulary notes. Show the diff before saving.

## Style for this skill

- One email = one job. Don't cram multiple CTAs into a single email unless `brand_voice.md` explicitly says the brand does that.
- Subject lines never use ALL CAPS, never start with "RE:" or "FW:" unless `brand_voice.md` permits it.
- Sequences should feel like a single thread, not unrelated emails. Reference earlier sends when natural.
- Keep design notes practical — if a brand uses plain-text emails, don't propose hero images.
- Never invent customer quotes.
