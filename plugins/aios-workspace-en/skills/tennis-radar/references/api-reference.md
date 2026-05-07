# API Reference — Supadata + WebFetch + WebSearch

Everything the `tennis-radar` skill needs to call its three sources. Keep this doc current when API surfaces change.

The radar uses one paid API (Supadata, free tier sufficient) plus two of Claude's built-in tools (WebFetch, WebSearch — no key, no quota).

---

## Loading credentials from `.env`

The Supadata API key lives in a single `.env` file at the **workspace root** (the directory the user runs Claude Code from — same directory as `context/`, `outputs/`, etc.).

The Bash tool runs each command in a fresh shell, so environment variables don't persist between calls. Every bash command that hits the Supadata API must load `.env` first.

**Canonical loading pattern** — prepend this to any bash command that uses `$SUPADATA_API_KEY`:

```bash
set -a && source .env && set +a
```

Together with a curl call:

```bash
set -a && source .env && set +a && \
  curl -s -H "x-api-key: $SUPADATA_API_KEY" "https://api.supadata.ai/v1/me"
```

For multiple calls in one bash invocation, load once at the top:

```bash
set -a && source .env && set +a
for q in "tennis stringing" "racquet review"; do
  curl -s -G -H "x-api-key: $SUPADATA_API_KEY" \
    --data-urlencode "query=$q" \
    --data-urlencode "uploadDate=week" \
    --data-urlencode "sortBy=views" \
    "https://api.supadata.ai/v1/youtube/search"
  sleep 1.1
done
```

**Why `curl -G --data-urlencode`** — it handles URL encoding cleanly without depending on `jq` or `python`. Spaces, ampersands, and reserved characters all work correctly.

**Expected `.env` format** (one key per line, no quotes around the value):

```
SUPADATA_API_KEY=supa_xxxxxxxxxxxxxxxx
```

If `.env` is missing or the key isn't set, the curl call returns 401. The skill's setup step (SKILL.md step 2) handles this — never invoke an API call before that step has confirmed the key is loadable. For a non-technical owner walkthrough, see `setup-walkthrough.md` next to this file.

---

## Supadata (YouTube + forum scraping)

**Base URL:** `https://api.supadata.ai/v1`
**Auth header:** `x-api-key: $SUPADATA_API_KEY`
**Free tier:** 100 credits/month, 1 request/second
**Sign-up:** https://supadata.ai → Dashboard → API Keys

### Cost model

1 credit per call for: search page (~20 results), video/channel/playlist metadata, transcript, web scrape, account info. Generated transcripts (no native captions) cost 2 credits/min.

### Endpoints used by the radar

| Endpoint | Purpose | Cost |
|---|---|---|
| `GET /youtube/search?query=...&uploadDate=week&sortBy=views` | Find recent tennis videos by keyword | 1/page |
| `GET /youtube/video?id=...` | Title, views, duration, channel, posted date | 1 |
| `GET /youtube/channel?id=...` | Subscribers, total views, video count | 1 |
| `GET /youtube/channel/videos?id=...&limit=10` | List a channel's recent videos | 1 |
| `GET /transcript?url=...&text=true` | Plain-text transcript (YouTube/TikTok/X/FB) | 1 |
| `GET /web/scrape?url=...` | Scrape a forum/community page to clean markdown | 1 |
| `GET /me` | Check remaining credits | 1 |

`/web/scrape` is used **only** for forums and community pages listed in `targets.md` under "Forums and communities" — Reddit and Talk Tennis Warehouse render through JavaScript and need Supadata's rendering. For competitor-owned pages, use WebFetch (see below) — it's free and good enough for static markup.

### Rate-limit handling

- **HTTP 401** → key is wrong or missing. Stop and ask user to check `.env`.
- **HTTP 402** → free tier exhausted or out of credits. Surface the upgrade prompt (see `SKILL.md` rate-limit section).
- **HTTP 429** → too fast (free tier is 1 req/sec). Sleep 1.2s and retry once. If it happens twice, surface as a transient error and continue without that data point.

### Calling pattern

Always prepend `set -a && source .env && set +a` (see Loading credentials section). Single call:

```bash
set -a && source .env && set +a && \
  curl -s -G -H "x-api-key: $SUPADATA_API_KEY" \
  --data-urlencode "query=tennis stringing" \
  --data-urlencode "uploadDate=week" \
  --data-urlencode "sortBy=views" \
  "https://api.supadata.ai/v1/youtube/search"
```

Batched calls (sleep 1.1s between them on the free tier — 1 req/sec limit):

```bash
set -a && source .env && set +a
for q in "tennis stringing" "racquet review" "string comparison"; do
  curl -s -G -H "x-api-key: $SUPADATA_API_KEY" \
    --data-urlencode "query=$q" \
    --data-urlencode "uploadDate=week" \
    --data-urlencode "sortBy=views" \
    "https://api.supadata.ai/v1/youtube/search"
  sleep 1.1
done
```

Transcript / forum-scrape follow the same pattern — replace the path and params:

```bash
set -a && source .env && set +a && \
  curl -s -G -H "x-api-key: $SUPADATA_API_KEY" \
  --data-urlencode "url=https://youtube.com/watch?v=VIDEO_ID" \
  --data-urlencode "text=true" \
  "https://api.supadata.ai/v1/transcript"
```

---

## WebFetch (competitor-owned URLs)

Claude's built-in `WebFetch` tool. Used for **deterministic** lookups: pages we already know belong to a competitor — homepage, blog, news/press page. Good for catching active promos, new product launches, banner copy changes.

