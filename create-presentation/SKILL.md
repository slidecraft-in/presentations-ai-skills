---
name: create-presentation
description: Creates a designed slide deck with Presentations.AI from a topic, the user's text, or a reviewed outline, and delivers it as a PowerPoint, PDF, or slide images. Use when the user asks for a presentation, deck, slides, or a pitch deck.
license: MIT
compatibility: Requires the Presentations.AI MCP server (https://api.presentations.ai/mcp).
---

# Create a presentation

These are defaults; the user's explicit instructions take priority. Never spend credits or share a deck the user didn't ask for. The tools named here are the Presentations.AI connector's.

## Limits

- Files can't be uploaded through this connection. Use a file's text as `sources` if you can read it, or ask the user to paste it.
- Slides can't be edited one by one here. After the deck is made, the user changes slides in the app through `deck_url`.

## Gather what matters

Ask only about what the request leaves open and the result depends on; infer the rest.

- The subject: a topic or instructions in `prompt`, the user's own material as text `sources`, or both. Pass their material as given; don't add facts or figures they didn't provide.
- Optional: `slide_count`, `audience`, and a brand (below). `language` defaults to English, so set it when the user writes in or asks for another language.
- The file format, set with `export_format`: PowerPoint unless the user asks for PDF or slide images (a ZIP with one image per slide). It's fixed when the deck is created and can't be changed here later. On plans without PowerPoint export, a PowerPoint costs extra credits and PDF doesn't, so mention the format and that cost when you confirm.

## Review the structure first (optional)

When the user wants to see or shape the structure before any slides are made:

1. Tell them an outline costs credits of its own, on top of the deck. Then call `create_outline` with the topic or text.
2. Show the outline and make the user's changes to it yourself. Calling `create_outline` again spends credits again.
3. Pass the edited outline as `deck.outline` to `create_presentation`. An outline can't be combined with `prompt` or `sources`.

## Apply a brand

If the user names a brand or company style, find it with `search_brand_kits` and pass the chosen result's id as `deck.brand_kit_id`. If several could fit, ask which one; if none match, say so and ask whether to continue without a brand. Never guess or invent an id.

## Create and wait

1. Call `create_presentation`. Retrying an identical call returns the same deck, unless that run failed or the deck was deleted. Pass a new `request_id` only when the user wants another copy.
2. Tell the user it has started and usually takes a few minutes, then keep checking without waiting for a reply: call `get_task` with the returned `task_id` and a `wait_time` of up to 60 seconds, until `status` is COMPLETED or FAILED.
3. If it keeps running well past a few minutes, stop checking, give the user `deck_url`, and offer to check again later.

If it fails, say in plain words that the deck didn't finish, without quoting technical details from `error`, and ask before trying again: a new attempt can spend credits again. If a tool reports a credits or plan problem, explain it plainly, with the balance from `whoami` if the user wants it. Don't suggest an upgrade or a purchase.

## Deliver the file

When the task is COMPLETED, call `get_export` with the `deck_id`. Give the user the `download_url`, naming the file type from `format`, and `deck_url` to open the deck in the app. The download link expires soon; when it has, call `get_export` again for a fresh one.

## Share only when asked

If the user asks to share without saying how, ask whether they want a link anyone can open or to invite specific people.

- Link: `share_deck`.
- People: `create_grant` with each person's email as `subject` and a `role` of VIEW, COMMENT, or EDIT. Ask for whichever of these the user didn't give.

## Never

Invent links, slide content, credit amounts, or statuses. Use what the tools return.

To find, rename, move, share, download again, or delete decks that already exist, follow the manage-presentations skill.
