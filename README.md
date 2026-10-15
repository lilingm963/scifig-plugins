# SciFig · AI Scientific Figures, Data Charts and Flowcharts

Bring scientific illustration, data visualization and research workflows into your AI assistant with [SciFig](https://scifig.ai).

## Three connected workspaces

- **Illustration:** turn research ideas and references into scientific illustrations, diagrams and graphical abstracts.
- **DataChart:** plot your tables as scientific charts and refine them through conversation.
- **FlowChart:** build editable flowcharts for methods, protocols and study workflows.

Start with research assets, figure templates and color palettes, then refine the result with text and label editing, regional edits, background tools, recoloring, upscaling and SVG vectorization. Prepare work for publication with downloadable license certificates and export formats for papers and presentations.

**Paid credits from subscriptions and credit packs never expire. Annual plans start at $8.33 per month, billed annually.** Your website and AI assistant use the same SciFig account and credit balance.

This plugin brings the hosted SciFig MCP server together with four focused workflow skills.

## Claude Code

Add this repository as a marketplace, then install the plugin:

```text
/plugin marketplace add lilingm963/scifig-plugins
/plugin install scifig@scifig-plugins
```

Open `/mcp`, select SciFig, and sign in through OAuth. Review the requested permissions before allowing access. You can disconnect the assistant at any time in SciFig Settings → API Keys → Connected apps.

To connect only the remote server, without these skills:

```bash
claude mcp add --transport http scifig https://mcp.scifig.ai/mcp
```

## Codex and other clients

```bash
codex mcp add scifig --url https://mcp.scifig.ai/mcp
codex mcp login scifig
```

First inspect existing configuration and keep unrelated servers. Claude, ChatGPT and Cursor can connect a custom remote MCP server at `https://mcp.scifig.ai/mcp`. Client availability depends on your account and plan. The portable `plugin.json` and `mcp.json` are included for hosts supporting Agent Plugins; the Claude manifest remains under `.claude-plugin/`.

Full instructions: [Install guide](https://scifig.ai/developers/mcp/install.md) · [API documentation](https://scifig.ai/docs/api).

## Tools and permissions

| Workflow | MCP tools | Required business permission |
| --- | --- | --- |
| Illustrations | `generate_figure` | `images:generate` |
| Image editing, OCR and upscaling | `edit_figure`, `detect_text`, `upscale_figure` | `images:edit` |
| Figure export | `convert_figure` | `images:convert` |
| Data charts | `generate_chart`, `edit_chart` | `charts:generate` |
| Flowcharts | `generate_flowchart`, `edit_flowchart` | `flowcharts:generate` |
| Task status and history | `get_job`, `list_figures` | `jobs:read` |
| Credit balance | `get_credit_balance` | `credits:read` |

External uploads additionally require `files:write`. Permissions are independent; granting generation does not automatically grant uploads, job reads or balance access. `offline_access` allows an OAuth client to refresh after you sign out of the website and is not a business permission.

## Skills

- `scifig-onboarding`: connect, check permissions and handle authentication errors.
- `scifig-figures`: illustrations, image editing and flowcharts.
- `scifig-charts`: plot only the user's supplied data and edit charts.
- `scifig-export`: choose formats, DPI and upscaling for the intended output.

## Publication and export workflow

Generate a figure, refine the result, and export it for your paper, poster or presentation. Illustrations support editing and image post-processing; DataChart returns publication formats from your supplied data; FlowChart keeps diagrams editable as SVG.

The same credit balance works across the website and MCP. Browse the [current plans](https://scifig.ai/pricing) for included credits and features. Balance checks, OCR and supported PNG/PDF/TIFF/PPTX conversions are free.

The workflow skills handle asynchronous jobs, permissions, idempotent retries, print DPI and output selection. See the [API reference](https://scifig.ai/docs/api) for format details and technical parameters.

## Privacy and support

OAuth connects your own SciFig account. Never commit API keys, tokens or reviewer passwords. The plugin sends selected prompts, supplied data and attachments to the SciFig service to perform requested actions. Hosted-service handling, retention, third-party processing and account deletion are described in the [privacy policy](https://scifig.ai/privacy) and [terms](https://scifig.ai/terms). Contact the team through [SciFig support](https://scifig.ai/contact).

The MIT license applies to this plugin distribution; use of the hosted service is governed by its own terms.
