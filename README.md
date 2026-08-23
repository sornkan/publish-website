# publish-website

Agent skill for [SORNKan](https://sornkan.com) static hosting.

Installs as a portable `SKILL.md`. It tells an agent how to connect to the public MCP at `https://publish.sornkan.com/mcp`, show a 60-minute preview, then claim the site for **$1 USDC / 30 days** on Base via x402.

This repository contains **instructions only**. It does not include the hosting worker, wallets, or secrets.

[![skills.sh](https://skills.sh/sornkan/publish-website)](https://skills.sh/sornkan/publish-website)

## Install

```bash
npx skills add sornkan/publish-website
```

That command is what `find-skills` / `npx skills find` look up. The same skill is also published at:

https://sornkan.com/.well-known/agent-skills/publish-website/SKILL.md

## Connect the MCP

```bash
claude mcp add --transport http publish https://publish.sornkan.com/mcp
grok mcp add --transport http publish https://publish.sornkan.com/mcp
openclaw mcp add publish --url https://publish.sornkan.com/mcp --transport streamable-http
```

Paying over x402 works only when the agent already has its own wallet. This skill never holds a private key.

## Flow

1. Ask the human first.
2. `upload_url` — PUT zip (max 3MB). The upload link expires in **15 minutes**.
3. `inspect` — preview URL live for **60 minutes**. Not owned yet. No charge yet.
4. Human opens the preview.
5. `claim` — $1 USDC for 30 days, only after they say it is theirs.

## What is not in this repo

- The Cloudflare Worker that serves sites
- API tokens, wallets, or payer keys
- The rest of the SORNKan product

Support: contact@sornkan.com
