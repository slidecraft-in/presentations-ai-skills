---
name: manage-presentations
description: Finds, inspects, renames, moves, shares, downloads again, or deletes presentations that already exist in the user's Presentations.AI workspace. Use when the user refers to a deck they already have; not for creating a new one.
license: MIT
compatibility: Requires the Presentations.AI MCP server (https://api.presentations.ai/mcp).
---

# Manage existing presentations

These are defaults; the user's explicit instructions take priority. Never change access, share, or delete unless the user asked for it. The tools named here are the Presentations.AI connector's.

## Find the deck

- `list_decks` lists decks, most recent first. Use `search` to match part of a name, or `project_id` to look inside a project. If `total` is more than you've seen, search or page before saying a deck isn't there. A deck still being generated isn't listed; fetch it with `get_deck` by its id.
- `list_projects` lists projects, for moving a deck or narrowing a search.
- `get_deck` shows one deck's details, its latest generation status, and the files the connected user exported from it, each with a fresh link.
- If more than one deck matches, show the names and ask which one. Never pick one silently.

## Change a deck

Use `patch_deck`:

- Rename it with `name`.
- Move it to another project with `project_id`. Taking a deck out of a project can't be done here.
- Change who in the workspace can open it with `visibility`. Set it only to what the user asked for; if their words don't map to one value, ask. Narrowing it cuts off people whose only access came from the old setting, so say who loses access. It never turns off a link or removes people invited by email.
- Switch the content it shows with `version`, only when the user asks for that.

## Download again

`get_export` returns a fresh `download_url` for the latest file the connected user exported from the deck; `format` says which file type it is. For an earlier file in another format, `get_deck` lists them. If there's no export, for example for a deck someone else made, give the user `deck_url` to open and download it in the app. A new file or a different format can't be made here; don't create a new deck for it.

## Share

If the user asks to share without saying how, ask whether they want a link anyone can open or to invite specific people.

- Link: `share_deck` returns the deck's link, creating one if needed. Pass `rotate` only when the user wants to replace the current link, so the old one stops working.
- People: `create_grant` with each person's email as `subject` and a `role` of VIEW, COMMENT, or EDIT. Ask for whichever of these the user didn't give. If someone already has a share on this deck, its role is replaced, even with a lower one.
- Turning off a link or removing a person's share can't be done here, and changing `visibility` doesn't do either. If the user wants that, point them to the app through `deck_url`; `has_share_link` tells you whether a link is active.

## Delete

Delete only a deck the user clearly identified and asked to delete. Once `delete_deck` succeeds, tell the user the deck was moved to trash; don't promise it can be restored.

## Out of scope

- Editing slide content: the user does that in the app, through `deck_url`.
- Making a new deck: follow the create-presentation skill.

## Never

Invent deck names, ids, links, or results. Use what the tools return.
