# Provider: rss (free default — zero setup)

The public path. Apple publishes a customer-reviews RSS/JSON feed per app, per country, with
**no key and no account.** This is the floor everyone gets. It is shallow — recent reviews,
capped depth, per country — and must be labeled as such.

## Endpoint

```
https://itunes.apple.com/{country}/rss/customerreviews/page={N}/id={appStoreID}/sortby=mostrecent/json
```

- `{country}` — two-letter store country (`us`, `gb`, `de`, …). Loop a few to widen coverage.
- `{appStoreID}` — the numeric App Store ID of a competitor (or the user's own app).
- `page={N}` — paginate `1..K`. Apple caps this feed; expect ~10 entries/page and a low page
  ceiling. Do **not** imply you fetched "all" reviews.

Fetch with WebFetch (or `curl -s`). Parse JSON: `feed.entry[]` → each has `im:rating.label`
(the star count), `title.label`, `content.label` (body), and `author`/`updated` for date.
The first `entry` is app metadata, not a review — skip it.

## Returns (per CONTRACT.md)

```
corpus:  [ { rating: Int(im:rating), title, body, country, date } ]   # 1-star + low-star most useful
tier:    "rss"
depth:   { count: <reviews fetched>, countries: [<looped>], dateRange: <min..max date seen> }
caveats: [ "Recent reviews only — Apple's RSS feed is capped and most-recent-sorted.",
           "Not exhaustive; older / all-time themes are not represented.",
           "Per-country; coverage limited to the countries looped." ]
```

## How to use the corpus

Mine the **1-star and 2-star** entries for *recurring felt reasons* — "stopped syncing,"
"paid and lost the feature," "told me X, it was wrong," "used to work." These themes sharpen
which of the user's findings map to a real grievance. You are not counting stars; you are
learning *why this app class gets one star.*

## Failure mode (fail soft)

If the feed 404s, returns empty, or the network is down: return an empty `corpus` with
`tier: "rss"`, `depth.count: 0`, and a caveat *"RSS feed returned no reviews."* The skill then
either uses a user-pasted corpus or degrades to "no grounding" — it never aborts.

## Honesty

This is the shallow floor. Always label output "recent themes, not exhaustive." Never let an
RSS-grounded run imply it surveyed the app's whole review history.
