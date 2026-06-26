# Provider: appfollow (paid — EXAMPLE STUB, unverified)

⚠️ **This is an unverified example adapter.** It exists to prove the provider seam is real — that
plugging in a *second* paid vendor is a one-file change and the skill plays no favorites. The
author has **not** tested it against a live AppFollow account, so treat the endpoint/field details
below as a starting point to confirm against [AppFollow's current API docs](https://appfollow.io),
not as verified fact. If you wire it up and correct it, a PR is welcome.

AppFollow is *one* ASO/review service among several (App Radar, Sensor Tower, MobileAction,
Applyra, …). Naming it here is not an endorsement — it's a worked example of the contract. Use
whichever vendor you already pay for, or none (the free `rss` provider is the default).

## Auth (key never in this repo)

Read the key from the **environment or OS keychain at call time**, never from a file in the repo:

- Environment variable `APPFOLLOW_API_KEY` (preferred, public-friendly).
- Or an OS-keychain entry you look up at launch.

Confirm the exact header AppFollow expects from their live docs (commonly an `X-AppFollow-API-Key`
or bearer-style header — **verify, don't assume**). **Never print the key.** If no key is found,
this provider fails soft and the skill falls back to `rss`.

## Access

Confirm the current base URL and review endpoint from AppFollow's API reference. The shape this
adapter needs is "fetch recent reviews for an app, filterable by rating and country." Map their
response into the corpus shape below.

## Returns (per CONTRACT.md)

```
corpus:  [ { rating, title, body, country, date } ]
tier:    "appfollow"
depth:   { count, countries, dateRange }
caveats: [ "Unverified example adapter — confirm field mapping against live AppFollow data.",
           "Paid corpus deepens evidence; it does not change the rubric or any band." ]
```

## Failure mode (fail soft — HARD requirement)

Any error — missing/invalid key, API shape changed, rate limit, network, or simply "this stub was
never finished" — must be caught. Print:

> *AppFollow grounding failed (example adapter is unverified, or key not set); falling back to free
> App Store RSS.*

…and continue with `rss`. A broken or incomplete paid adapter is a **degraded run, never a broken
skill.**

## Honesty

Report the tier truthfully, including that it's unverified until someone confirms it. Do not let a
deeper corpus tempt band inflation — the three-part test is identical regardless of provider.
Paying buys better-grounded scores, not better scores.
