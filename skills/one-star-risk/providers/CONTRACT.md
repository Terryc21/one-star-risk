# Provider Contract

A **provider** grounds the one-star lens in real reviews. The skill talks to "a provider,"
never to a named vendor — so the lens never looks like one vendor's ad, never ties its fate to
one API, and survives any single vendor changing or dying.

## The interface

Every provider file (`providers/<name>.md`) must document how to produce:

```
ground(competitorAppIDs, opts) → {
  corpus:  [ { rating, title, body, country, date } ],   # the reviews (may be empty)
  tier:    "applyra" | "rss" | "paste" | "none",          # which provider grounded
  depth:   { count, countries, dateRange },               # how deep — for the honesty line
  caveats: [ "recent themes, not exhaustive", ... ]       # what NOT to over-claim
}
```

- `corpus` may be empty; an empty corpus is a valid (degraded) result, not an error.
- `tier`, `depth`, and `caveats` feed the **grounding line** the skill prints every run. A
  provider that returns a corpus without honest depth/caveats violates the contract.

## The four rules every provider must obey

1. **Keys come from the environment or OS keychain — never from a file in this repo.** Free
   users who don't configure a key simply fall through to RSS. No key ever lands in git.
2. **Fail soft, never hard.** Any error (API changed, key missing, network down, rate limit) →
   the provider reports failure and the skill falls to the next tier (RSS, then paste, then
   none). A provider must never abort the run.
3. **Deepen evidence, never change the rubric.** A provider returns a *corpus*; it does not
   assign or influence bands. Two providers over the same app must not move a finding's band —
   only the *confidence/grounding* behind it.
4. **Report depth honestly.** `depth` and `caveats` must reflect what was actually fetched
   (recency, country coverage, cap), so the skill can label the floor as shallow when it is.

## Adding a vendor

Drop a new `providers/<vendor>.md` documenting the four-block return above, the auth source
(env var / keychain entry name), and the failure mode. Wire `--provider=<vendor>`. That's it —
the core never imports anything vendor-specific, so a new adapter touches no other file.

Adapter fixes are community-maintained and best-effort: a paid vendor changing its API breaks
*that adapter only*, the skill falls back to free RSS, and a one-file PR fixes it. See the
"Maintenance" note in the repo README.