**Cost:** Free, built-in.
**Quota:** None from the radar's side.
**Auth:** None.

### What it's for in the radar

For each competitor in `targets.md`:

- **Website** (homepage) — fetch and scan the resulting markdown for promo keywords (`% off`, `sale`, `discount`, `free shipping`, `bundle`, `clearance`) and product launch language (`new`, `introducing`, `now available`, `pre-order`).
- **Blog / news URL** (if filled in) — fetch and look for any post dated within the report window. Note title + summary in the report's competitor-activity section.

### Tool signature

`WebFetch(url, prompt)` — fetches the URL, converts to markdown, then runs `prompt` over the content and returns the answer. The prompt is what makes WebFetch useful: ask focused questions instead of returning full-page dumps.

### Usage patterns

**Promo / sale detection** (per competitor homepage):

```
WebFetch(
  url="https://solincosports.com",
  prompt="Is there a sale, promo, discount, or limited-time offer mentioned on this page? If yes, quote the exact wording and any end date. If no, say 'no active promo detected'."
)
```

**New product detection** (per competitor homepage or product page):

```
WebFetch(
  url="https://www.luxilon.com",
  prompt="List any products described as new, recently launched, just released, or pre-order. For each, give the product name and one-sentence positioning."
)
```

**Blog / news scan** (per competitor blog URL, if listed):

```
WebFetch(
  url="https://example-tennis-brand.com/blog",
  prompt="List blog posts dated within the last 14 days. For each: title, date, one-line summary."
)
```

### Limitations (note in run notes when they bite)

- **Bot-blocked sites** return errors or login walls. Major brands (Solinco, Wilson, Babolat) are typically fine; smaller competitors may fail. If a fetch fails, note it in the report's run notes and continue.
- **Banner-image-only promos** are invisible to WebFetch — markdown conversion strips images. A site with "20% OFF" only as a hero image will read as "no active promo detected." Acceptable false negative for v1.
- **JavaScript-only sites** may return an empty shell. If a fetch returns no meaningful content from a site you know is active, fall back to Supadata `/web/scrape` (1 credit) for that URL.

---

## WebSearch (open-web discovery)

Claude's built-in `WebSearch` tool. Used for **discovery** — finding pages we don't already have URLs for. Good for press coverage, athlete signings, partnerships, podcast appearances, third-party reviews, and competitor mentions across the open web.

**Cost:** Free, built-in.
**Quota:** None from the radar's side.
**Auth:** None.
**Recency:** Indexed results; expect 1–2 day lag behind real-time. Acceptable for bi-weekly cadence.

### What it's for in the radar

For each competitor in `targets.md`, run focused queries for: news mentions, product launches, partnerships/signings, sentiment in third-party coverage. Plus a few industry-wide queries to catch movement that doesn't tie to a specific competitor.

### Tool signature

`WebSearch(query)` — returns ranked search results with title, URL, and snippet. The radar reads the snippets directly; only fetch a result with WebFetch if the snippet is interesting and you need the full article.

### Usage patterns

**Per-competitor news + launches**:

```
WebSearch(query='"Solinco" tennis news 2026')
WebSearch(query='"Solinco" launches OR announces OR partnership')
```

**Per-competitor promo / sale detection** (catches third-party coverage of promos that WebFetch missed because they were image-only):

```
WebSearch(query='"Solinco" promo OR sale OR discount OR coupon 2026')
```

**Per-competitor athlete / sponsorship signings**:

```
WebSearch(query='"Solinco" signs OR sponsors OR ambassador tennis')
```

**Industry-wide** (run once per radar, not per competitor):

```
WebSearch(query="tennis stringing industry news 2026")
WebSearch(query="ATP racquet sponsorship change 2026")
```

### Reading results

Each result has `title`, `url`, `snippet`. The radar:

1. Skim snippets for anything matching report criteria (promo, launch, signing, news).
2. If a snippet is rich enough, quote it directly in the report and cite the URL.
3. If a snippet looks promising but is too thin, follow up with `WebFetch` on that URL for the full article.
4. Drop irrelevant results silently — don't pad the report with noise.

### Limitations (note in run notes when relevant)

- **1–2 day indexing lag.** A competitor's announcement from yesterday may not show up yet. If results look suspiciously stale (top result is months old for a "news" query), flag it in run notes.
- **Branded queries are noisy** when a brand name is also a common word. Quote the name (`"Solinco"`) and add `tennis` to the query to filter.
- **No date filter from the tool itself** — rely on putting the year in the query and on snippet text to filter recency.

---

## Quick credit budget per run (defaults)

Sized so a single run fits comfortably in the Supadata free tier.

| Source | Operations | Cost |
|---|---|---|
| Supadata YouTube searches (industry seeds + brand seeds) | ~14 | 14 credits |
| Supadata video metadata (top results, deduped) | ~20 | 20 credits |
| Supadata transcripts (top 5 to summarize) | 5 | 5 credits |
| Supadata channel stats (per competitor) | up to 7 | 7 credits |
| Supadata `/web/scrape` (forum/community URLs) | up to 3 | 3 credits |
| WebFetch (homepage + blog/news per competitor) | ~14 | $0 |
| WebSearch (3–4 queries per competitor + 2 industry) | ~25–30 | $0 |
| **Total** | | **~49 Supadata credits** |

**Supadata-only, fits under the 100-credit free tier.** Bi-weekly cadence at this size: ~98 credits/month, just under the free tier ceiling.

If `references/targets.md` lists significantly more than 7 competitors, the run will exceed the free Supadata tier and trip the upgrade prompt (see SKILL.md rate-limit handling).
