# Provider: applyra (paid — deeper corpus, optional)

Applyra is one paid ASO provider, used here as the **reference paid adapter** — proof the
provider seam works with a real key-gated vendor. It is in no way required; free users never
touch it. A paid corpus is deeper and older than RSS, which makes bands *better grounded* — it
does **not** change the rubric or move a band by itself (CONTRACT rule 3).

## Auth (key never in this repo)

`X-API-Key` header (NOT `Authorization: Bearer` — that returns `MISSING_API_KEY`). The key is
read from the **environment or OS keychain at call time**, never from a file in the repo:

- Preferred (public): environment variable `APPLYRA_API_KEY`.
- macOS keychain (single-user convenience):
  `security find-generic-password -s "applyra-api-key" -a "<account>" -w`

**Never print the key.** If neither source yields a key → this provider "fails soft" and the
skill falls back to RSS (see Failure mode).

## Access

Two paths, whichever the user has wired:

- **Applyra MCP** (if connected this session): tools `mcp__applyra__*` — e.g.
  `list_applications`, `add_application`, and the review/score surfaces. Prefer MCP when present.
- **REST**, base `https://www.applyra.io/api/v1` (the `www` matters; apex 307-redirects, and
  `api.applyra.io` has no DNS). Relevant endpoints: `/applications` (GET/POST),
  `/applications/{id}/scores/history`, plus review surfaces per the live API reference.

POST payload gotchas (if registering an app to ground against): field is `app_id` (snake_case;
the validation error misleadingly says `appId`), `store` is `"ITUNES"` or `"GPLAY"`, `lang` is
a full locale like `"en-US"`, country like `"us"`.

## Returns (per CONTRACT.md)

```
corpus:  [ { rating, title, body, country, date } ]   # deeper + older than RSS
tier:    "applyra"
depth:   { count: <reviews>, countries: [...], dateRange: <wider than RSS> }
caveats: [ "Paid corpus deepens evidence; it does not change the rubric or any band." ]
```

## Failure mode (fail soft — HARD requirement)

Any error — missing key, auth failure, API shape changed, rate limit, network — must be caught.
Print exactly:

> *Applyra grounding failed (provider API may have changed or key not set); falling back to free
> App Store RSS.*

…and continue with the `rss` provider. A broken paid adapter is a **degraded run, never a broken
skill.** This single behavior is what keeps "the skill is broken" bug reports from landing on a
third-party API change — it converts them into "paid path degraded" notices.

## Honesty

Report the deeper tier truthfully (count, date range). Do **not** let the deeper corpus tempt
band inflation — the three-part test is identical regardless of provider. Paying buys
better-grounded scores, not better scores.
