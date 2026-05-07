# Default Tennis Search Queries

Niche-wide search seeds and brand-query templates the radar uses on every run. Edit this file to add or remove queries — the radar reads it directly.

Per-competitor data (websites, blog URLs, watch-list, forums) lives in `targets.md` next to this file.

## Industry-broad (YouTube via Supadata)

- `tennis stringing`
- `tennis string review`
- `tennis racquet review`
- `racquet stringing tutorial`
- `tennis string comparison`
- `ATP racquet`
- `WTA racquet`

## Brand-specific (YouTube via Supadata)

For each brand listed under "Competitors to track" in `targets.md`, run:

- `<brand> string review`
- `<brand> string vs`

Skip brand searches for any competitor without a `YouTube channel:` field set — channel-based discovery is more reliable than name search for those.

## Promotion / sale detection (WebSearch)

Run per competitor. The first two are the highest-signal; add the third when the owner suspects discounting is moving the market.

- `"<brand>" tennis promo OR sale OR discount 2026`
- `"<brand>" coupon OR "free shipping" OR "limited time"`
- `"<brand>" tennis "% off" OR clearance`

These complement WebFetch on the competitor's homepage — WebSearch catches third-party coverage and image-only banners that WebFetch's markdown view misses.

## News / launches / partnerships (WebSearch)

Run per competitor:

- `"<brand>" tennis news 2026`
- `"<brand>" launches OR announces OR partnership`
- `"<brand>" signs OR sponsors OR ambassador tennis`

## Industry-wide news (WebSearch)

Run once per radar, not per competitor:

- `tennis stringing industry news 2026`
- `ATP racquet sponsorship change 2026`
- `tennis equipment trends 2026`

## Sentiment / community (Supadata `/web/scrape`)

URLs for these come from `targets.md` under "Forums and communities."
