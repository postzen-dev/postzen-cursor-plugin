# Changelog

All notable changes to this plugin will be documented here.

## 1.2.1 — skill corrections

Factual fixes to the four skills, verified against the MCP tool schemas. No packaging changes.

- `queue`: `updateQueueSlot` replaces a queue's whole slot list (it does not move one slot), and `deleteQueueSlot` without `queueId` deletes every queue on the profile. The skill now reads the queue, edits the list, and sends it back with `queueId`; deleting a queue requires an explicit `queueId` and the user's confirmation by name. Placed posts keep their times instead of "redistributing". `previewQueue` returns free upcoming slots only; queued posts come from `listPosts` with `status: "queued"`. Profiles have no timezone, so the skill no longer refers to one.
- `analytics`: `getDailyMetrics` aggregates post metrics by publish or received date; it is not account-level data. Added the `attribution` modes and the UTC/`post_count` caveats for `getBestTimeToPost`.
- `post`: `scheduledFor` accepts any ISO 8601 offset, not only UTC. Documented the `queuedFromProfile` + `queueId` mode as the fourth creation mode, the mode combinations that return 400, and the 100 MB / direct-URL rule for external media.
- `connect`: PostZen's own OAuth callback completes the connection; `completeConnect` is not part of this flow. Added `profileId` and the 10-minute expiry to the start step, the 30-minute Pinterest board-selection window, and Telegram's access-code flow alongside Bluesky's app-password page.

## 1.2.0 — OpenAI Codex plugin

- Added the OpenAI Codex plugin (`postzen-dev/postzen-codex-plugin`): same hosted MCP server and four skills, packaged in the `.codex-plugin/plugin.json` format. No changes for other hosts.

## 1.1.0 — generated from the unified source repository

- The plugin is now generated from `postzen-dev/postzen-plugins`, with no functional changes.
- The Claude Code plugin version jumps from 0.1.0 to align with the other hosts.

## 1.0.0 — initial release

- Added the `postzen` MCP server pointing at `https://mcp.postzen.dev/mcp` (OAuth 2.1 with dynamic client registration — no API key or client ID to configure).
- Added four skills: `postzen-post`, `postzen-queue`, `postzen-analytics`, `postzen-connect`.
- Logo: PostZen's official mark.
