# Presentations.AI — Tool Reference

Detailed parameter docs for all 9 MCP tools.

## create_presentation_from_topic

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `topic` | string | Yes | Topic or brief (max 500 chars) |
| `slideCount` | integer | Yes | Number of slides (1-50) |
| `exportType` | enum | Yes | `ppt`, `pptx`, `pdf`, `image`, `render`, `share` |
| `language` | string | No | Language code (default: `en`) |
| `domain` | string | No | Company domain for branding |
| `target_audience` | enum | No | `executive-leadership`, `general-employees`, `clients-customers`, `students-trainees`, `technical-team`, `general-audience` |
| `tone` | enum | No | `professional`, `conversational`, `authoritative`, `persuasive`, `educational` |
| `callback_url` | string | No | HTTPS webhook URL |
| `immediatePollUrl` | boolean | No | Return job ID immediately for polling |

## create_single_slide

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `topic` | string | Yes | Slide topic (max 500 chars) |
| `exportType` | enum | Yes | Output format |
| `language` | string | No | Language code (default: `en`) |
| `domain` | string | No | Company domain |
| `target_audience` | enum | No | Same as create_presentation_from_topic |
| `tone` | enum | No | Same as create_presentation_from_topic |
| `callback_url` | string | No | HTTPS webhook URL |

## create_presentation_from_content

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `content` | string | Yes | Raw text content |
| `exportType` | enum | Yes | Output format |
| `topic` | string | No | Title override; instruction text when preservationMode is `instruction` |
| `slideCount` | integer | No | Slides (1-50). Omit for `preserve` mode |
| `language` | string | No | Language code (default: `en`) |
| `domain` | string | No | Company domain |
| `preservationMode` | enum | No | `enhance`, `preserve`, `summarize`, `instruction` |
| `target_audience` | enum | No | Same as create_presentation_from_topic |
| `tone` | enum | No | Same as create_presentation_from_topic |
| `callback_url` | string | No | HTTPS webhook URL |

## create_presentation_from_file

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `file_data` | string | Yes | Base64-encoded file (max 5MB) |
| `file_name` | string | Yes | Original filename with extension |
| `mime_type` | enum | Yes | MIME type (see supported list) |
| `exportType` | enum | Yes | Output format |
| `topic` | string | No | Topic override |
| `slideCount` | integer | No | Slides (1-50). Omit for `preserve` mode |
| `language` | string | No | Language code (default: `en`) |
| `domain` | string | No | Company domain |
| `preservationMode` | enum | No | `enhance`, `preserve`, `summarize`, `instruction` |
| `target_audience` | enum | No | Same as create_presentation_from_topic |
| `tone` | enum | No | Same as create_presentation_from_topic |
| `callback_url` | string | No | HTTPS webhook URL |

**Supported MIME types:** `application/pdf`, `application/vnd.openxmlformats-officedocument.presentationml.presentation`, `application/vnd.ms-powerpoint`, `application/vnd.openxmlformats-officedocument.wordprocessingml.document`, `application/vnd.ms-word`, `text/plain`, `text/markdown`, `application/rtf`

## create_presentation_from_slides — _currently disabled_

_This tool is temporarily inactive on the server. The backend function still
exists but depends on the upstream `/getMeta` AI classification endpoint, which
has been removed. Re-enable once that dependency is replaced._

<!--
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Presentation title (max 100 chars) |
| `slides` | array | Yes | Array of slide objects (1-50) |
| `type` | enum | No | Template: `ideatodeck`, `business_presentation`, `technical_comparison`, `product_showcase`, `training_material`, `pitch_deck`, `report_summary` |
| `domain` | string | No | Company domain |
| `exportType` | enum | Yes | Output format |
| `language` | string | No | Language code (default: `en`) |
| `callback_url` | string | No | HTTPS webhook URL |

### Slide object schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | Yes | Slide title (max 200 chars) |
| `section` | string | Yes | Body content (max 5000 chars) |
| `background_images` | string[] | No | Image URLs (max 5) |
| `graphicType` | enum | No | `Cover`, `Mission Vision`, `Key Features & Benefits`, `Market Opportunity`, `comparison` |
| `layout_types` | enum[] | No | `coverpage`, `table` |
| `text_suitability` | enum | No | `condensed`, `expanded`, `bullets` |
-->

## create_document_from_content — _currently disabled_

_This tool is temporarily inactive on the server. It overlaps with
`create_presentation_from_content` (which handles raw text via AI) and returned
only a private editor URL with no exportType or share-URL option. Re-enable
when a clearly distinct use case is reintroduced._

<!--
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Presentation title |
| `slides` | string | Yes | JSON string of slide content array |
| `type` | string | No | Document type |
| `domain` | string | No | Company domain |

**Note:** Does not support `exportType`. Returns an editable document URL.
-->

## update_document_content

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `docId` | string | Yes | Document ID to update |
| `slides` | string | Yes | JSON string of slide operations |

### Slide operations

| Action | Description |
|--------|-------------|
| `update` | Replace content at slide index |
| `add` | Insert new slide at index |
| `delete` | Remove slide at index |

Indices are 0-based. Changes apply immediately — no re-export needed.

## refresh_presentation

Regenerate an existing presentation. Use when the user wants the whole deck redone rather than tweaking specific slides.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `docId` | string | Yes | Document ID of the presentation to regenerate |
| `file_data` | string | No | Base64-encoded source file (max 5MB) to reshape the presentation with new content |
| `file_name` | string | No | Original filename with extension (required when `file_data` is provided) |
| `mime_type` | enum | No | MIME type (required when `file_data` is provided) |

**Supported MIME types:** same list as `create_presentation_from_file`.

## check_job_status

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | string | Yes | Job ID from async operation |

**Response states:** `pending` (processing), `completed` (includes URL + doc ID), `failed` (includes error).
