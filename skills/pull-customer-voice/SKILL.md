---
name: pull-customer-voice
description: Pull fresh customer language — verbatim quotes and themes — from the user's connected tools (chat, call recordings, docs, support), scoped to a specific topic. Use as a STANDALONE research skill when the user asks "what are customers saying about X", "give me a customer voice digest", "find recent feedback on pricing", "what's the buzz around our onboarding", "summarize complaints from this week", or similar voice-of-customer research questions. ALSO used internally as a primitive by write-ad-copy and write-email-campaign — when called as a primitive, return structured output without conversational chrome.
---

# Pull customer voice

Surface verbatim customer quotes and themes about a specific topic from the sources configured in `customer_sources.md`. Two distinct entry points: standalone (for research) and embedded (called by writing skills before they draft).

## Pre-flight

### 1. Detect entry mode

Check whether you were invoked by:
- **A user message directly** (standalone mode) — they want research output.
- **Another skill in this plugin** passing a topic + "embedded" flag (embedded mode) — return structured data, skip conversational chrome.

### 2. Check for required workspace files

Look for `customer_sources.md` in the user's workspace.

- **If missing**: hand off to the `setup` skill, passing along the original task. If embedded mode, also pass back the calling skill's deferred task.
- **If present**: read it before doing anything else.

### 3. Get the topic

- **Embedded mode**: the calling skill passes the topic. Don't ask.
- **Standalone mode**: the topic comes from the user's message. If unclear, ask one question: "What topic should I scope to?"

## Pull from each configured source

For each source listed in `customer_sources.md`, run the appropriate query.

**Default time scope**: last 30 days, unless `customer_sources.md` says otherwise or the user specified a different window.

### `~~chat` channels

For each channel listed:
- Search messages mentioning the topic or close synonyms.
- Pull the original message and any 1–2 reply messages that add context.
- Note the channel name and message date for attribution.

### `~~call recordings`

For the configured scope (e.g. "last 30 days customer calls", "deal stage = discovery"):
- Search transcripts for mentions of the topic.
- Pull the verbatim sentence(s) plus 1 sentence of surrounding context.
- Note the call/customer/date when available.

### `~~docs`

For each configured folder/page:
- Search for the topic.
- Pull relevant excerpts from interview transcripts, research notes, VoC summaries.
- Note the doc title and section.

### `~~support`

For configured tags/queues:
- Search tickets mentioning the topic.
- Pull customer-side messages only (not agent replies).
- Note the ticket ID, customer name (if appropriate), and date.

### Sources that return nothing

Skip silently. Do not pad with generic or unrelated quotes. Quality over coverage.

### No sources configured

If `customer_sources.md` indicates no live sources are configured, return immediately with a clear empty result. The calling skill (or user) will fall back to manual quote-pasting.

## Synthesize

Output two sections, in this order:

### Verbatim quotes

Default 5–10 quotes. Fewer if not enough usable matches were found — never fabricate to hit a count.

Format each quote:
```
> "[Verbatim quote — exact words, including typos, fragments, profanity if present]"
> [Source: ~~chat #feedback, 2026-04-28]
```

If no attribution available: `[Source: unattributed]`.

**Verbatim means verbatim.** Do not tidy up customer language. Their typos, fragments, and word choices are the voice — that's the whole point.

### Themes

3–5 themes. Each theme:
- A short label (3–5 words)
- A one-sentence description
- A count of supporting quotes

Format:
```
- **Pricing surprise** (4 quotes) — Customers expected the upgrade to cost less, based on what they saw on the comparison page.
- **Onboarding friction** (3 quotes) — New users get stuck at the integration step and can't tell which connector they need.
```

## Behavior depending on entry mode

### Embedded mode (called by write-ad-copy or write-email-campaign)

- No conversational opening or wrap-up.
- Return ONLY the two sections (Verbatim quotes, Themes) in raw structured form.
- If empty, return a clear empty marker so the caller can fall back gracefully.
- Don't ask follow-up questions — the calling skill already clarified the topic.

### Standalone mode (called directly by user)

- Open with a one-line summary of what was searched. E.g. *"Searched 3 ~~chat channels and last-30-days of ~~call recordings for 'pricing'."*
- Output the two sections.
- End with: *"Want me to draft an ad or email using these? Or run a different topic?"*

## Style for this skill

- Verbatim means verbatim. Never clean up customer language — typos, fragments, profanity all stay.
- Always cite the source. If you can't, say "(unattributed)".
- If sources are sparse, output what you have and explicitly flag the limitation. Don't pad.
- Never fabricate quotes. The whole value of this skill collapses if you do.
- If a quote contains personal information that shouldn't be exposed (full names of small-company contacts, specific deal amounts), redact subtly: `"[Customer at Acme] said: '...'"`.
