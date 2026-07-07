---
name: publee
description: Publish HTML (a single page or a static file tree) to a shareable https://<slug>.publee.site URL via the Publee API. Use when the user wants to share an HTML file, prototype, report, or static site as a URL — optionally password-protected, with no build step or hosting setup.
---

# Publee — publish HTML to a shareable URL

Publee turns HTML into a limited-share URL (`https://<slug>.publee.site`). One
`POST` request, no account required. Use it when the user asks to "share this
HTML", "give me a URL for this page", or wants to hand a prototype/report to
someone else.

## Quick start (no auth)

```bash
curl -X POST https://publee.app/api/publish \
  -H "Content-Type: application/json" \
  --data '{
    "title": "My page",
    "visibility": "public",
    "html": "<!doctype html><html><body><h1>Hello</h1></body></html>"
  }'
```

Response (`201`):

```json
{
  "site": { "url": "https://<slug>.publee.site", "slug": "<slug>", "visibility": "public", "expiresAt": "..." },
  "claimToken": "…",
  "claimUrl": "https://publee.app/claim?token=…"
}
```

Give `site.url` (and the password, if any) to the user. Anonymous publishes
**expire after 7 days** — mention this, and also share `claimUrl`: opening it
after signing in transfers the site to the user's account (extending
retention). `claimToken`/`claimUrl` are only present on anonymous publishes.
With an API token (`Authorization: Bearer publee_live_...`), sites last longer
(free plan: 30 days, paid plans: persistent) and more visibility options
unlock.

## Parameters

| Field | Type | Notes |
|---|---|---|
| `html` | string | Full HTML document, published as `index.html`. Use this **or** `files`. |
| `files` | array | Multi-file sites. Each item: `{ path, content }` for text or `{ path, contentBase64, mimeType }` for binary. Must include a root `index.html` (a single root `.html` file is auto-renamed). |
| `title` | string | Defaults to the HTML `<title>`. |
| `description` | string | Optional. |
| `visibility` | string | `public` \| `password` (default) \| `private` \| `workspace` \| `members`. Anonymous callers may only use `public` or `password`. `private` needs the Pro plan; `workspace`/`members` need the Team plan. |
| `password` | string | Required when `visibility: "password"`. Min 6 chars. |
| `slug` | string | Custom subdomain, 3–63 chars, `[a-z0-9-]`. Random if omitted. **Paid plans (Pro+) only** — anonymous/free publishes always get a random slug. |
| `overwrite` | boolean | Republish to an existing `slug` you own (same URL, all files replaced). Requires a Bearer token. |
| `spaMode` | boolean | Serve `index.html` for unknown paths (client-side routing). |
| `noindex` | boolean | Default `true` (blocks search engines). Setting `false` needs a paid plan (Pro+). |
| `memberEmails` | string[] | Allowlist for `visibility: "members"` (Team plan). Members get an email notification with the site URL. |

`visibility` defaults to `password`, so **always pass `visibility` explicitly**:
either `"public"`, or `"password"` together with a `password`.

## Multi-file example

```bash
curl -X POST https://publee.app/api/publish \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer publee_live_..." \
  --data '{
    "title": "Docs site",
    "visibility": "password",
    "password": "secret123",
    "files": [
      { "path": "index.html", "content": "<!doctype html>..." },
      { "path": "css/style.css", "content": "body { ... }" },
      { "path": "img/logo.png", "contentBase64": "iVBORw0...", "mimeType": "image/png" }
    ]
  }'
```

## Limits

- Site size: 10MB anonymous (and unverified-email accounts), 25MB free plan,
  100MB paid plans. Max 1000 files.
- Free plan: up to 3 concurrently active sites per workspace.
- Slugs of expired/deleted sites are reserved for 90 days (not reusable).

## Errors

- `400` — validation (bad slug, missing `index.html`, short password, plan-gated
  feature like custom `slug` / `noindex: false` on free, …); message in
  `{ "error": "..." }`.
- `401` — invalid/expired token, or a visibility that needs auth.
- `415` — missing `Content-Type: application/json`.
- `429` — rate limited; wait and retry, don't loop.

## MCP server (optional)

Publee also exposes the same capability as a remote MCP server at
`https://publee.app/api/mcp` (tools: `publee_publish`, `publee_list_sites`).
For Claude Code:

```bash
claude mcp add --transport http publee https://publee.app/api/mcp \
  --header "Authorization: Bearer publee_live_..."
```

The header is optional; without it, publishes are anonymous (7-day expiry).

Publee also supports **OAuth login** for MCP clients (OAuth 2.1 with dynamic
client registration; discovery at
`https://publee.app/.well-known/oauth-authorization-server`). In clients with
OAuth support (claude.ai custom connectors, Claude Code `/mcp` → authenticate),
connect without a header and sign in via the browser — no API token needed.

Prefer the REST API from scripts and this skill's curl examples in agents that
already have shell access — no extra setup needed.

## Notes

- API tokens (`publee_live_...`) are created in the Publee dashboard at
  https://publee.app after signing in.
- Published content is served sandboxed from `*.publee.site` (CSP sandbox,
  `noindex` by default) — safe for sharing untrusted/generated HTML.
- To update a site instead of minting a new URL: pass the same `slug` plus
  `overwrite: true` (token required).
