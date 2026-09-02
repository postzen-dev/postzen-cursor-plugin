# PostZen

Cursor plugin that connects agents to [PostZen](https://www.postzen.dev) through PostZen's hosted [Model Context Protocol](https://modelcontextprotocol.io/) server at `https://mcp.postzen.dev/mcp`.

Schedule and publish social media posts across 10 platforms — X (Twitter), Instagram, TikTok, LinkedIn, Facebook, YouTube, Threads, Pinterest, Bluesky, and Telegram — without leaving Cursor. The plugin bundles the MCP server plus workflow skills for drafting and adapting content per platform, uploading media, filling posting queues, connecting accounts, and building analytics reports.

## Install

1. Open **Cursor Settings → Plugins**.
2. Search for **PostZen**.
3. Click **Install**, then complete the PostZen sign-in when prompted. Your browser opens the PostZen dashboard, where you approve access and choose which profiles to expose.

Or run `/add-plugin postzen` in chat.

Don't have a PostZen account? Sign up at [postzen.dev](https://www.postzen.dev) and connect your social accounts in the [dashboard](https://app.postzen.dev) — or let the agent walk you through it with the `postzen-connect` skill.

## MCP

```json
{
  "mcpServers": {
    "postzen": {
      "type": "http",
      "url": "https://mcp.postzen.dev/mcp"
    }
  }
}
```

Auth is OAuth 2.1 and the client registers itself, so Cursor just prompts for PostZen sign-in when the plugin connects — there is no client ID or API key to configure. The resulting token carries a PostZen API key scoped to the profiles you approved; revoke it at any time from the PostZen dashboard.

For headless or CI use, skip OAuth and configure the server yourself with an API key (create one in the dashboard under Settings → API keys):

```json
{
  "mcpServers": {
    "postzen": {
      "type": "http",
      "url": "https://mcp.postzen.dev/mcp",
      "headers": { "Authorization": "Bearer pzn_your_api_key" }
    }
  }
}
```

## Skills

| Skill | What it does |
| --- | --- |
| `postzen-post` | Draft, adapt, and publish or schedule a post across platforms, with media uploads, queue slots, and best-time-to-post suggestions |
| `postzen-queue` | Set up recurring posting slots (at your best-performing times), preview upcoming posts, and fill the queue |
| `postzen-analytics` | A readable analytics summary: post performance, follower growth, daily metrics |
| `postzen-connect` | Guided flow to link a new social account |

## What agents can do

The MCP server exposes 44 tools. The main groups:

| Category | Capabilities |
| --- | --- |
| Posts | Create, list, get, update, and delete posts; publish now, schedule, save drafts, or queue; presigned media uploads |
| Queues | List, create, update, and delete recurring slots; preview the queue; find the next free slot |
| Accounts | List connected accounts, start and complete a platform connection, disconnect, manage Pinterest boards |
| Analytics | Per-post analytics, post timelines, daily metrics, follower stats, best time to post, sync externally published posts |
| Profiles, API keys, webhooks | Manage profiles, API keys, and webhook subscriptions and deliveries |

The hosted server is the source of truth for tool names and schemas.

## Example prompts

```text
Post this launch announcement to X and LinkedIn tomorrow at 9am PT
```

```text
Take this blog post and turn it into a thread for X and a LinkedIn post, save both as drafts
```

```text
How did my posts perform this week?
```

```text
Add these three posts to my queue
```

## Notes

- Publishing is an outward-facing action: the skills always show you the final per-platform content and timing and ask for confirmation before anything goes live. Drafts never require confirmation.
- The skills never ask for social media passwords or tokens — account connections always go through the platform's own browser-based flow.
- The plugin connects only to `https://mcp.postzen.dev/mcp`, which proxies tool calls to the PostZen API at `https://api.postzen.dev`. Media uploads go to the presigned upload URL returned by the `createMediaPresign` tool. No hooks, no shell commands, no local code execution.
- Streamable HTTP is the only supported transport (`/sse` exists for legacy clients).

## Docs

- PostZen: https://www.postzen.dev
- Dashboard: https://app.postzen.dev
- API docs: https://docs.postzen.dev
- Agent quickstart: https://www.postzen.dev/agent-quickstart.md
- Status: https://status.postzen.dev
- Server URL: https://mcp.postzen.dev/mcp

Logo is PostZen's official mark.

## License

MIT
