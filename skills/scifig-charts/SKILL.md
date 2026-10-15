---
name: scifig-charts
description: Use when the user wants a publication-ready plot or data chart from their own data (CSV, TSV, a table in the conversation, a spreadsheet), or wants to change a chart SciFig made — plot it with the SciFig MCP tools generate_chart and edit_chart.
---

# Data charts with SciFig

Charts are plotted from the user's own data. Only use data the user gave you; never invent, complete or round values. If the data or the comparison to plot is unclear, ask one short question first: every chart costs credits.

## Making a chart

Call `generate_chart` with exactly one of:

- `data`: CSV or TSV text including the header row (`data_format` is detected from the first line, or set it to `csv` / `tsv`);
- `rows`: one JSON object per row, column name to value (up to 100,000 rows).

Either way the data is at most 1 MB (1,000,000 characters). `request` says what to plot in plain language (1–16,000 characters), for example "box plot of response by group with individual points".

Optional:

- `chart_types`: supported chart types, when the user named one (`boxplot`, `violin`, `kaplan_meier`, `volcano`, `forest`, `dose_response`…); otherwise SciFig chooses from the data;
- `journal_style`: `nature`, `science`, `cell`, `plos`, `acs` or `default`, when the user names a journal;
- `figure_width`: `single`, `onehalf` or `double` column (PLOS and ACS have no `onehalf`);
- `color_palette`: `okabe_ito` (default, colour-blind safe), `wong`, `tol_bright`, `tol_muted`, `nature`, `science`, `nejm`, `lancet` or `jama`.

The tool takes text only. For an Excel workbook, a workbook with several sheets, or data larger than 1 MB, the user can call `POST /charts/generations` of the SciFig API (it accepts XLSX and XLS with `sheet_name`, up to 8 MB), or export the sheet as CSV and paste it.

Creating or editing requires `charts:generate`; `get_job` and `list_figures` require `jobs:read`. These scopes are independent. Keep a stable `idempotency_key` for each intended creation or edit, and reuse it only when retrying that exact request.

## Results

A finished chart has PNG, PDF, TIFF and EPS outputs of the same figure, sized for the journal column, and a link that opens it in the SciFig DataChart workspace. Show the user the download links and the editor link.

Creation waits about 25 seconds; charts often take longer. If the job is still running, call `get_job` later. Never call `generate_chart` again for the same request: that creates and charges a second chart.

## Changing a chart

Call `edit_chart` with the `job_id` of the latest version and an `instruction` (1–16,000 characters). It keeps the data, chart types, journal style and palette.

- `REVISION_CONFLICT` with `PARENT_JOB_RUNNING`: the chart is still being made. Wait, then retry with the same `idempotency_key`.
- `REVISION_CONFLICT` with `NOT_LATEST_REVISION` or `EDIT_IN_PROGRESS`: a newer version exists or another edit is running (possibly made on the website). Use the newest job, after it has finished.
- `INVALID_REQUEST` with `JOB_NOT_SUCCEEDED`: that chart failed; make a new one instead.

## Data errors

`INVALID_REQUEST` with a `DATACHART_…` code in `details.reason` means the data could not be read (for example `DATACHART_CSV_INVALID`, `DATACHART_TOO_MANY_ROWS`, `DATACHART_HEADERS_INVALID`). Nothing was charged. Tell the user what is wrong with the data and fix it only with their agreement.
