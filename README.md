# Publee Agent Skills

[Agent Skills](https://github.com/vercel-labs/skills) for [Publee](https://publee.app) —
turn HTML into limited-share URLs (`https://<slug>.publee.site`).

## Install

### As a Claude Code plugin (skill + MCP server)

Inside Claude Code:

```
/plugin marketplace add publee-dev/skills
/plugin install publee@publee
```

This installs the `publee` skill together with the Publee MCP server
(`https://publee.app/api/mcp`, OAuth login — no API token required).

### As a standalone skill

```bash
npx skills add publee-dev/skills
```

Works with Claude Code, Cursor, Codex, and any agent supported by the `skills` CLI.

## Skills

| Skill | Description |
|---|---|
| [`publee`](skills/publee/SKILL.md) | Publish HTML (single page or static file tree) to a shareable URL via the Publee API. Supports password protection, custom slugs, and republishing to the same URL. |
| [`shop-homepage`](skills/shop-homepage/SKILL.md) | Build a homepage for a small local business (restaurant, salon, classroom, …) from a short interview, then publish it with Publee. |

## What is Publee?

Publee publishes HTML with one `POST` request — no build, no hosting setup, no
account required. Anonymous publishes expire in 7 days; sign in at
[publee.app](https://publee.app) for longer-lived sites, private visibility, and
API tokens.

## License

MIT
