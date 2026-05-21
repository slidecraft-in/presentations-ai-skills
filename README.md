# Presentations.AI — Agent Skills

Public Agent Skills repository for [Presentations.AI](https://presentations.ai)
— skills that turn ideas, files, or outlines into complete, designed
presentations. Compatible with Claude.ai, Claude Desktop, Claude Code, and any
client that adopts the [Anthropic Agent Skills](https://agentskills.io) format.

## What's in here

| Skill | Purpose |
|---|---|
| [`presentations-ai/SKILL.md`](./presentations-ai/SKILL.md) | Guides the agent through the four creation flows: from a topic, from raw text, from a file, or a single slide. Covers preservation modes, async job polling, and routing rules. |
| [`presentations-ai/references/REFERENCE.md`](./presentations-ai/references/REFERENCE.md) | Detailed parameter reference for every Presentations.AI MCP tool. Read this when you need exact field names or constraints. |

## Companion connector

These skills pair naturally with the **Presentations.AI MCP connector**:

- **Connector URL**: `https://api.presentations.ai/mcp`
- **Auth**: OAuth 2.0 with PKCE + Dynamic Client Registration
- **Listed in**: the Claude.ai Connectors Directory once approved

The skills also work standalone — they describe the workflow even without the
connector loaded — but you get the best experience with both enabled.

## Five active tools (v0.3.2)

The Presentations.AI MCP server currently exposes:

- `create_presentation_from_topic` — full deck from a topic
- `create_single_slide` — one slide from a topic
- `create_presentation_from_content` — raw text → designed slides
- `create_presentation_from_file` — PDF / DOCX / PPTX / TXT / MD → designed slides
- `check_job_status` — poll an async creation job

Four tools (`create_presentation_from_slides`, `create_document_from_content`,
`update_document_content`, `refresh_presentation`) are temporarily disabled
upstream. The skill is written to use only the active surface.

## Installation

Add the connector first (so the model has tools available):

1. Visit the Claude.ai Connectors Directory and add **Presentations.AI**, or
2. Open Claude Desktop → Settings → Connectors → Add Connector → URL
   `https://api.presentations.ai/mcp`

Then point Claude at this repo as a skill source (slash-command install) or
copy `presentations-ai/SKILL.md` into your local Claude skill directory.

## License

MIT. See [LICENSE](./LICENSE).

## Support

- Docs: <https://console.presentations.ai/apiref/docs/>
- Email: <support@presentations.ai>
- Privacy: <https://presentations.ai/privacy>
- Terms: <https://presentations.ai/terms>
