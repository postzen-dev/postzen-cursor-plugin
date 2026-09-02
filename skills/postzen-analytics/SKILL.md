---
name: postzen-analytics
description: Build a social media analytics report from PostZen — post performance, follower growth, daily metrics, and best times to post across connected accounts. Use when the user asks how their posts or accounts are performing.
argument-hint: <platforms and time range, e.g. "LinkedIn, last 30 days">
---

# PostZen analytics report

Produce a readable performance summary for the user's connected social accounts using the PostZen MCP tools provided by this plugin.

## Workflow

1. **Scope it.** Call `listAccounts` to see connected accounts. If the user didn't specify platforms or a time range, default to all accounts over the last 7 days and say so in the report.
2. **Pull the data** (parallel calls where independent):
   - `getAnalytics` — per-post engagement metrics for the period.
   - `getDailyMetrics` — day-by-day account-level metrics (impressions, engagement).
   - `getFollowerStats` — follower counts and growth.
   - `listPosts` — recently published posts, to tie metrics back to actual content.
   - Optionally `syncExternalPosts` first if the user posts outside PostZen and wants those included.
3. **Synthesize, don't dump.** Lead with the headline (best-performing post, follower trend direction, notable spikes or drops). Then a short per-platform breakdown. Quote actual numbers with their period. Call out anything actionable — e.g. a content type that consistently outperforms, or a platform going stale.
4. **Close with timing guidance** if relevant: `getBestTimeToPost` gives data-driven posting-time suggestions per account; mention the top slots.

## Notes

- Some platforms gate analytics behind extra permission scopes; if a tool returns an authorization/scope error for one account, note it in the report ("analytics not enabled for this account — reconnect it in the PostZen dashboard to grant analytics access") and continue with the rest rather than failing the whole report.
- Metrics are read-only — this skill never publishes or modifies anything.
