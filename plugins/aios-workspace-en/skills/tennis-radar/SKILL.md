---
name: tennis-radar
description: Generate a tennis-industry radar report — what's hot in tennis content, what competitors are doing, what people are saying about your brand. Pulls from YouTube (via Supadata), competitor websites (via WebFetch), and open-web news (via WebSearch). Reads prior reports to surface deltas. Use when the user says "run the tennis radar", "what's happening in tennis right now", "competitor scan", "industry report", "what are competitors doing", or "any news from <competitor>". Cadence is owner-chosen — works on any schedule.
---

# Tennis Radar

Produce a dated tennis-industry report that catches what the owner can't see from inside Shopify. Combines YouTube search, competitor-website fetches, and open-web search. Compares against the prior report to flag deltas.

## When to use

- Owner asks for a fresh radar/industry report
- Owner wants a focused scan on a single competitor or account
- Owner wants to know "what's new since last time" without specifying a source
- Anything that smells like "what's happening out there in tennis"

## Steps

### 1. Confirm scan mode

Ask the user (one question, three options):

- **Full scan** — every competitor and industry-wide queries from `references/targets.md`
- **Focus** — one competitor or one topic the owner names
- **Watch-list only** — just the manual URLs in `references/targets.md` "Watch-list" section

If the owner doesn't specify, default to **full scan**.

### 2. Verify setup

Before any API calls, run these checks against the **workspace root** (the directory the user runs Claude Code from — same directory as `context/` and `outputs/`):

#### 2a. Targets

Read `${CLAUDE_PLUGIN_ROOT}/skills/tennis-radar/references/targets.md`. It must have at least one competitor block with a `Website:` or `YouTube channel:` field filled in (not the `{{...}}` placeholder). If it's still all placeholders, open the file with the owner, walk through filling in at least 3 competitors, save, then continue.

This file lives inside the skill, not in `context/`. The skill owns its own runtime config; `context/` stays focused on strategic business knowledge.

#### 2b. `.env` file location and Supadata credential

The `.env` file lives at the workspace root and stores the Supadata API key. WebFetch and WebSearch are built-in Claude tools — no key needed. Format:

```
SUPADATA_API_KEY=supa_xxxxxxxxxxxxxxxx
```

For a non-technical owner, walk them through `${CLAUDE_PLUGIN_ROOT}/skills/tennis-radar/references/setup-walkthrough.md` — it's a hand-held, paced setup with celebration moments and a standalone smoke test. Don't paraphrase it; open it and follow it.

For a technical user who just wants the steps:

1. **Does `.env` exist at workspace root?** If not, create an empty one with `Write` (a single newline is fine).
2. **Read `.env`.** Check for `SUPADATA_API_KEY`. If missing, get a key at https://supadata.ai → Dashboard → API Keys → Create API Key (free tier, starts with `supa_`).
3. **Write the key safely** — preserve everything already in `.env`:
   - Use the `Edit` tool, not `Write`. The change is appending one line, never overwriting.
   - If the file ends without a trailing newline, add one before the new line.
   - If `SUPADATA_API_KEY` already exists with a different value, ask the owner before replacing — they may have a reason.
4. **Verify the value loads.** Run a smoke test:
   ```bash
   set -a && source .env && set +a && \
     curl -s -H "x-api-key: $SUPADATA_API_KEY" "https://api.supadata.ai/v1/me"
   ```
   Expect a JSON response with credit info. A 401 means the key is wrong; stop and ask the owner to re-paste.

#### 2c. Gitignore safety

The `.env` file holds secrets and should never reach git.

1. Check whether `.gitignore` exists at the workspace root.
2. If it doesn't exist, create one with `.env` as the only line.
3. If it exists, check whether `.env` (or a pattern matching it like `.env*`) is listed.
4. If not listed, append `.env` to it. Do not silently rewrite the whole file — use `Edit` to append.

Skip this if `.git/` doesn't exist at the workspace root (the user isn't using git for this workspace).

