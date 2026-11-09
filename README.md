# Lexicon SEO MCP

**Words matter.**

Public documentation for [Lexicon SEO](https://lexiconseo.com)’s hosted [Model Context Protocol](https://modelcontextprotocol.io) server. Lexicon runs technical SEO and AI-visibility audits. This repository does **not** ship a local server binary: clients connect to the hosted endpoint.

| | |
|---|---|
| Product | https://lexiconseo.com |
| MCP docs | https://lexiconseo.com/docs/mcp/ |
| Pricing | https://lexiconseo.com/pricing/ |
| Support | support@lexiconseo.com |
| Endpoint | `https://lexiconseo.com/mcp` |

## What you get

With a **Starter**, **Professional**, or **Business** account, Claude Code, Cursor, or Claude Desktop can:

- Start an audit of a public site
- Read the stored report (Markdown or JSON)
- List issues and pull fix steps
- Recheck one public page for a single issue
- Read monitoring diffs, citation checks, and content-refresh drafts

Tools use the **same monthly audit allowance and page cap** as the Lexicon dashboard. A Free account receives an upgrade error until you move to a paid plan.

The score in a report is a **Lexicon heuristic**, not a Google score. Fix steps match the Markdown export from the dashboard.

## Authentication

1. Sign in and open [Settings](https://lexiconseo.com/login/?next=/app/settings/).
2. Create an API key. Copy it when it is shown.
3. Lexicon stores a **hash** of the key, not the key.
4. Send `Authorization: Bearer YOUR_API_KEY` on every request.
5. Treat the key like a password. Do **not** commit it.

### Rate limits (per API key)

| Plan | Requests per minute |
|---|---|
| Starter | 30 |
| Professional | 60 |
| Business | 120 |

A request past the limit is refused until the next minute (`429`).

Paid tools that spend credits (`start_audit` after the monthly allowance, `extra_citation`, `refresh_page` after the allowance) quote a price first. Send `confirm: true` only when you intend to spend.

## Setup

Replace `YOUR_API_KEY` with the key from Settings, or put the key in the environment as documented below.

### Claude Code

```bash
claude mcp add --transport http lexicon https://lexiconseo.com/mcp --header "Authorization: Bearer YOUR_API_KEY"
```

Or add this to `.mcp.json`. Claude Code also accepts `Bearer ${LEXICON_API_KEY}` when the key is in the environment:

```json
{
  "mcpServers": {
    "lexicon": {
      "type": "http",
      "url": "https://lexiconseo.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Cursor

Put this in `.cursor/mcp.json` in a project, or in `~/.cursor/mcp.json` for every project. Cursor also accepts `Bearer ${env:LEXICON_API_KEY}`:

```json
{
  "mcpServers": {
    "lexicon": {
      "url": "https://lexiconseo.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Claude Desktop

Open **Customize → Connectors → Add custom connector**. Use URL `https://lexiconseo.com/mcp`. Choose no sign-in. Under Request headers, set the name to `authorization` and the value to `Bearer YOUR_API_KEY` (including the word Bearer and the space). Request headers are in beta.

If that section is missing, add this to `claude_desktop_config.json` (on macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`). It uses the `mcp-remote` bridge:

```json
{
  "mcpServers": {
    "lexicon": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://lexiconseo.com/mcp",
        "--header",
        "Authorization: Bearer YOUR_API_KEY"
      ]
    }
  }
}
```

## Tools

| Tool | Purpose | Parameters |
|---|---|---|
| `list_sites` | Saved monitoring sites and sites this account has audited | _(none)_ |
| `start_audit` | Start a read-only audit of one public site. Uses one audit from this month’s allowance. After that, send `confirm: true` to spend the quoted credits. | `url` (required), `confirm` (optional boolean) |
| `get_audit_status` | Status, pages crawled, and score | `id` (required) |
| `get_report` | Stored report as Markdown or JSON | `id` (required), `format`: `markdown` \| `json` (required) |
| `list_issues` | Issues on a report, grouped by code | `report_id` (required), `severity` optional: `critical`, `warning`, `info`, or `P0` / `P1` / `P2` |
| `get_fix` | Fix steps for one issue code (same text as the Markdown export) | `issue` (required), `report_id` (optional) |
| `verify_fix` | Fetch one public page and say whether that issue is still present. Does not use an audit. Site-wide codes are refused. | `url` (required), `issue_code` (required) |
| `get_monitoring` | Latest saved-site diff and regressions | `site` (required; id, host, or URL) |
| `get_citations` | Citation questions stored on a report. Does not run a new search. | `report_id` (required) |
| `extra_citation` | One more citation question after the plan allowance. First call quotes the price; `confirm: true` spends credits. | `report_id` (required), `confirm` (optional) |
| `refresh_page` | Draft title, meta description, and H1 for one page in a finished audit. Business also drafts one section. Uses one content refresh from the monthly allowance; after that, `confirm: true` spends credits. Does not publish. Same page is not drafted again for 30 days. | `report_id` (required), `url` (required), `confirm` (optional) |
| `get_refresh` | Read a stored content refresh draft. Does not call the model. Omit `url` to list drafts on the report. | `report_id` (required), `url` (optional) |

Canonical tool descriptions and schemas live on the live server (`tools/list`) and are mirrored in `lhm.plugin.json`.

## Agent skill

This repo includes an Agent Skill at [`skills/lexicon-seo/SKILL.md`](skills/lexicon-seo/SKILL.md) so coding agents know when and how to call the Lexicon tools after the MCP server is connected.

Install via skills.sh (after this repository is public):

```bash
npx skills add NK2-arch/lexicon-mcp
```

## Manifests in this repo

| File | For |
|---|---|
| `lhm.plugin.json` | [LobeHub MCP Market](https://market.lobehub.com/s/publish-mcp) (`cloudEndpoint` + tools) |
| `skills/lexicon-seo/SKILL.md` | [skills.sh](https://www.skills.sh/), [ClawHub](https://clawhub.ai/), Cursor skills |
| `.mcp.json` / `cursor-mcp.json` | [Cursor Directory](https://cursor.directory/plugins/new) / Cursor Plugin variable wiring |
| `mcp.json` + `plugin.json` | Agent Plugins / [Hermes Agent](https://hermes-agent.nousresearch.com/) portable package (URL only; auth is client-managed per Agent Plugins 1.0.0) |
| `.cursor-plugin/plugin.json` | Cursor Plugin manifest (`LEXICON_API_KEY` variable) |

See [`LISTING-NOTES.md`](LISTING-NOTES.md) for directory-specific publish steps and gaps.

## License

MIT — see [LICENSE](LICENSE). Copyright (c) 2026 Lexicon SEO.

Product usage remains subject to Lexicon’s terms at https://lexiconseo.com/terms/ and plan limits at https://lexiconseo.com/pricing/.
