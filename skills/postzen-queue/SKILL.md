---
name: postzen-queue
description: Set up and manage PostZen posting queues — recurring time slots that posts fill automatically. Use when the user wants to create, review, or reorganize their posting schedule, see upcoming slots, or post at their best-performing times.
argument-hint: <days/times to add, or "show my queue">
---

# Manage PostZen queues

Help the user build and maintain a posting queue using the PostZen MCP tools provided by this plugin.

A queue is a set of recurring weekly time slots (e.g. Mon/Wed/Fri at 9:00) tied to a profile and timezone. Posts added "to the queue" are scheduled into the next open slot automatically.

## Workflow

1. **Show current state.** Call `listQueueSlots` and summarize the existing schedule as a compact weekly view (day + time + timezone). If empty, say so and move to setup.
2. **Setting up or changing slots:**
   - If the user names days/times, create them with `createQueueSlot`. Confirm the timezone — use the user's profile timezone unless they specify one, and echo it back ("9:00 AM America/Edmonton").
   - If the user asks for "good times" or doesn't specify, call `getBestTimeToPost` for their connected accounts and propose a concrete slot set built from the top suggestions (e.g. 3 slots/week to start). Create the slots only after they approve the proposal.
   - Use `updateQueueSlot` to move a slot and `deleteQueueSlot` to remove one. Deleting a slot doesn't delete posts — but confirm before deleting, since queued posts will redistribute to the remaining slots.
3. **Review what's coming.** `previewQueue` shows how queued posts map onto upcoming slots — use it after any slot change so the user sees the effect, and whenever they ask "what's going out this week?" (combine with `listPosts` filtered to scheduled/queued for the full picture).
4. **Filling the queue.** Queue a post by calling `createPost` with `queuedFromProfile` (plus `queueId` for a non-default queue) instead of `scheduledFor` — PostZen assigns the next free slot atomically and returns it as the post's `scheduledFor`. Queuing several posts in sequence lands them in consecutive slots automatically. Never fetch `getNextQueueSlot` and pass its time back as `scheduledFor`: the slot isn't reserved by that call, so a concurrent create can take it and the post ends up scheduled outside the queue. `getNextQueueSlot` is for *telling the user* when their next slot is, nothing more.

## Notes

- Slots are per-profile. If the user has multiple profiles, confirm which one before creating or deleting anything.
- Keep proposals modest: a new queue with 3–5 slots a week is easier to sustain than a dense one; the user can always add more.
- All times shown to the user should include the timezone; silent UTC is how posts end up publishing at 3 AM.