### 3. Read history

Look in `outputs/tennis-radar/`:

- The most recent dated report — read it end to end. Internalize what was reported, what flags were active, what followups were suggested.
- The previous report (one before the most recent) — skim for trend context.
- `_state/views-history.jsonl` if it exists — used in step 6 to detect persisting virality on YouTube videos.

If `outputs/tennis-radar/` doesn't exist, create it. This is the first run — note that explicitly when writing the report.

### 4. Build the query plan

Show the user what's about to run, before burning credits. One bullet per source:

```
About to scan:
- YouTube (Supadata): 7 industry searches + ~10 brand searches + transcripts for top 5 videos (~46 credits)
- Competitor websites (WebFetch): homepage + blog/news per competitor (~14 fetches, free)
- General news (WebSearch): 3-4 queries per competitor + 2 industry-wide (~25-30 queries, free)
- Forums (Supadata /web/scrape): 3 community URLs (~3 credits)

Estimated Supadata usage: ~49/100 free credits
WebFetch + WebSearch: free, built-in
```

Ask: "Run as planned, or want to adjust?" Owner can trim competitors, switch to focus mode, etc.

### 5. Run scans

Use the patterns in `references/api-reference.md`. Every bash call that hits Supadata must prepend `set -a && source .env && set +a` so the key loads in that shell — environment variables don't persist between Bash tool calls. WebFetch and WebSearch are tool calls, not bash — no env loading needed.

Order matters — Supadata is rate-limited (1 req/sec on free), so sleep 1.1s between calls.

**YouTube (Supadata)** — query plan combines industry-wide queries from `references/default-queries.md` with brand-specific queries built from each competitor in `references/targets.md` (e.g. `<brand> string review`):
1. For each query: `GET /youtube/search?query=<q>&uploadDate=week&sortBy=views&limit=10`
2. From results, dedupe video IDs across all queries (a single video may appear in multiple searches)
3. For top ~20 unique videos: `GET /youtube/video?id=<id>` for current view count
4. For top 5 by views: `GET /transcript?url=<url>&text=true` to summarize content
5. For each tracked competitor with a YouTube channel: `GET /youtube/channel?id=<handle>` for stats snapshot

**Competitor websites (WebFetch)** — for each competitor in `references/targets.md` with a `Website:` value:
1. WebFetch the homepage with a focused promo prompt (see `api-reference.md` "WebFetch" section for exact prompt patterns) — captures active sales, discount banners, "free shipping" callouts.
2. WebFetch the homepage again with a launch-detection prompt — captures new product callouts and "introducing" copy.
3. If the competitor has a `Blog / news URL:` filled in, WebFetch it with a recent-posts prompt — captures dated blog posts within the report window.
4. If a fetch fails (bot block, login wall, JS-only shell), note the URL in run notes and continue. Do not retry; do not block the report.

**General news (WebSearch)** — open-web discovery for things competitors don't put on their own sites, plus community sentiment from listed forums:
1. Per competitor: news + launches + promos + signings (4 queries — see `references/default-queries.md` for exact patterns).
2. Industry-wide (run once, not per competitor): tennis-stringing news, ATP/WTA sponsorship changes, equipment trends.
3. Read snippets directly. Only follow up with `WebFetch` on a result URL if the snippet is rich enough to be worth quoting but too thin to stand alone.
4. **Forums:** for each URL in `references/targets.md` "Forums and communities," call Supadata `GET /web/scrape?url=<url>` (1 credit each). Reddit and Talk Tennis Warehouse render through JavaScript, which is why they go through Supadata rather than WebFetch. Read the markdown for recurring themes; skip one-off comments.

If any call returns 401/402/429 or empty results due to quota, jump to the rate-limit handler (step 8). Otherwise continue.

### 6. Detect persisting virality

For every video pulled in step 5 that was posted >14 days ago, look up its previous view count in `_state/views-history.jsonl`:

