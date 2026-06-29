# one-star-risk

A Claude Code skill that answers one question before you ship an app:
**which of your known issues could actually cost you a one-star review?**

You already have a list of things that aren't perfect — a `git diff`, a `TODO` list, notes
from another code review. Most of them no user will ever care about. A few could tank your
App Store rating. This skill sorts them: it tags each issue with how much it threatens your
rating, and — this is the important part — **explains its reasoning for every tag, so you can
disagree.**

```
🔴 HIGH   Sync only copies 21% of a user's data   → they see most of their stuff vanish on a
                                                      second device. "It broke." People review over this.
🟢 LOW    "Add warranty" takes two taps not one    → mild annoyance, nobody one-stars over it.
⚪ NONE    Internal helper has a confusing name     → users never see it.
```

It works because it's grounded in *why apps actually get one star* — which is rarely the
crash. It's the **"it used to work / I paid for this / the app told me something that wasn't
true"** feeling. When you point it at competitor App Store reviews, it learns that app's real
complaints and scores against them; with no reviews to learn from, it says so plainly and
treats its scores as guesses.

It does **not** find bugs — you feed it issues you already have, and it triages them. And it is
deliberately built to *talk itself down*: a scary-looking tag that it can't justify in one
sentence gets its risk lowered, so the top of the list is trustworthy rather than alarmist.

## A note on what these ratings are

The bands are a **reasoned opinion, not a measurement.** There is no score under the hood — each
tag comes from a judgment call (does a real user notice this, feel wronged, and act on it?),
sharpened against competitor reviews when some are available. Two honest people could disagree
on any single band, and the skill is built to invite exactly that: every tag names the one reason
it survives, so you can overrule it.

Treat the output as a **prioritized starting point for your own judgment**, not a verdict. When a
run learns only from recent App Store reviews — or from no reviews at all — it leans more
speculative still, and says so in its opening line. Less to learn from means lower confidence in
the bands; the skill surfaces that rather than hiding it.

## Before you run it

This skill **triages a list you already have — it does not generate one.** So you need
something to feed it first. That "something" is any list of issues, for example:

- the output of a code-review or audit skill you just ran (this is the main use),
- a `git diff` of changes you're about to ship,
- a plain TODO / backlog file, or
- issues you just describe in chat.

If you run `/one-star-risk` with nothing to score, it has nothing to do. The usual order is:
**run an auditor → hand its findings to this skill → get them ranked by review risk.** Think of
it as the second step, not the first.

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
| Paid (optional) | **any ASO review API you already pay for** | set its key in your env |
| None | ungrounded priors | — (degrades loudly) |

**No vendor is endorsed or required.** The skill talks to a *provider interface*
([`providers/CONTRACT.md`](skills/one-star-risk/providers/CONTRACT.md)), not to any one company.
The free Apple RSS feed is the default and needs nothing. If you happen to subscribe to an ASO
service (Appfigures, App Radar, AppFollow, Sensor Tower, MobileAction, Applyra, …), you can plug it
in as a deeper review source. Two paid adapters ship as **examples** so the seam is real, not
theoretical — [`applyra`](skills/one-star-risk/providers/applyra.md) (verified by the author) and
[`appfigures`](skills/one-star-risk/providers/appfigures.md) (an unverified stub showing how to add
your own). Adding a different vendor is a one-file adapter; see the contract.

Every run **reports its grounding tier** and degrades loudly when there are no reviews to learn
from. A paid provider deepens the *evidence*, never the scoring method — paying buys
better-grounded scores, not better scores. The skill does not recommend buying anything.

## Maintenance / best-effort contract

Paid-provider adapters are **best-effort** and may break when a vendor changes their API. On
failure the skill **falls back to free App Store RSS** and keeps going — a degraded run, never a
broken skill. Adapter fixes are community-maintained; the provider seam (`providers/CONTRACT.md`)
lets a one-file PR fix a broken vendor without touching the core. PRs welcome.

## Example

