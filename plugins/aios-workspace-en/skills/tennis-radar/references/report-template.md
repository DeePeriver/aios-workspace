# Tennis Radar Report Template

The structure every report follows. Keep sections in this order so deltas across reports are easy to read side-by-side.

```markdown
# Tennis radar — {{YYYY-MM-DD}}

**Period covered:** {{previous report date}} → {{today}}
**Scan mode:** {{full / focus on <competitor> / watch-list only}}
**Sources used:** {{YouTube via Supadata + competitor websites via WebFetch + open-web via WebSearch + forums via Supadata web/scrape}} (note any skipped due to rate limits)

---

## 1. Headline

Two or three sentences. The single most important thing the owner should know from this report. Lead with anything that explains a sales/conversion movement (competitor promo, viral post, news event). If nothing obvious, lead with the strongest content signal.

---

## 2. What's hot in tennis (last {{N}} days)

The top 5–10 videos across the industry, ranked by views. For each:

- **Title** — channel, views, posted X days ago
- One sentence on what's resonating and why

Pull from YouTube search results. Cluster by theme where possible.

---

## 3. Competitor activity

One subsection per competitor in `references/targets.md`. Skip competitors with no movement.

### {{Competitor name}}
- **YouTube:** {{N new uploads, top: "<title>" views}}
- **Website (WebFetch):** {{any new product, banner copy change, hero callout}}
- **Open-web news (WebSearch):** {{press, partnerships, athlete signings, third-party reviews}}
- **Promo flag:** ⚠️ {{describe if any active discount or sale detected, with end date if known — combine WebFetch homepage scan + WebSearch promo queries}}

---

## 4. Persisting virality

Posts/videos posted >14 days ago that are still gaining views relative to the last report. This is the "we keep missing this" signal.

For each:
- **{{title}}** — {{competitor}} — posted {{N}} days ago — +{{Δ}} views since last report

If none, write "No persisting virality detected this cycle."

---

## 5. What people are saying

Sentiment + recurring themes from WebSearch snippets and forum scrapes. Two sub-sections:

**About {{your brand}}:** {{sentiment count, recurring praise, recurring complaints}}
**About competitors:** {{any pattern affecting how the market thinks of competitors — durability complaints, hype, comparisons}}

---

## 6. Deltas vs. last report

Compare to the previous dated file in `outputs/tennis-radar/`. Three buckets:

- **Accelerating:** themes/competitors getting more attention than last cycle
- **Fading:** themes/competitors that dominated last cycle but went quiet
- **New:** anything that wasn't on the radar last cycle

If this is the first report, write "First report — no prior data to compare against."

---

## 7. Signals for {{your brand}}

The "so what" section. Concrete actions the report suggests:

- ⚠️ Defensive flags (competitor moves likely affecting conversion)
- 💡 Content angles (topics resonating that fit our positioning)
- 📊 Watchlist additions (new competitors, accounts, or topics worth tracking next time)

---

## 8. Followups

- {{suggested plan: /aios-workspace-en:plan content "<idea>"}}
- {{suggested brainstorm: /aios-workspace-en:brainstorm <theme>}}
- {{any URL worth manually re-checking before next run}}

---

## Run notes

- API usage: Supadata {{N}}/100 credits this run
- Skipped: {{list any source that hit rate limits or returned empty — including any WebFetch URLs that bot-blocked}}
- Time to generate: {{N}} minutes
```

---

## Where the report lives

`outputs/tennis-radar/YYYY-MM-DD.md`

State files (skill-managed, owner doesn't edit):

- `outputs/tennis-radar/_state/views-history.jsonl` — append-only log of each video's view count per scan, used to detect persisting virality
