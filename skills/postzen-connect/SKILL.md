---
name: postzen-connect
description: Connect a new social media account to PostZen (X, Instagram, TikTok, LinkedIn, Facebook, YouTube, Threads, Pinterest, Bluesky, Telegram) or review/disconnect existing connections.
argument-hint: <platform to connect, e.g. "pinterest">
---

# Connect a social account to PostZen

Walk the user through linking a social account so posts can target it, using the PostZen MCP tools provided by this plugin.

## Workflow

1. **Show current state.** Call `listAccounts` and summarize what's already connected (platform + username) so the user doesn't double-connect.
2. **Start the connection.** Call `createConnectUrl` with the `platform` and the `profileId` the account should belong to (from `listProfiles`; ask which if there are several). Give the user the returned `authUrl` as a clickable link and tell them to open it in their browser, sign in to the platform, and approve access — the link expires after 10 minutes. Do not try to complete the OAuth flow for them.
3. **Finish and verify.** PostZen's own OAuth callback completes the connection when the user approves in the browser; there is nothing for you to exchange. `completeConnect` exists only for integrations that receive the OAuth `code` on their own redirect URL, which the `createConnectUrl` flow never does — don't call it. After the user says they've approved, call `listAccounts` again. Check the resulting account's `status` — not just that it appears. Potential pitfall:
   - A `connected` status proves auth, not publishing. If the user is connecting because publishing failed, suggest a quick test post (a draft, or a real post they confirm) to exercise the publish path. Sometimes users will forget to approve all permissions requested by the platform's OAuth flow.
4. **Platform-specific follow-ups:**
   - **Pinterest**: after connecting, a default board should be selected — use `listPinterestBoardsForSelection` and `selectPinterestBoard` (or `createPinterestBoard` for a new one). Both take the `state` from `createConnectUrl`, which stays valid for board selection for 30 minutes from when the connect URL was created, so do this right after connecting.
   - **Bluesky**: uses an app password rather than OAuth — the `authUrl` is a PostZen-hosted page where the user enters their handle and an app password generated in their Bluesky settings; the connection completes when they submit it. Never ask the user to paste the app password into this chat.
   - **Telegram**: no OAuth either — the `authUrl` is a PostZen-hosted page that issues a short-lived access code. The user adds @PostZenScheduleBot as an administrator of their channel or group and sends that code to the bot; the connection completes when the bot receives it.

## Disconnecting

`disconnectAccount` removes a connection. Confirm with the user first — scheduled posts targeting that account will no longer be able to publish.

## Cautions

- Never ask for or handle the user's social media passwords, app passwords, or tokens directly; the browser-based connect flow is the only path.
- If a connection repeatedly fails, point the user to the PostZen dashboard (https://app.postzen.dev) where the same flow exists with more error detail.
