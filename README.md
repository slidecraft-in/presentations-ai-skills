# Presentations.AI Agent Skills

[Agent Skills](https://agentskills.io) for [Presentations.AI](https://www.presentations.ai).
They walk an agent through making designed slide decks and managing existing ones with the
Presentations.AI MCP server.

| Skill | Use it to |
|---|---|
| [`create-presentation`](create-presentation/SKILL.md) | Create a deck from a topic, the user's text, or a reviewed outline, and deliver it as a PowerPoint, PDF, or slide images. |
| [`manage-presentations`](manage-presentations/SKILL.md) | Find, inspect, rename, move, share, download again, or delete decks that already exist. |

## Requirements

The skills use the tools of the Presentations.AI MCP server, so connect it first:

```
https://api.presentations.ai/mcp
```

Setup steps for each client are in
[presentations-ai-mcp-server](https://github.com/slidecraft-in/presentations-ai-mcp-server#connect-from-your-mcp-client).
Clients that support the [MCP Skills extension](https://modelcontextprotocol.io/extensions/skills/overview)
get both skills from the server itself.

## Install

- **Claude (claude.ai and Claude Desktop):** zip a skill folder, then go to
  **Customize → Skills**, click **+**, choose **Create skill → Upload a skill**, and upload
  the ZIP. Repeat for the second skill. Skills need **Code execution and file creation**
  turned on under **Settings → Capabilities**.
- **Claude Code:** copy both folders into `~/.claude/skills/` (all projects) or
  `.claude/skills/` (one project).
- **Gemini CLI:** install the
  [Gemini CLI extension](https://github.com/slidecraft-in/presentations-ai-gemini-extension),
  which includes both skills and the server, or run
  `gemini skills install https://github.com/slidecraft-in/presentations-ai-skills.git`.

## Support

- Docs: [docs.presentations.ai/docs/agents](https://docs.presentations.ai/docs/agents)
- Email: `support@presentations.ai`

## License

MIT, see [LICENSE](LICENSE).
