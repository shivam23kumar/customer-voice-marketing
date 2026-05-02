# Connectors

## How tool references work

Plugin files use `~~category` as a placeholder for whatever tool you have connected in that category. The plugin is tool-agnostic — it describes workflows in terms of categories (chat, call recordings, docs) rather than naming a specific product (Slack, Gong, Notion). When the plugin runs, it maps each `~~category` to the matching connector your org has installed.

This means the same plugin works for an org on Slack + Gong + Notion as for one on Teams + Granola + Confluence — no code changes.

## Connectors used by this plugin

| Category        | Placeholder         | Examples                                          | Required?    |
| --------------- | ------------------- | ------------------------------------------------- | ------------ |
| Brand voice     | (Anthropic plugin)  | Anthropic Brand Voice plugin                      | Recommended  |
| Chat            | `~~chat`            | Slack, Microsoft Teams, Discord                   | Optional     |
| Call recordings | `~~call recordings` | Gong, Granola, Fireflies, Otter, Chorus           | Optional     |
| Docs            | `~~docs`            | Notion, Google Drive, Confluence, Coda            | Optional     |
| Support         | `~~support`         | Intercom, Zendesk, Help Scout, Front              | Optional     |

## What happens if you don't have any of these

The plugin works without any customer-conversation connectors — it just falls back to asking you to paste customer quotes when drafting copy. The brand-voice profile can also be extracted from samples you paste, without the Brand Voice plugin installed.

But the plugin's value scales with how many sources you connect. With zero sources, it's an opinionated copywriter. With three or more, it becomes a copywriter grounded in fresh customer language every time it drafts.

## Connector recommendations the plugin makes

During setup, the plugin checks which connectors are installed in your org and recommends installing missing ones. Recommendations are framed as suggestions, never required — you can always skip and add them later.
