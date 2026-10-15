---
name: scifig-export
description: Use when the user needs a SciFig figure, chart or flowchart in a particular file format or resolution — SVG to edit, PDF or TIFF for a journal, PPTX for slides, a higher-resolution PNG for a poster — and choose between convert_figure and upscale_figure.
---

# Exporting SciFig figures

Pick the format from what the user will do with the file. Ask only if the purpose is unclear.

| Purpose | Tool and arguments | Credits |
| --- | --- | --- |
| Edit shapes and text in Illustrator, Inkscape or Figma | `convert_figure` with `format: svg` (`vector_mode: editable_text` keeps text editable; `full_vector` traces everything as paths) | Paid; needs a paid plan |
| Journal submission | `convert_figure` with `format: tiff` or `pdf`, `dpi: 600` (line art: `1000`) | Free |
| Slides | `convert_figure` with `format: pptx` | Free |
| Web or a document | the PNG output as it is | Free |
| Poster, print or more pixels | `upscale_figure` with `scale: 4k` or `8k`, then convert if needed | Paid |

## What each tool does

- `convert_figure` to PNG, PDF, TIFF or PPTX is free, up to 200 conversions per day. `dpi` (300, 600 or 1000) is written into the file and sets the page size; it does not add pixels. For more pixels, upscale first.
- PNG and TIFF need a PNG source; PDF and PPTX also take JPEG. WebP sources can only be vectorized to SVG or upscaled.
- A PPTX holds the image on one slide; it is not editable shapes. For editable shapes use SVG.
- `upscale_figure`: `2k`, `4k` (Starter plan or higher) or `8k` (Plus plan or higher). Generated illustrations are already about 2K, so use `4k` or `8k`. A credit-pack balance counts as the Pro plan.
- Charts already come as PNG, PDF, TIFF and EPS in one job: use those outputs directly instead of converting.
- A flowchart is an SVG. Convert it to PNG, PDF, TIFF or PPTX with `convert_figure`; `width` (256–4096) sets its long edge in pixels, default 2048. The SVG render budget is 2048² total pixels; for a 2:1 diagram the largest valid long edge is 2896, so 4096×2048 is refused. This conversion is asynchronous; preserve the returned job ID. It cannot be vectorized again or upscaled.

## Limits and errors

- A conversion source is at most 20 MB; an 8K upscale may be larger and is refused with `FILE_TOO_LARGE`. Convert the 4K version instead.
- `OUTPUT_TOO_LARGE`: the converted file would exceed 24 MiB. A lower DPI does not help (it does not change pixels); convert a smaller image.
- `RATE_LIMITED` with `DAILY_CONVERSION_LIMIT`: the free daily conversions are used up; tell the user when they can continue (`Retry-After`).
- `SVG_NOT_RENDERABLE`: a flowchart contains text the server fonts cannot draw; edit the flowchart text, then convert again.
- `PLAN_REQUIRED` for SVG, 4K or 8K: tell the user which plan it needs and point to https://scifig.ai/pricing.

Pass the output's `file_id` from the earlier job, not its download link.

Conversion requires `images:convert`; upscaling requires `images:edit`. Uploading external sources additionally needs `files:write`, and polling results needs `jobs:read`. Check the granted scopes without requesting broader access automatically.
