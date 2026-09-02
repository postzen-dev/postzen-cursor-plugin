---
name: postzen-post
description: Create, schedule, or queue a social media post with PostZen. Use when the user wants to post, schedule, cross-post, or draft content for X (Twitter), Instagram, TikTok, LinkedIn, Facebook, YouTube, Threads, Pinterest, Bluesky, or Telegram.
argument-hint: <what to post, which platforms, and when>
---

# Post with PostZen

Publish or schedule the user's content using the PostZen MCP tools provided by this plugin.

## Workflow

1. **Resolve accounts.** Call `listAccounts` to get connected accounts. Each account has an `_id` (the `accountId` you must reference), a `platform`, and a `username`. If the user named platforms, match them; if ambiguous (multiple accounts on one platform), ask which. If a requested platform has no connected account, tell the user and offer to connect it (see the `postzen-connect` skill / `createConnectUrl`).
2. **Adapt the content per platform.** Don't blast identical text everywhere unless asked:
   - **X**: 280 chars for standard accounts — trim or thread-style tighten. Hashtags sparingly.
   - **LinkedIn**: longer-form is fine; professional tone; line breaks improve readability.
   - **Instagram / TikTok / YouTube / Pinterest**: media is required — a text-only post cannot publish there. If the user gave no media, either drop the platform (confirm first) or save as draft.
   - **Pinterest**: pins need a board; the account's default board is used unless the user picks one (`getPinterestBoards` / `listPinterestBoardsForSelection`).
   - **Bluesky**: 300 chars.
   - Platform value strings: `x` (alias `twitter`), `instagram`, `tiktok`, `linkedin`, `facebook`, `youtube`, `threads`, `pinterest`, `bluesky`, `telegram`.
3. **Handle media.** For local files, call `createMediaPresign`, upload the file with an HTTP `PUT` to the returned `uploadUrl`, then reference the returned `publicUrl` in the post's `mediaItems`. Already-hosted public URLs can be referenced directly.
4. **Choose the timing — exactly one of:**
   - `publishNow: true` — publishes immediately.
   - `scheduledFor` — ISO-8601 UTC, at least 60 seconds in the future. If the user gave a local time, convert to UTC and confirm the conversion.
   - `isDraft: true` — saves without publishing.
   - Queue: if the user says "add to queue" or gives no time preference for a scheduled post, pass `queuedFromProfile` (the profile id) on `createPost` instead of `scheduledFor` — PostZen claims the next free slot atomically and returns it. Never fetch `getNextQueueSlot` and pass its time as `scheduledFor` (the slot isn't reserved, so it can race); use it only to tell the user when their post would go out. `getBestTimeToPost` suggests data-driven times for non-queue scheduling.
5. **Confirm before publishing.** Show the final per-platform content and timing and get an explicit yes before calling `createPost` with `publishNow` or a near-term `scheduledFor`. Drafts don't need confirmation.
6. **Create the post** with `createPost`: `content`, `platforms: [{ platform, accountId }, ...]`, optional `mediaItems`, and the timing field. Report back the post `_id`, status, and scheduled time.

## Cautions

- Publishing is a real, outward-facing action. Never call `createPost` with `publishNow: true` without the user's explicit confirmation in this conversation.
- Never invent `accountId` values — always take them from `listAccounts`.
- If a `createPost` call fails, report the actual error; don't retry blindly (retries can double-post if the first attempt landed).
