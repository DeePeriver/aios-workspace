# Tennis Radar — Targets

What the radar watches: specific competitor accounts, manual watch-list URLs, and forum sources. Edit this file directly to change what the radar pulls.

> **Owner edits this file.** Replace the example entries below with your real competitors. Keep the field names exactly as shown — the skill parses them.

---

## Competitors to track

One block per competitor. Skip fields you don't have. The skill uses every filled field.

- **Website** is the homepage — used by WebFetch for promo/launch detection. Fill this in for every competitor; it's the highest-signal field.
- **Blog / news URL** is optional but useful — a dedicated press, blog, or news index page. Fetched with WebFetch separately from the homepage.
- **YouTube channel** is the handle (e.g. `@solincosports`) — used by Supadata for channel stats and brand-search seeding.

### Solinco
- **Website:** https://solincosports.com
- **Blog / news URL:** https://solincosports.com/blogs/news
- **YouTube channel:** @solincosports
- **Notes:** Aggressive on poly strings. Big affiliate program.

### Luxilon
- **Website:** https://www.luxilon.com
- **Blog / news URL:**
- **YouTube channel:** @luxilonofficial
- **Notes:** Premium ATP-tier positioning. Owned by Wilson.

### {{competitor name}}
- **Website:**
- **Blog / news URL:**
- **YouTube channel:**
- **Notes:**

---

## Watch-list URLs (optional)

Specific URLs the radar should re-check on every run. Useful for tracking the trajectory of a single video, post, or article over time.

- {{URL}} — {{why you're watching it}}

---

## Forums and communities (web scrape)

URLs the radar scrapes for community sentiment about your brand and competitors. Each entry should be a search URL or thread index, not a homepage. Replace `{{your_brand}}` with your actual brand name in each URL.

These are scraped via Supadata `/web/scrape` (not WebFetch) — Reddit and Talk Tennis Warehouse render through JavaScript and Supadata handles that better.

- **Talk Tennis Warehouse:** https://tt.tennis-warehouse.com/index.php?search/&q={{your_brand}}
- **Reddit /r/tennis:** https://www.reddit.com/r/tennis/search/?q={{your_brand}}&sort=new&t=month
- **Reddit /r/10s:** https://www.reddit.com/r/10s/search/?q={{your_brand}}&sort=top&t=month

---

## Notes for owner

- **To add a competitor:** copy a block above, paste, fill in the fields you have. Save.
- **To remove a competitor:** delete their block. The skill will skip them on the next run.
- **Search queries** live in `default-queries.md` next to this file — they're niche-wide rather than per-competitor.
