---
name: write-ad-copy
description: Draft ad copy for any paid channel (Meta, Google, LinkedIn, TikTok, display, native, etc.) in the user's brand voice, grounded in real customer language pulled fresh from their connected tools. Use when the user asks to "write an ad", "draft Meta ad copy", "give me ad headlines", "write LinkedIn ads", "create Google ad variations", "write a paid social ad", "draft display ad copy", or any similar request for paid-channel copy. Always shows receipts — the customer quotes and themes that grounded the draft.
---

# Write ad copy

Draft ad copy that sounds like the brand and is grounded in real customer language pulled fresh from the user's connected tools.

## Pre-flight

### 1. Check for required workspace files

Look in the user's workspace for `brand_voice.md` and `customer_sources.md`.

- **If either is missing**: tell the user "I need to learn your brand first — about 3 minutes — then I'll come right back to your ad." Hand off to the `setup` skill, passing along: (a) the original ad request, (b) any details already provided. Resume here once setup completes.
- **If both are present**: read them both before doing anything else. They are the brain of every draft.

### 2. Clarify only what you can't infer

Ask the user for any of these you don't already have. Do NOT ask things you can already infer from `brand_voice.md` or context.

- **Channel and format** — Meta feed primary text, Google search headline + description, LinkedIn sponsored content, TikTok primary text, display banner copy, etc. Each has its own constraints.
- **Product or offer** being advertised — what's the user actually promoting?
- **Audience** if not obvious from `brand_voice.md` — sometimes campaigns target a sub-segment.
- **Variant count** — default to 5 if not specified.
- **Hard constraints** — character limits beyond the channel default, mandatory phrases, banned phrases for this campaign.

Keep clarification to one round. Two at most.

## Pull customer voice (just-in-time)

Invoke the `pull-customer-voice` skill internally — as a primitive, not a user-facing handoff. Pass it:
- The topic of the ad (product, offer, pain being solved)
- A request for: 5–10 verbatim customer quotes and 3–5 themes
- A flag indicating "embedded mode" so it returns structured output instead of conversational

Wait for results before drafting.

**If `pull-customer-voice` returns nothing usable** (no connectors configured, no relevant matches found):

> "I couldn't find recent customer language about [topic] in your connected sources. Want to paste a few customer quotes I should ground this in? Or should I draft from brand voice alone? (Drafting from brand voice alone is fine — just less grounded in current customer language.)"

Then proceed with whichever the user chooses.

## Draft

Generate the requested number of ad variants. For each:

- **Match `brand_voice.md` precisely** — voice descriptors, sentence rhythm, vocabulary tier, opening patterns, CTAs. The voice anchors are your reference for whether a sentence sounds right.
- **Pull at least one phrase, framing, or pain-point from the customer quotes you gathered** — but adapt the language, don't quote verbatim in the ad itself (verbatim quotes go in the receipts section, not the ad).
- **Respect the channel's character and format constraints** — Meta primary text vs. headline vs. description, Google's character limits, etc.
- **Vary the angle across variants** — don't ship 5 variants of the same hook. Cover at least three of: pain-led, outcome-led, social-proof-led, curiosity-led, specificity-led, contrarian.
- **Honor `Taboo words and phrases` from `brand_voice.md`** — never use anything on that list.

Output the variants in a clean numbered list, ready to copy-paste.

## Show receipts

Below the variants, output a `## Grounded in` section. Three subsections, in this order:

**Themes** (3–5 short themes from the customer-voice pull that informed the variants):
- *[Theme name]* — [one-sentence description] — [count] quotes
- ...

**Verbatim quotes** (2–4 quotes that directly shaped specific phrases or framings in the ads). Each quote with attribution where available:
- > "[Verbatim quote]" — [Source: ~~chat #feedback, 2026-04-28]
- > "[Verbatim quote]" — [Source: ~~call recordings, deal "Acme Corp", 2026-04-22]

**Voice anchors matched** (which voice anchors from `brand_voice.md` you mirrored):
- Variant 2 mirrors the rhythm of: "[anchor sentence from brand_voice.md]"
- Variant 4 echoes the opening pattern: "[opening pattern]"

This receipts section is **non-negotiable**. Never output ads without showing what they were grounded in. Never fabricate quotes. If a section is empty (e.g. no quotes pulled), say so explicitly: "*No verbatim quotes — drafted from brand voice alone.*"

## Offer to refine the brand profile (optional)

If during the conversation you noticed a tension between the user's likely instinct and the current `brand_voice.md` — e.g. they asked for something "punchy" but the brand profile says "considered" — surface it once at the end:

> "Side note: you asked for something punchy, but `brand_voice.md` describes the voice as 'considered, paragraph-style.' Want me to adjust the brand profile, or was this campaign deliberately off-voice?"

Don't update `brand_voice.md` without their explicit OK.

## Style for this skill

- No emoji unless `brand_voice.md` explicitly says emojis are part of the voice.
- Never invent customer quotes. If the source pull came back empty and the user didn't paste any, the receipts section says so plainly.
- The variants are the headline output. The receipts come below in a clearly separated section. The user should be able to copy-paste the variants without grabbing the receipts.
- If a channel has unusual constraints (e.g. TikTok caption length), state them once before the variants so the user knows you've respected them.