A full worked run — four findings scored, two cleared to 🟢, one held at 🟡, one set aside as
⚪ — is in [`examples/worked-run.md`](examples/worked-run.md). It shows the
ungrounded-degradation honesty line, the named triggers, the `🟡 → 🟢` risk-lowering verdicts
with their reasons, and the one band that *holds* under the aggressive skeptic pass.

## The risk strip — two ways you'll see it

Each finding's verdict can carry a small **risk strip**: a left-to-right scale,
**At risk → Watch → Clear**, with a star marking the band. Further right means *safer* — less
like what actually gets these apps one-starred. The **zone is the band** (firm); the star's
nudge *within* a zone is only a **lean** toward the neighbour (`deep` / `mid` / `border`), never
a precise score.

You'll see one of two forms depending on **where the skill is running** — you don't pick, the
skill detects the surface:

**1. Rich strip — in a chat client that can render widgets** (Claude Code's app/web UI). The
strip renders as an interactive colored bar inside the verdict, like this:

> At risk · Watch · **Clear** &nbsp;&nbsp;`🟢` &nbsp; ⟨———————————☆—⟩ &nbsp; *clear*
>
> *(In a widget-capable client this is a colored red→amber→blue bar with a white star marker.
> It's drawn live, so it can't be embedded as-is in this README — the line above is a flattened
> stand-in.)*

**2. Markdown strip — everywhere else** (a written-back `UNFORGET.md`, a pasted report, a CI
log, a plain terminal). The same information falls back to ASCII so it survives copy-paste:

```
🟡 A15  Newsletter silently undelivered    Watch   |····★·····| Clear   (watch → clear, mid)
🟢 A20  Latent sync dependency             Clear   |········★·| Clear   (border → just clear)
🟢 A6   3 screens miss a search entry      Clear   |·········★| Clear   (clear)
```

**Both forms always show the same three things together** so a strip can't be misread on its
own: the **band glyph** (🔴/🟡/🟢/⚪), the **zone word** in text, and the strip. Colour is never the
only cue — which also keeps it readable with colour-vision deficiency. And the strip never
travels without the run's **grounding line** and the count of ⚪ internal findings it set aside,
so "none in the red" is never mistaken for a clean bill of health it didn't earn.

## Layout

```
.claude-plugin/
  plugin.json              # marketplace manifest (for `claude plugin validate` / distribution)
skills/one-star-risk/
  SKILL.md                 # the rubric, honesty contract, skeptic, procedure
  providers/
    CONTRACT.md            # provider interface + the 4 rules
    rss.md                 # free default adapter
    applyra.md             # example paid adapter, verified (key from env/keychain, never repo)
    appfigures.md          # example paid adapter, unverified stub (shows how to add a vendor)
  reference/
    rubric.md              # provenance + worked examples (the 🔴 and the 🟡→🟢 demotion)
examples/
  worked-run.md            # a real four-finding scoring run, ungrounded path
```

## Related skills

Other public Claude Code skills by the same author — one-star-risk is the *triage lens* that
sits on top of what these *find*:

| Skill | What it does |
|---|---|
| [bug-echo](https://github.com/Terryc21/bug-echo) | After you fix a bug, finds and rates other instances of the same pattern. |
| [bug-prospector](https://github.com/Terryc21/bug-prospector) | Mines for hidden bugs pattern-based auditors miss — logic errors, broken assumptions, state-machine gaps. |
| [radar-suite](https://github.com/Terryc21/radar-suite) | Five audit skills in one install — a complete Swift/SwiftUI audit pipeline. |
| [workflow-audit](https://github.com/Terryc21/workflow-audit) | Xcode SwiftUI workflow auditing — dead ends, broken promises, data wiring. |
| [unforget](https://github.com/Terryc21/unforget) | Single source of truth for deferred work, so nothing punted gets lost. Pairs with one-star-risk's optional write-back. |
| [skill-reviewer](https://github.com/Terryc21/skill-reviewer) | Candid reviews of Claude Code skills, with file:line citations and ranked actions. |
| [tutorial-creator](https://github.com/Terryc21/tutorial-creator) | Generates personalized coding lessons from your own codebase. |
| [prompter](https://github.com/Terryc21/prompter) | Rewrites your prompts for clarity before they run. |

## License

Apache-2.0.
