---
name: presentations-ai
description: "Use this skill whenever the user wants to create a presentation, slide deck, or single slide through Presentations.AI. Triggers include: any mention of 'presentation', 'deck', 'pitch deck', 'slides', 'PPTX', 'PowerPoint', 'keynote', 'training material', 'investor pitch', 'board deck', or requests to turn an article, paragraph, notes, transcript, PDF, Word doc, or PowerPoint file into a slide deck. Also use when the user asks for a single slide on a topic, or wants to export an existing topic as PPTX, PDF, image, or shareable web link. Pairs with the Presentations.AI MCP connector at https://api.presentations.ai/mcp. Do NOT use for editing Google Slides, generating video, spreadsheet tasks, or non-presentation document workflows."
license: MIT
---

# Presentations.AI

Turn a topic, a paragraph of text, an article, or a document into a complete
designed presentation. Backed by the Presentations.AI MCP server which exposes
5 active tools for creating slides on demand.

## Connector setup

This skill pairs with the Presentations.AI MCP connector. Once the connector
is added, the tools below are available automatically:

```
URL:       https://api.presentations.ai/mcp
Transport: Streamable HTTP
Auth:      OAuth 2.0 with PKCE + Dynamic Client Registration
```

## Routing — which tool for which intent

Pick the tool based on what the user provides:

| User has… | Use |
|---|---|
| A topic / brief, wants a full deck | `create_presentation_from_topic` |
| A topic, wants just one slide | `create_single_slide` |
| Raw text — article, notes, transcript | `create_presentation_from_content` |
| A source file — PDF, DOCX, PPTX, TXT, MD | `create_presentation_from_file` |
| A running async job to monitor | `check_job_status` |

## Workflow 1 — Create from a topic

Use `create_presentation_from_topic`. Fill in missing parameters from context:

| Parameter | How to choose |
|---|---|
| `topic` | The user's phrasing of what the deck is about |
| `slideCount` | Quick pitch: 3–5 · Standard meeting: 8–12 · Training: 15–25 · Keynote: 10–15. Default 10 if no signal. |
| `target_audience` | One of: `executive-leadership`, `clients-customers`, `general-employees`, `students-trainees`, `technical-team`, `general-audience` |
| `tone` | One of: `professional`, `conversational`, `authoritative`, `persuasive`, `educational` |
| `exportType` | `pptx` (editable PowerPoint) · `pdf` (read-only sharing) · `image` (PNG) · `share` (public web link) · `render` (preview) |
| `immediatePollUrl` | Set `true` for jobs that may exceed 60 seconds — returns a jobId to poll. |

Briefly explain your parameter choices before calling the tool.

For a **single slide** instead of a full deck, use `create_single_slide` with
just the topic; it always returns an image.

## Workflow 2 — Transform existing content

When the user provides text or a file, route to the right tool:

| Input | Tool |
|---|---|
| Raw text (article, notes, transcript) | `create_presentation_from_content` with `content: "..."` |
| File (PDF, DOCX, PPTX, TXT, MD) | `create_presentation_from_file` with `file_data` (base64, max 5 MB), `file_name`, `mime_type` |

Both tools accept a `preservationMode`:

| Mode | Use when… | `slideCount` |
|---|---|---|
| `enhance` | Source is short and needs expanding into full slides | Required |
| `summarize` | Source is long and needs condensing | Required |
| `instruction` | User has specific direction (e.g. "make it persuasive for execs") — put directions in `topic` | Required |
| `preserve` | Keep the source structure exactly as-is | Do **not** supply — derived from source |

If the source file is over 5 MB, advise the user to compress or split it
before retrying.

## Workflow 3 — Async jobs (preferred for any deck >5 slides)

The creation tools run AI generation and rendering steps that usually take
30–60 seconds, sometimes longer. To avoid client-side timeouts:

1. Pass `immediatePollUrl: true` on the creation tool — it returns a `jobId`
   in under a second.
2. Wait at least 30 seconds before the first poll, then call
   `check_job_status` with the `jobId`.
3. Poll every 10–15 seconds while status is `processing`.
4. When status flips to `completed`, the response contains the document URL.
   If status is `failed`, surface the error message to the user in plain
   language.

Most jobs finish within 60 seconds; treat anything past 2 minutes as a
likely failure and offer to retry.

## Error handling

Translate server errors to plain user-facing language:

| Server says | Tell the user |
|---|---|
| "out of credits" | Credits have run out — add more from the Presentations.AI account, then I'll retry |
| "plan has expired" / "plan is invalid" | The Presentations.AI subscription needs renewal in account settings |
| "API access is blocked" | The current plan doesn't include API access; suggest upgrading |
| "Rate limited" | Briefly wait and try again |
| "Invalid API key" / "API key is empty" | Re-check the API key in Presentations.AI developer settings |

Never expose raw error codes, MCP transport details, or server internals to
the user.

## Output handling

Every successful tool call returns a document URL. Always include:

1. The URL itself, plainly visible
2. The export format (PPTX, PDF, image, share)
3. The slide count (when known)
4. A one-line offer to refine or iterate

Keep responses brief — the user can read the deck for themselves.

## Reference

For exact parameter shapes, validation rules, and full tool schemas, see
[references/REFERENCE.md](references/REFERENCE.md).

## Currently disabled tools

Four MCP tools are temporarily inactive server-side. The skill is written
to use only the 5 active tools. The disabled tools are listed below for
future-self awareness:

| Tool | Reason |
|---|---|
| `create_presentation_from_slides` | Depends on the removed `/getMeta` classification endpoint |
| `create_document_from_content` | Overlaps with `create_presentation_from_content`; returned only a private editor URL |
| `update_document_content` | Slide-update flow not yet validated end-to-end |
| `refresh_presentation` | Refresh flow not yet validated end-to-end |

If a user asks to **edit** an existing deck (modify specific slides, refresh
the whole presentation, etc.), explain that slide-editing is not currently
available through the connector and offer to **regenerate** the deck via
`create_presentation_from_topic` or `create_presentation_from_content`.
