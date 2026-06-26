# Worked run — two findings, ungrounded path

A real invocation of the one-star-risk lens against two findings from a pre-launch iOS app's
deferred-work backlog. It demonstrates four things the skill promises:

- the **loud ungrounded-degradation line** (no competitor reviews were supplied),
- a **named trigger** on every band (the honesty guardrail),
- the **aggressive skeptic pass** firing on its own and demoting both findings, and
- the value being **triage** — neither finding turned out to threaten the rating.

---

## Input

> Score these two findings for one-star risk. No competitor apps named (test the ungrounded
> path). Pre-launch product.
>
> **(1) S67** — "Add Extended Warranty requires 2 clicks: expand a disclosure THEN tap the Add
> button; when the section is empty the disclosure body has zero info value, only the button."
> Surfaces in: iOS + macOS Add/Edit Item flow.
>
> **(2) S99** — "Stuff Scout now returns value RANGES (e.g. $2,000–$11,500) instead of point
> estimates; more honest for items with real market dispersion, but mismatches a locked App
> Store screenshot that showed a confident point estimate." Surfaces in: Scout result screen +
> App Store screenshot.

---

## Output

**Grounding:** No competitor apps or App Store ID named.

> ⚠️ **No grounding corpus — bands below are priors from the generic rubric, not from real
> reviews.** Treat them as hypotheses, not findings. To ground this, name competitor App Store
> IDs (free RSS) or set `APPLYRA_API_KEY` (deeper corpus).

**Context flag:** pre-launch → no-review-buffer premium applies (borderline bands bump up,
marketing mismatches treated as more urgent).

### Initial scoring (three-part test)

**S67 — 2-click Add Extended Warranty**
- Notice: ✅ in the Add/Edit Item flow — a core-ish path a real user hits.
- Feel wronged: ⚠️ weak. An extra tap on a *secondary* feature is annoyance, not "the app lied / broke / took my money." The feature works.
- Act publicly: ❌ nobody one-stars over "two taps where one would do."
- Initial: **🟡 MED** (borderline).

**S99 — Scout value ranges vs. locked screenshot**
- Notice: ✅ Scout results are user-facing; the screenshot is on the store page.
- Feel wronged: ⚠️ the *only* grievance angle is sharpener 2 (contradicts marketing copy). The range itself is *more honest*, not a defect.
- Act publicly: ❌ a range where a screenshot showed a point estimate doesn't review-bomb.
- Initial: **🟡 MED** (held up only by the marketing-mismatch sharpener).

### Skeptic pass (aggressive — refute each non-⚪)

- **S67 → demote.** The grievance is "two taps instead of one" on a non-core add-on. That's annoyance, not betrayal — fails feel-wronged and act-publicly under a skeptical read. **🟡→🟢 LOW.**
- **S99 → demote.** The 🟡 leaned entirely on "the range mismatches the screenshot." Skeptic: the range is *more honest* — a positive, not a regression. The only residue is a locked-screenshot mismatch, a marketing item, not a user-facing defect. **🟡→🟢 LOW.**

### Report

**Grounded via: none (ungrounded priors).** Pre-launch weighting applied.

| Band | ID | Finding | Trigger (named) | Skeptic result |
|---|---|---|---|---|
| 🟢 LOW | S99 | Scout returns value ranges vs. locked point-estimate screenshot | Marketing-mismatch only (sharpener 2); the range itself is *more* honest — fails feel-wronged as a defect | demoted 🟡→🟢: range is a positive, not a bug; only angle is a screenshot mismatch — a marketing item |
| 🟢 LOW | S67 | Add Extended Warranty needs 2 clicks (expand disclosure → tap Add) | Notice ✅ (core-ish flow), but feel-wronged/act-publicly fail — annoyance on a non-core add-on, not grievance | demoted 🟡→🟢: two-taps-where-one-would-do is friction, not betrayal; the feature works |

**⚪ pile:** none in this batch (both were user-facing, just low-risk).

*Bands are a contestable lens, ungrounded (no real-review corpus this run), and overridable —
not a measurement. Both findings are worth doing for polish; neither threatens the launch
rating.*

---

## Why this is the right demonstration

The lens didn't just rank — it **talked itself down**. Both findings entered as a scary-looking
🟡 and left as 🟢 because the skeptic pass couldn't keep a trigger alive under a skeptical read.
That's the whole design: *a band you can't defend in one sentence is not a real band.* And it
said, loudly, that it had **no real reviews to ground on** — so the bands are hypotheses, not
findings. Confident-but-ungrounded output is exactly the failure this skill exists to avoid.
