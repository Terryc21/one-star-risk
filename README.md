# one-star-risk

A Claude Code skill that re-scores audit findings for **App Store one-star-review risk**.

It is a **judgment lens, not a detector.** Feed it findings you already have — a `git diff`, a
file, a TODO list, or another auditor's output — and it returns each one with a 🔴/🟡/🟢/⚪ band,
a **named, overridable trigger**, and a skeptic-pass result. It grounds those bands in *why apps
like yours actually get one star* (rarely the crash — it's "used to work / I paid for this / the
app told me X and it was wrong"), using real competitor reviews when available.

The whole design serves one constraint: **never let "the agent assigned 🔴" start reading as
"this IS 🔴."** Every band shows its work or gets demoted.

## Install

```sh
git clone https://github.com/Terryc21/one-star-risk.git
ln -s "$(pwd)/one-star-risk/skills/one-star-risk" ~/.claude/skills/one-star-risk
```

Then in Claude Code: ask to *"scope these findings for one-star risk"* / *"which of these
threaten my rating?"*, or invoke `/one-star-risk`.

## Grounding (free by default)

| Tier | Provider | Setup |
|---|---|---|
| Free default | `rss` — Apple's public customer-reviews feed | none |
| Paste fallback | user-supplied competitor reviews | paste them in |
| Paid (optional) | `applyra` (reference adapter) | `APPLYRA_API_KEY` in your env |
| None | ungrounded priors | — (degrades loudly) |

Every run **reports its grounding tier** and degrades loudly when there's no corpus. A paid
provider deepens the *evidence*, never the *rubric* — paying buys better-grounded scores, not
better scores.

## Maintenance / best-effort contract

Paid-provider adapters are **best-effort** and may break when a vendor changes their API. On
failure the skill **falls back to free App Store RSS** and keeps going — a degraded run, never a
broken skill. Adapter fixes are community-maintained; the provider seam (`providers/CONTRACT.md`)
lets a one-file PR fix a broken vendor without touching the core. PRs welcome.

## Layout

```
skills/one-star-risk/
  SKILL.md                 # the rubric, honesty contract, skeptic, procedure
  providers/
    CONTRACT.md            # provider interface + the 4 rules
    rss.md                 # free default adapter
    applyra.md             # reference paid adapter (key from env/keychain, never repo)
  reference/
    rubric.md              # provenance + worked examples (the 🔴 and the 🟡→🟢 demotion)
```

## License

MIT.
