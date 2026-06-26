# Provider: appfigures (paid — EXAMPLE STUB, unverified)

⚠️ **This is an unverified example adapter.** It exists to prove the provider seam is real — that
plugging in a *second* paid vendor is a one-file change and the skill plays no favorites. The
author has **not** run it against a live Appfigures account, so confirm the auth scheme and field
mapping below against [Appfigures' current API docs](https://docs.appfigures.com/) before relying
on it. If you wire it up and correct it, a PR is welcome.

Appfigures is *one* ASO/review service among several (Applyra, App Radar, AppFollow, Sensor Tower,
…). Naming it here is not an endorsement — it's a worked example of the contract. Use whichever
vendor you already pay for, or none (the free `rss` provider is the default).

## Why Appfigures is a reasonable example

Of the paid services, Appfigures has the most developer-friendly *documented* REST API and an
indie-priced tier — it ties reviews to revenue/downloads, which is its real differentiator. The
review endpoint and fields below are taken from its public docs.

## Access (confirm in your account — HTTPS only)

- **Base URL:** `https://api.appfigures.com/v2/`  (HTTP is rejected; HTTPS only.)
- **Reviews endpoint:** `GET /reviews`
- **Identify the app(s):** `products=<product_id>[,<product_id>...]` (Appfigures product IDs, not
  raw App Store IDs — resolve via their products/search route first).
- **Useful filters:** `countries`, `stars`, `start` / `end` (date range), `count`, `page`, `sort`.

## Auth (key never in this repo)

Appfigures supports two schemes — **verify which your use needs:**
- **HTTP auth with a Client Key** — for your *own* account's data.
- **OAuth 1.0** — required for public-facing apps and for data on apps you don't own.

Create credentials at `appfigures.com/developers/keys` ("My API Keys"). Read the key/secret from
the **environment or OS keychain at call time** (e.g. `APPFIGURES_CLIENT_KEY`), **never from a
file in this repo. Never print it.** If no credential is found, this provider fails soft and the
skill falls back to `rss`.

**Access caveat that matters here:** the **Public Data API add-on** is required to read reviews for
products **not owned by your account** — i.e. competitor reviews, which is exactly this skill's
grounding use. Free accounts must use the Public Data API even for their own apps. Confirm your
plan includes it before assuming competitor grounding will work.

## Returns (per CONTRACT.md) — map Appfigures fields → corpus shape

Appfigures review object → corpus item:
- `stars`  → `rating`
- `title`  → `title`
- `review` → `body`
- `iso`    → `country`  (Google Play returns `"ZZ"`)
- `date`   → `date`

```
corpus:  [ { rating: <stars>, title, body: <review>, country: <iso>, date } ]
tier:    "appfigures"
depth:   { count, countries, dateRange }
caveats: [ "Unverified example adapter — confirm auth scheme + field mapping against live data.",
           "Competitor reviews need the Public Data API add-on; verify your plan.",
           "Paid corpus deepens evidence; it does not change the rubric or any band." ]
```

## Failure mode (fail soft — HARD requirement)

Any error — missing/invalid credential, missing Public Data API add-on, OAuth not set up, API
shape changed, rate limit, network, or simply "this stub was never finished" — must be caught.
Print:

> *Appfigures grounding failed (example adapter is unverified, credential not set, or Public Data
> API add-on missing); falling back to free App Store RSS.*

…and continue with `rss`. A broken or incomplete paid adapter is a **degraded run, never a broken
skill.**

## Honesty

Report the tier truthfully, including that it's unverified until someone confirms it. Do not let a
deeper corpus tempt band inflation — the three-part test is identical regardless of provider.
Paying buys better-grounded scores, not better scores.