- If current views > previous + 10% → flag as "persisting viral" with the delta
- Append the new snapshot to `_state/views-history.jsonl`: `{"video_id": "...", "title": "...", "views": N, "scan_date": "YYYY-MM-DD"}`

If no history exists yet (first or second run), skip the comparison and just record the current snapshot.

### 7. Synthesize and write the report

Use the structure in `references/report-template.md`. Save to `outputs/tennis-radar/YYYY-MM-DD.md`.

Section-specific rules:

- **Headline** (section 1) — lead with anything that explains revenue movement (a competitor's active promo, a viral video, a sentiment spike). If no obvious driver, lead with the strongest content opportunity.
- **What's hot** (section 2) — cluster YouTube videos by theme; one cluster shouldn't be three rows of "X reviews Y string."
- **Competitor activity** (section 3) — combine WebFetch findings (their site says X) with WebSearch findings (third parties say Y). Promo flags come straight from the WebFetch prompts.
- **Persisting virality** (section 4) — only include items with >10% view growth since last report. If none, say so.
- **What people are saying** (section 5) — pull from WebSearch snippets and forum scrapes. Keep to recurring themes, not one-off comments.
- **Deltas** (section 6) — explicit comparison to the most recent prior report. Reference it by date.
- **Signals** (section 7) — concrete and actionable. "Solinco running 15% off through May 12" beats "competitors active."

### 8. Rate-limit handling (graceful degradation)

If Supadata returns a quota or auth error mid-run:

1. **Don't fail the whole report.** Continue with the data already collected. WebFetch and WebSearch have no quota, so the report still has competitor-website and open-web findings even if YouTube data is partial.
2. Note the missing source clearly in the report's "Run notes" section: "Skipped: Supadata YouTube (free tier exhausted at 100 credits — sign in to supadata.ai to upgrade)."
3. After saving the report, surface the upgrade message to the user in chat:

```
⚠️ Hit the Supadata free-tier limit during this run.

What this means: YouTube data is partial — I pulled what I could before credits ran out. Competitor-website fetches and open-web searches still completed (those are free).
To run a full scan next time, upgrade here: https://supadata.ai/pricing
  - Supadata Basic: $5/mo for 300 credits

The report I just saved still has everything I could pull.
```

4. Save the report regardless. Mark its `Sources used:` line accurately.

### 9. Update state and report back

After writing the report:

1. Append the run's view snapshots to `_state/views-history.jsonl`.
2. Tell the user the report path and a one-line summary of the headline finding.
3. Suggest the obvious followup — if a content angle is strong, suggest `/aios-workspace-en:plan content "<idea>"`. If a sentiment theme is recurring across reports, suggest `/aios-workspace-en:brainstorm <theme>`.

## When to pause and check with the user

- Setup is incomplete (missing Supadata key, empty `references/targets.md`) — stop, walk through `references/setup-walkthrough.md`, then resume.
- The query plan in step 4 will obviously bust the free tier (>14 brands, weekly cadence) — show the projected usage and ask before running.
- A rate-limit hits mid-run — finish what you can, surface the upgrade prompt clearly, don't auto-retry.
- The report's headline reflects something high-stakes (a competitor's major launch, a sentiment crisis) — surface this conversationally, not just buried in the report.

## Don't

- Don't run scans without showing the query plan first. Owners need to see what's about to cost credits.
- Don't write the report without reading at least the most recent prior report. Deltas are the whole point of the bi-weekly cadence.
- Don't dump raw API responses or full WebFetch markdown into the report. Synthesize. Owner reads the report on a Monday morning, not at a debugging session.
- Don't pad sections with empty bullets. If "Persisting virality" found nothing, write one sentence saying so and move on.
- Don't auto-upgrade the user's plan or store credit-card info — only suggest upgrades with links.
- Don't retry failed WebFetch calls more than once. Bot-blocked sites stay blocked; note in run notes and continue.
