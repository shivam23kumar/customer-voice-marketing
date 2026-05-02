# customer-voice-marketing

A Cowork plugin that helps content marketing managers write **ad copy** and **email campaigns** that sound like your brand and are grounded in real customer language — not generic AI prose.

Built as a thin marketing layer on top of Anthropic's official [Brand Voice](https://claude.com/plugins/brand-voice) plugin. Brand Voice handles "what does our brand sound like." This plugin handles "given that voice plus this week's customer conversations, draft my campaign."

## What it does

- Drafts ad copy and email campaigns in your brand voice, on demand.
- Pulls fresh customer language from your chat tool, call recordings, docs, and support inbox every time it drafts copy. So this week's campaign reflects this week's customer conversations — not last quarter's brand bible.
- Shows you the receipts. Every draft cites the customer quotes and themes it was grounded in. No fabrication.
- Improves over time. When you edit a draft, the plugin offers to remember your preferences in your brand profile.

## Who it's for

Content marketing managers and copywriters. No coding required.

## Install

1. Download `customer-voice-marketing.plugin` from the [Releases page](#).
2. Open Cowork, go to Settings → Plugins, drag the file in.
3. Done. The first time you ask it to write something, it walks you through a 3-minute setup.

## How to use

Just ask in chat:

- *"Write a launch email for our new pricing tier."*
- *"Give me 5 Meta ad headlines for our spring promo."*
- *"What language are customers using to describe our onboarding?"*

The first time, you'll be guided through a 3-minute setup that:

1. Connects you to Anthropic's official Brand Voice plugin (recommended) — or, if you'd rather not install another plugin, lets you paste 5–10 pieces of copy you're proud of so the plugin can extract a voice profile.
2. Asks you to point at where your team's customer conversations live (chat channels, call-recording tool, doc folders, support inbox).

After that, every draft uses your brand profile plus fresh customer language pulled just-in-time.

## What gets created in your workspace

Two editable files:

- `brand_voice.md` — the plugin's understanding of your brand voice. Edit any time.
- `customer_sources.md` — where to pull fresh customer language from. Edit any time.

These are the brain of the plugin. Both live in your workspace so your team can see them, version-control them, and improve them over time.

## Connectors

This plugin is tool-agnostic. It works with whatever your org has connected — Slack or Teams or Discord, Gong or Granola or Fireflies, Notion or Drive or Confluence, Intercom or Zendesk. See [CONNECTORS.md](./CONNECTORS.md) for the full list and how the plugin maps generic categories to your specific tools.

## Skills included

- **setup** — first-run wizard that creates `brand_voice.md` and `customer_sources.md`.
- **write-ad-copy** — drafts ad copy for any paid channel, grounded in customer language.
- **write-email-campaign** — drafts single emails or full sequences, grounded in customer language.
- **pull-customer-voice** — pulls verbatim customer quotes and themes about a topic. Used internally by the writing skills, also available standalone for research.

Four skills, deliberately small. The point is to do these well, not to ship a 15-skill suite.

## Embedding in workflows

The skills work in chat, but they're also callable from scheduled tasks and hooks if someone on your team is technical enough to wire them up. Common patterns:

- Weekly scheduled run of `pull-customer-voice` to produce a digest.
- Auto-draft a re-engagement email when a CRM trigger fires.
- Refresh your `brand_voice.md` from this quarter's top-performing emails.

## Contributing

PRs welcome. The four skills inside `skills/` are deliberately small — fork, customize, share back. If you build a useful new skill (e.g. *write-landing-page*, *write-social-post*), open a PR.
