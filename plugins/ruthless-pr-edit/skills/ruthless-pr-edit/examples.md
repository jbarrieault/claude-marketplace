# Examples

Read these before drafting. They carry the taste.

---

## 1. The thorough one

Nothing here is false. Almost none of it is needed.

**Before** — *Refactor: Implement comprehensive caching layer for organization lookups*

> ## Summary
>
> This PR implements a comprehensive caching layer for organization lookups in order to
> improve performance. Currently, every request performs a database query to fetch the
> organization, which is inefficient at scale.
>
> ## Changes
>
> - Added a new `RedisCache` wrapper class in `app/lib/redis_cache.rb`
> - Modified `OrgLookup#find` to check the cache before querying Postgres
> - Added a `cache_key` private method that builds a key from org id and `updated_at`
> - Set a TTL of 5 minutes (configurable via `ORG_CACHE_TTL`)
> - Added cache invalidation on the `Organization` model's `after_commit` hook
> - Updated `spec/lib/redis_cache_spec.rb` with 12 new test cases
>
> ## How it works
>
> When a request comes in, `OrgLookup#find` first checks Redis using the generated cache
> key. On a hit, it deserializes and returns. On a miss, it falls through to Postgres and
> writes the result back...
>
> ## Testing
>
> All existing tests pass. Added new unit tests for the cache wrapper. Manually verified
> locally.
>
> ## Future work
>
> We could potentially extend this pattern to other lookups.

**After** — *Cache org lookups in Redis*

> ## Summary
>
> Org lookups hit Redis instead of Postgres, with a five-minute TTL and invalidation when
> the org changes.

Everything cut was either in the diff, in the commits, or in nobody's interest. The TTL
and the invalidation survived because they are the two things a reviewer would ask about
and cannot see at a glance — and they fold into the one sentence rather than earning a
Details section.

---

## 2. When something earns its way back

Reduction first. Then one sentence returns, because a reviewer would be wrong without it.

**Before** — *Add tenant_id to messages table and backfill*

> ## Summary
>
> This PR adds a `tenant_id` column to the `messages` table so that we can scope messages
> per tenant, which is a prerequisite for the multi-tenancy work described in ENG-4471.
>
> ## Details
>
> The migration adds the column as nullable with no default so that it doesn't lock the
> table. A separate backfill task iterates in batches of 1000 with a sleep between
> batches. Once the backfill completes we'll add the NOT NULL constraint in a follow-up.
> The column is indexed. Note that `messages` is our largest table (~400M rows) so the
> backfill will take roughly six hours to run.
>
> ## Changes
>
> - `db/migrate/20260901_add_tenant_id_to_messages.rb`
> - `lib/tasks/backfill_message_tenant_id.rake`
> - Index added concurrently
>
> ## Testing
>
> Ran the migration and backfill against a staging snapshot.

**After** — *Add tenant_id to messages*

> ## Summary
>
> Messages can be scoped per tenant (ENG-4471).
>
> ## Details
>
> The column ships nullable; a follow-up adds the constraint once the backfill finishes.
> Deploy this before running the backfill — it takes about six hours against production's
> 400M rows.

The batch size, the sleep, the concurrent index, the file list: all *how*, all in the
diff and the commits. Details exists here, and only here, because a reviewer who misses
the ordering approves a change that behaves differently than they think.

---

## 3. When reduction reveals emptiness

Ruthless editing is not "make it shorter." Sometimes the passes expose a body that never
said anything, and the fix is a real sentence, not a shorter empty one.

**Before** — *Fix bug*

> Fixes the bug where things weren't working. See ticket.

**After** — *Stop dropping webhook retries after a 500*

> ## Summary
>
> Failed webhook deliveries were marked complete instead of requeued, so any delivery
> that hit a 500 was silently lost (ENG-4502).

Barely longer. It now tells an uninformed reader what changed and why it mattered. No
Details section, because there is nothing a reviewer would get wrong without one.

---

## Titles

| Before | After |
| --- | --- |
| `[WIP] Various fixes and improvements to the sync service` | `Retry sync on transient S3 errors` |
| `ENG-4471` | `Add tenant_id to messages` |
| `refactor(auth): update the way we handle token refresh logic` | `Refresh auth tokens before expiry, not after` |
| `Update dependencies` | `Upgrade Sidekiq 6 → 7` |
| `This PR adds support for exporting reports as CSV files` | `Export reports as CSV` |
