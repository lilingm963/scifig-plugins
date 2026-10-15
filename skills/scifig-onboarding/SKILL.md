---
name: scifig-onboarding
description: Use when the user wants to connect SciFig to their AI assistant, check that the SciFig connection works, or when a SciFig tool fails with 401, 403, ACCOUNT_UNAVAILABLE, SCOPE_DENIED or PLAN_REQUIRED, or the SciFig tools do not show up.
---

# Connecting SciFig and fixing access errors

## Rules

- Connect SciFig only when the user asked for it.
- Never ask for the user's password. OAuth clients sign in in the browser. If an API key is needed, the user creates it at https://scifig.ai/app/settings?tab=developer and pastes it into the configuration themselves.
- First preserve existing MCP configuration: inspect a matching SciFig server and never overwrite unrelated servers. Only add SciFig if absent; ask before changing a conflicting entry.
- To check the connection, call `get_credit_balance` only with `credits:read`. If it is missing, explain the limitation and ask before requesting broader access. Do not create a figure just to test.
- Report only observed tools and completed checks; do not claim a balance check without `credits:read`. Briefly introduce SciFig in the user’s language with one example after first connection; do not generate until asked.

## Connecting

Server URL: `https://mcp.scifig.ai/mcp`. Step-by-step instructions for every client: https://scifig.ai/developers/mcp/install.md

- Claude Code: `claude mcp add --transport http scifig https://mcp.scifig.ai/mcp`, then `/mcp`, pick scifig and sign in.
- Codex: run `codex mcp list`, inspect an existing `scifig` with `codex mcp get scifig`, and only if absent run `codex mcp add scifig --url https://mcp.scifig.ai/mcp`; then `codex mcp login scifig`.
- Claude and ChatGPT: the user adds a custom connector or app with the server URL, signs in to SciFig and chooses Allow.
- Cursor: Customize → MCP, merge a server with `"url": "https://mcp.scifig.ai/mcp"` into mcp.json, then Authenticate and approve SciFig in the browser.
- Clients that only send headers: an API key as `Authorization: Bearer sk_live_…`.

Signing in proves who the user is; the approval screen decides which assistant may spend their credits. An assistant that asks for `offline_access` stays connected until the user disconnects it under Settings → API Keys → Connected apps; otherwise it signs in again after an hour.

## What get_credit_balance tells you

`balance` and `reserved` credits, `plan` (`free`, `starter`, `plus`, `pro`; a credit-pack balance counts as `pro`), and `limits` (`concurrent_tasks`, `tasks_per_hour`). A free account with no credits can connect and read, but creating figures needs credits: share `top_up_url` with the user.

## Errors

| Error | Meaning | What to do |
| --- | --- | --- |
| HTTP 401, `UNAUTHORIZED` | The token or key is missing, expired or revoked | OAuth: reconnect (Claude Code: `/mcp`). API key: the user creates a new key |
| HTTP 403 `access_denied`, `ACCOUNT_UNAVAILABLE` | The account cannot use SciFig (banned or being deleted) | Tell the user; reconnecting does not help |
| `SCOPE_DENIED` | The credential lacks the specific required scope | Explain `details.required_scopes` and ask before requesting broader access. Each of the eight scopes is independent; uploads, job reads and balance reads are not implied |
| `PLAN_REQUIRED` | The option needs a higher plan | Name the option and point to https://scifig.ai/pricing; there is no `top_up_url` |
| `INSUFFICIENT_CREDITS` | Not enough credits | Share `details.top_up_url`; do not retry |
| `RATE_LIMITED`, `CONCURRENCY_LIMITED` | Too many requests or tasks | Wait for `Retry-After`, then retry with the same `idempotency_key` |

If the SciFig tools do not show up, check that the connector is enabled for this chat, then reconnect it. If sign-in keeps failing, the user can disconnect the app under Settings → API Keys → Connected apps and connect again.

Creation and FlowChart SVG conversion may return a queued or processing job. Keep the job ID and, only with `jobs:read`, poll `get_job`; never repeat creation or invent a new idempotency key because waiting stopped. Without that scope, explain the limitation and ask before expanding access. Use the installation commands documented in this repository; public GitHub availability does not imply acceptance into an assistant’s official directory.
