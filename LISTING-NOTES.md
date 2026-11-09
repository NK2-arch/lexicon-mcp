# Listing notes (draft — not submitted)

Checked against public docs and Taras visibility research (`/workspace/lexicon-listings/taras-visibility-hacks-20261109.md`). **Nothing in this folder has been published.**

## Suggested public GitHub repo name

`lexicon-mcp` (or `lexicon-seo-mcp`).

## Directory checklist

### 1. LobeHub MCP Market — https://market.lobehub.com/s/publish-mcp

- **Needs:** public GitHub repo you own or can push to; Node.js ≥ 22; `lhm.plugin.json` (this repo includes one with `cloudEndpoint` + tools).
- **Publish path:** human browser for `lhm login` and `lhm github connect`, then `lhm plugin publish <repo-url> --dir .`.
- **Could not confirm live:** market.lobehub.com returned **403** to the automated fetch used for this draft; schema taken from secondary write-ups and a public `lhm.plugin.json` example (identifier / name / version / cloudEndpoint / tools). Re-read the official skill before Nick approves publish: https://lobehub.com/publish-mcp/skill.md
- **Risk:** org-owned GitHub repos have had CLI claim/push-access issues in the past; verify with Nick’s intended GitHub owner account.

### 2. Vercel skills.sh — https://www.skills.sh/

- **Needs:** public repo with `SKILL.md` (`name` + `description`; name matches parent directory). This repo: `skills/lexicon-seo/SKILL.md`.
- **Listing:** no submit form — discovery follows `npx skills add <owner>/<repo>` install telemetry.
- **Gap after repo is public:** someone must run one install with telemetry for the directory page to appear. Confirm whether a minimum install count exists (not documented).

### 3. Cursor Directory — https://cursor.directory/plugins/new

- **Needs:** public GitHub repo; form auto-scans for `.mcp.json` / `mcp.json`, `skills/*/SKILL.md`, etc.
- **This repo:** `.mcp.json` (Cursor-shaped URL + `${LEXICON_API_KEY}`), `skills/lexicon-seo/SKILL.md`, plus `.cursor-plugin/plugin.json` for the Cursor Plugin shape.
- **Gap:** submit form requires human sign-in; official Cursor Marketplace publish is a separate flow (`cursor.com/marketplace/publish`) and was not required by Taras’s Cursor Directory row.
- **Auth note:** Agent Plugins 1.0.0 forbids secrets in portable `mcp.json` headers; Cursor Plugins allow `${VAR}` via `variables` in `.cursor-plugin/plugin.json` (`cursor-mcp.json`).

### 4. ClawHub — https://clawhub.ai/

- **Needs:** folder with `SKILL.md`; CLI `clawhub login` + `clawhub skill publish`.
- **This repo:** `skills/lexicon-seo/` is the publishable skill folder.
- **License note (confirmed):** ClawHub publishes skills under **MIT-0** regardless of repo LICENSE. Document external Lexicon paid-plan cost in the skill (done).
- **Gap:** GitHub importer only imports repos owned by the signed-in GitHub account (not third-party).

### 5. Hermes Agent marketplace — https://hermes-agent.nousresearch.com/docs/developer-guide/plugins/catalog-submission

- **Needs:** public repo; portable `plugin.json` + `mcp.json` and/or skills; catalog PR adding `plugin-catalog/<name>.yaml` to `NousResearch/hermes-agent` with a **full 40-char commit SHA**; `hermes plugins validate` green; human maintainer review; install/test evidence on the PR.
- **This repo:** `plugin.json` (with Hermes `trust: untrusted` for spend-capable tools), Agent Plugins `mcp.json` (URL only), `skills/lexicon-seo/SKILL.md`.
- **Auth gap:** Agent Plugins 1.0.0 says remote headers must not contain credentials and defines no portable credential-reference fields — auth stays **client-managed**. Hermes users must attach Bearer auth in their Hermes MCP client config (not committed here). Confirm the exact Hermes `config.yaml` / UI steps for header auth before the catalog PR.
- **Not included:** `server.json` for the Official MCP Registry (separate Taras row; Lexicon queue holds it for namespace/DNS/CLI cost). Not required by the five directories above.

## Docs vs Worker source (minor)

Sources: https://lexiconseo.com/docs/mcp/ and `/workspace/lexicon-site/site/src/mcp/tools.ts` (+ `keys.ts` / `plans.ts`).

| Topic | Docs page | Worker source | Draft choice |
|---|---|---|---|
| Tool list | 12 tools, short blurbs | Same 12 names; richer descriptions + JSON schemas | Prefer **source** schemas in tables / `lhm.plugin.json` |
| `list_issues` severity | critical, warning, info | Also accepts P0 / P1 / P2 aliases | Document both (source) |
| `get_report` | “Markdown or JSON” | `format` required enum | Document required `format` |
| `get_fix` | issue code | Optional `report_id` for affected URLs | Document optional `report_id` |
| `refresh_page` | allowance + confirm | Also: 30-day per-page reuse; Business section draft | Document source extras |
| Rate limits | 30 / 60 / 120 | `PLANS.*.requestsPerMinute` matches | Same |
| Max active keys | not mentioned | `MAX_ACTIVE_KEYS = 5` | Omitted from README (internal ops detail; not on public MCP docs page) |
| Tagline | site chrome | `PRODUCT_TAGLINE = "Words matter."` | Used with period |

No secrets, account IDs, or internal Worker URLs were copied into this draft.
