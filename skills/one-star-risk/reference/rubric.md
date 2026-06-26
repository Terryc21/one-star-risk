# The One-Star Rubric — Provenance & Worked Examples

Long-form companion to SKILL.md. Read this when you want the *why* behind a band, or a worked
example to calibrate against. The operational rubric in SKILL.md is the canonical short form;
this file does not override it.

## Where this came from

The criterion was developed by a solo developer ranking a pre-launch deferred-work backlog the
day before App Store submission. The move that makes it work is counterintuitive: **don't ask
the agent to "find risky stuff." Make it build the criterion first, anchored in real evidence,
then apply it.** A vague fear ("what if I get review-bombed at launch?") is useless to a coding
agent. An operational definition with a pass/fail test is auditable.

The three construction steps:
1. **Mine ground truth** — scan competitor App Store reviews for the *actual* reasons similar
   apps get one star. Rarely the crash; it's the "used to work / I paid for this / the app told
   me X and it was wrong" feeling.
2. **Operationalize** — turn that into a definition tight enough to score against: the
   three-part test (notice / feel-wronged / act-publicly) plus the bands.
3. **Make it a reusable artifact** — drop the definition into an audit pass so every finding
   comes back with a band *and a one-line stated trigger*. That stated-trigger gate is the
   whole point: it keeps a confident-looking band overridable.

## The diagnosis this tool closes

The machine's honesty is **bottom-up**: code → findings → severity. The user's honesty is
**top-down**: experience → expectation → betrayal. They reach different conclusions because they
start from different places. **The gap between code-honesty and experience-honesty is where
one-star reviews live.** This rubric is an instrument built to stand in that gap — and it is
only honest if it shows its work. The machine can't feel the user's betrayal; it can only flag
the conditions that produce it, *if* you force it to name why each time.

## Worked example A — the 🔴 (CloudKit 21% sync)

An audit found CloudKit sync covered 18 of 85 model fields (21%). The code-level severity was
"HIGH, large effort, post-release" — found and deferred across three separate sessions, each
time correctly classified by every code-level signal.

Under this rubric: a user syncing between devices **immediately sees** 79% of their data
missing (passes **notice**), feels the app **broke an implied "your stuff syncs" promise**
(passes **feel-wronged**), and is the kind of thing people **review over** (passes
**act-publicly**). Sharpener 1 (core flow) and sharpener 2 (contradicts the implied promise)
both apply. → **🔴**, regardless of fix effort. "Technically correct deferral" (sharpener 3) is
not a defense. This is the central example: same finding, opposite conclusion, because the lens
scores the *felt* experience, not the code.

## Worked example B — the demotion (scout value ranges, 🟡→🟢)

A change made an in-app product-research feature return value *ranges* (e.g. "$2,000–$11,500")
instead of point estimates. Initial inference: 🟡 — looked like a regression that telegraphs
uncertainty and mismatches a locked App Store screenshot showing a confident point estimate.

The **skeptic pass** refuted it: the range is *more honest* for items with genuine market
dispersion — a positive, not a bug. The only surviving angle was the screenshot mismatch (a
marketing item, not a user-facing defect), which doesn't clear act-publicly on its own. →
**demoted to 🟢.** This is the skeptic working exactly as intended: a scary-looking band that
could not keep a surviving trigger got floated back down. Without the skeptic, the report would
have over-scared.

## The pseudo-precision aside (a candid note, kept deliberately)

The author folded the one-star band into an existing backlog sort as one factor:

```
Score = (Urgency × RiskIfNotFixed × ROI × OneStar) ÷ (Effort × RiskOfFixing)   (×2 on OneStar pre-launch)
```

…multiplying ordinal color-codes (🔴=4, 🟡=3, …) into a single number that **is not
mathematically defensible** — and knew it: *"multiplying color-coded ratings together isn't
science. It just floats the scary stuff to the top, which is all I wanted."*

The honest, slightly uncomfortable truth most tool-users won't say out loud: a deliberately
non-rigorous instrument is still useful **as a sort key** if you don't pretend it's a metric.
The danger isn't the rough math — it's dressing it up as measurement. If you implement the
sort key (UNFORGET write-back), name it a heuristic out loud. That naming *is* the honesty move.

## The same insight, arriving twice

A prior project note had already named the felt-truth-vs-literal-truth mechanism months earlier,
in a narrower pricing-copy context (a "feature-was-free-now-isn't" anchor — users who feel a
previously-free thing was taken away leave one-star reviews even when the change is defensible).
The same insight arriving twice, from two directions, is itself evidence it's load-bearing. The
*mechanism* (felt-truth beats literal-truth) is durable; only the specific *trigger* changes
with circumstances.
