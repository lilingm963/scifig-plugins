---
name: scifig-figures
description: Use when the user asks for a scientific illustration, schematic, graphical abstract, diagram, flowchart or workflow diagram, or wants to edit an existing figure (labels, background, colours, aspect ratio, one region) — create or change it with the SciFig MCP tools. For plots from data use scifig-charts; for file formats and print resolution use scifig-export; for connection problems use scifig-onboarding.
---

# Scientific figures with SciFig

SciFig tools charge credits. Before creating, make sure the request is clear; ask one short question if the subject or the labels are ambiguous.

## Choosing a tool

- Illustration, schematic, graphical abstract, cell or molecule drawing → `generate_figure`. Put the labels the user wants in the prompt (3–20,000 characters). `quality: premium` gives the most detailed results; `reference_images` takes up to 3 images.
- Process, pipeline, protocol, study design, CONSORT diagram → `generate_flowchart` (prompt 1–8,000 characters). It can draw from up to 8 `attachments`: a protocol PDF, a Word or Markdown file, a draft image or SVG.
- Plot from data → use the scifig-charts skill.
- Change an existing illustration → `edit_figure`:
  - free-form change → `operation: instruct` with an `instruction` (up to 2,000 characters); add a `mask` PNG (white = area to change) to redraw one region only;
  - clean white background → `white_background`; different shape → `aspect_ratio`; new colours → `recolor` (optional `palette`, 1–8 hex colours);
  - fix or translate labels → call `detect_text` on the image first (free), then `edit_figure` with `operation: replace_text`, the detection id as `text_detection` and `replacements` by index.
- Change a flowchart you made → `edit_flowchart` with the latest job id of that flowchart.

## Passing images and files

Image and attachment arguments take a `file_id` from an earlier SciFig job, an https URL, or a data URL. Requests to the MCP server are limited to 4 MB, so a data URL can carry only about 3 MB; for a larger file, use an https URL. Never pass a scifig.ai URL: pass the `file_id` instead.

Image creation requires `images:generate`; editing, OCR and upscaling require `images:edit`; flowchart creation and editing require `flowcharts:generate`. External uploads additionally need `files:write`; polling and inline outputs need `jobs:read`. Missing scopes do not imply a broken connection.

## Jobs

Choose a stable `idempotency_key` for each intended creation or edit before calling the tool, and reuse it only for an exact retry. Creation tools wait about 25 seconds. If the result says the job is still running, call `get_job` with the job id a little later. Never call the creation tool again for the same request: that creates and charges a second job. If a job failed with `TASK_TIMED_OUT` and has `late_delivery_until`, it may still arrive for free; check `get_job` again later.

Each output has a `file_id` you can pass to the next tool and a download link valid for one hour (call `get_job` again for a fresh one). Flowchart results also include a link that opens them in the SciFig editor; show it to the user.

## Errors

- `INSUFFICIENT_CREDITS`: tell the user and share `details.top_up_url`; do not retry.
- `PLAN_REQUIRED`: the option (a model, reference images, 4K or 8K upscaling, SVG vectorization) needs a higher plan. Tell the user which option and suggest the pricing page https://scifig.ai/pricing; this error has no `top_up_url`.
- `CONTENT_REJECTED`: the content policy refused the request or the result; it is refunded. Suggest rewording; do not resend the same prompt.
- `REVISION_CONFLICT` on `edit_flowchart`: with `PARENT_JOB_RUNNING`, wait and retry with the same `idempotency_key`; with `NOT_LATEST_REVISION`, edit the newest version instead.
- `RATE_LIMITED`, `CONCURRENCY_LIMITED`, `SERVICE_UNAVAILABLE`: wait as long as the error says, then retry once with the same `idempotency_key`.
