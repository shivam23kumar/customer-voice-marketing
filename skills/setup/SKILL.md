---
name: setup
description: Run the first-time setup wizard for the customer-voice-marketing plugin. Use this when a sibling skill (write-ad-copy, write-email-campaign, pull-customer-voice) detected that brand_voice.md or customer_sources.md is missing from the user's workspace, OR when the user explicitly asks to "set up the marketing plugin", "configure brand voice", "redo marketing setup", or "reconfigure customer voice marketing". The setup takes about 3 minutes and produces two editable files in the user's workspace.
---

# Setup wizard

Walk the user through a 3-minute, two-step configuration of the plugin. Produce two files in the user's workspace: `brand_voice.md` and `customer_sources.md`.

## Entry conditions

Run this skill when:
- A sibling skill detected one or both required files missing and handed off here. Preserve the original task they were trying to do — you'll resume it at the end.
- The user explicitly asked to set up or reconfigure the plugin.

## Open the conversation

Greet briefly. Set expectation:

> "I need to learn two things before I can write copy that sounds like your brand: how your brand sounds, and where your customers' actual words live. About 3 minutes total. Ready?"

If a calling skill handed off a deferred task, mention it: "Once we're done, I'll come right back to the [email/ad/etc.] you asked for."

## Step 1 — Brand voice

Goal: produce `brand_voice.md` in the user's workspace.

### 1a. Check for Anthropic's Brand Voice plugin

Silently call `mcp__plugins__list_plugins` to see what's installed. Look for a plugin with name or slug containing `brand-voice` (the official Anthropic plugin).

### 1b. If Brand Voice IS installed

Tell the user: "I see you have Anthropic's Brand Voice plugin. That's the cleanest way to extract your tone — let's use it."

If Brand Voice doesn't yet have a profile configured, walk the user through pointing it at their brand materials (Notion, Drive, Confluence, etc.). Defer to Brand Voice's own setup for the actual extraction.

Once Brand Voice has produced a profile, summarize that profile into `brand_voice.md` in the user's workspace, using the structure in `references/brand_voice.template.md`. In the `Source` field, write: `Generated from Anthropic Brand Voice plugin on [today's date]`.

### 1c. If Brand Voice is NOT installed

Recommend it via `mcp__plugins__suggest_plugin_install` with the slug `brand-voice`. Frame it in one sentence: "Anthropic ships an official Brand Voice plugin that does this part really well. Want to install it now, or skip and go with a quick fallback?"

- **If they install it**, return to step 1b.
- **If they skip it**, run the fallback flow below.

### 1d. Fallback: extract voice from samples

If the user skipped Brand Voice, do the extraction here:

Ask the user to share 5–10 pieces of copy they're proud of — anything that captures the brand voice well. Accept any of: pasted text, attached files, links to docs in their `~~docs` tool, pointers to a folder in their workspace.

When the samples arrive, extract a voice profile that covers:
- 3–5 voice descriptors (adjectives like "warm but precise", "blunt", "playfully technical")
- The audience being addressed
- Sentence rhythm patterns (short and punchy? Long and considered? Mixed?)
- Vocabulary tier (casual / neutral / formal; jargon allowed?)
- Common opening patterns
- Common CTAs
- Taboo words and phrases (anything the brand consciously avoids)
- 2–3 verbatim "voice anchor" sentences from the samples — sentences so on-brand they could anchor any future draft

Write the result to `brand_voice.md` in the user's workspace using `references/brand_voice.template.md` as the structure. Source: `Extracted from N samples provided by [user] on [date]`.

### 1e. Confirm and offer to edit

Show the user the resulting `brand_voice.md` and explicitly say:

> "This file lives in your workspace. You can open it any time and edit it directly — I'll respect whatever's in there. Anything you want to change before we move on?"

If they want changes, edit the file based on their input.

## Step 2 — Customer conversation sources

Goal: produce `customer_sources.md` in the user's workspace.

### 2a. Probe what they have

Silently call `mcp__mcp-registry__list_connectors` to see which `~~chat`, `~~call recordings`, `~~docs`, and `~~support` connectors are installed in the user's org.

### 2b. For each category they HAVE a connector for, ask a targeted question

- **`~~chat`**: "Which channels in your `~~chat` tool have actual customer feedback or your team sharing customer quotes? Give me 1–5 channels."
- **`~~call recordings`**: "When you want recent customer-call insights, what should I scope to — last 30 days of all calls, a specific deal stage, a specific call category?"
- **`~~docs`**: "Are there any folders or pages in your `~~docs` tool with customer interviews, research notes, or VoC summaries?"
- **`~~support`**: "Any tags, queues, or topic filters I should use when pulling support conversations?"

For each, accept their answer and note exactly what they said — names, scopes, filters.

### 2c. For categories they DON'T have a connector for

Use `mcp__mcp-registry__suggest_connectors` to recommend one, framed gently: "If your team uses [category tool], connecting it would let me pull customer language directly. Want to add it now or later?" Don't push — most users will only have one or two of these.

### 2d. Write customer_sources.md

Use `references/customer_sources.template.md` as the structure. For each configured source, include:
- The connector category (with the `~~placeholder`)
- The specific channels, folders, scopes, or filters the user named
- A one-line note on what to expect there ("feature requests + complaints", "post-sale check-ins", "interview transcripts")

### 2e. Graceful degrade if no sources

If the user has zero customer-conversation connectors installed (or skips configuring all of them), still create `customer_sources.md` — but with a placeholder section saying:

> "No live sources configured. When you ask for ad copy or email campaigns, the plugin will ask you to paste relevant customer quotes manually."

This keeps the writing skills functional even with zero connectors.

## Wrap up

Show both files to the user with brief paths. Tell them:

> "You're set. These two files (`brand_voice.md` and `customer_sources.md`) are the brain of the plugin. Edit them any time — the plugin re-reads them before every draft."

If a calling skill handed off a deferred task, hand control back to that skill now, with all the context (original request, both files now exist).

## Style for this skill

- Conversational, not bureaucratic. The audience is non-technical content marketing managers.
- Default to questions with sensible recommendations, not open-ended "tell me about your brand."
- Never expose internal plugin file paths — only the workspace files (`brand_voice.md`, `customer_sources.md`).
- Keep total wall-clock time under 5 minutes. If you're trending longer, simplify.
- Never invent or assume voice characteristics that the samples don't actually show.
