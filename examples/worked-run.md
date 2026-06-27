# Worked run — four findings, ungrounded path

A real invocation of the one-star-risk lens against four findings from a pre-launch iOS app's
deferred-work backlog. It demonstrates the full output shape:

- the **loud ungrounded-degradation line** (no competitor reviews were supplied),
- a **named trigger** on every band (the honesty guardrail),
- the **aggressive skeptic pass** firing on its own — two findings have their risk lowered
  (in the report this reads as "cleared to 🟢", not "demoted"), one **holds** under scrutiny,
- the **risk strip** in its copy-paste (ASCII) form, with the per-finding **lean** shown as
  words (`deep`/`mid`/`border`/`clear`), never a number,
- the **⚪ pile** collapsing an internal, non-user-facing finding to a single line, and
- the value being **triage** — of four findings, exactly one is worth worrying about for the
  rating.

---

## Input

> Score these findings for one-star risk. No competitor apps named (test the ungrounded path).
> Pre-launch product.
>
> **(1) S67** — "Add Extended Warranty requires 2 clicks: expand a disclosure THEN tap the Add
> button; when the section is empty the disclosure body has zero info value, only the button."
> Surfaces in: iOS + macOS Add/Edit Item flow.
>
> **(2) S99** — "Stuff Scout now returns value RANGES (e.g. $2,000–$11,500) instead of point
> estimates; more honest for items with real market dispersion, but mismatches a locked App
> Store screenshot that showed a confident point estimate." Surfaces in: Scout result screen +
> App Store screenshot.
>
> **(3) S104** — "The paywall lists 'Recall alerts' as a subscriber benefit, but the feature is
> currently free for everyone — the gate isn't wired." Surfaces in: subscription paywall, seen
> by every user who opens it.
>
> **(4) S110** — "Internal `ValuationCache` key collides for two items sharing a model number;
> second lookup returns the first item's cached estimate until the cache is invalidated."
> Surfaces in: internal cache layer (never shown directly; estimates are re-fetched on view).

---

## Output

**Grounding:** No competitor apps or App Store ID named.

> ⚠️ **No reviews to learn from — bands below are priors from the generic scoring test, not from
> real reviews.** Treat them as hypotheses, not findings. To ground this, name competitor App
> Store IDs (free RSS) or set `APPLYRA_API_KEY` (more reviews to learn from).

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

**S104 — Paywall lists a benefit that's actually free**
- Notice: ✅ the paywall is shown to every user who considers subscribing.
- Feel wronged: ✅ this is the classic "the app told me X and it wasn't true" — a paid-tier promise that doesn't match reality (sharpener 2, and it's a *payment*-adjacent claim). Pre-launch weighting sharpens it further.
- Act publicly: ✅ paywall/pricing dishonesty is a top reason this app class gets one-starred — people do review over "they sold me on a feature that was already free / not gated as claimed."
- Initial: **🟡 MED**, leaning 🔴 under the pre-launch premium.

**S110 — Internal cache key collision**
- Notice: ❌ the collision is invalidated on view (estimates re-fetch), so a real user never sees a wrong value surface.
- Feel wronged: n/a — nothing user-visible to feel wronged about.
- Act publicly: n/a.
- Initial: **⚪ NONE** (internal; fix it for correctness, but it isn't a rating risk).

### Skeptic pass (aggressive — refute each non-⚪)

*(Internally this step "demotes" or "holds" a band; in the report the lowered ones are written
as "cleared to 🟢" so the direction isn't mistaken for "made worse.")*

- **S67 → lower.** The grievance is "two taps instead of one" on a non-core add-on. That's annoyance, not betrayal — fails feel-wronged and act-publicly under a skeptical read. **🟡→🟢 LOW.**
- **S99 → lower.** The 🟡 leaned entirely on "the range mismatches the screenshot." Skeptic: the range is *more honest* — a positive, not a regression. The only residue is a locked-screenshot mismatch, a marketing item, not a user-facing defect. **🟡→🟢 LOW.**
- **S104 → holds.** Tried to refute it: is it just annoyance? No — it's a *payment* claim that's false, on a surface every prospective subscriber sees, in the exact category (pricing honesty) this app class gets review-bombed for. The trigger survives all three parts. **Stays 🟡** (and the pre-launch premium keeps it from being talked down). The one finding here actually worth fixing before launch.

### Report

**Grounded via: none (ungrounded priors).** Pre-launch weighting applied.

Risk strip legend: `risk‹───★─────›clear` — left = riskier, right = safer; the `★` marks the
band. The **zone is the band**; the star's nudge within a zone is a **lean** stated in the word
below it (`watch, deep` / `clear` …), not a score.

| Band | ID | Finding | Trigger (named) | Verdict | Risk strip |
|---|---|---|---|---|---|
| 🟡 MED | S104 | Paywall lists "Recall alerts" as a paid benefit, but it's free for everyone (gate unwired) | Notice ✅ + feel-wronged ✅ + act-publicly ✅ — a false *payment* claim on a surface every prospective subscriber sees; pricing dishonesty is a top one-star driver for this app class | holds — survives all three parts; a false paywall promise is review-bomb territory, and pre-launch weighting keeps it up | `risk‹───★─────›clear`<br>watch, deep |
| 🟢 LOW | S99 | Scout returns value ranges vs. locked point-estimate screenshot | Marketing-mismatch only (sharpener 2); the range itself is *more* honest — fails feel-wronged as a defect | cleared to 🟢 — range is a positive, not a bug; only angle is a screenshot mismatch, a marketing item | `risk‹───────★─›clear`<br>clear |
| 🟢 LOW | S67 | Add Extended Warranty needs 2 clicks (expand disclosure → tap Add) | Notice ✅ (core-ish flow), but feel-wronged/act-publicly fail — annoyance on a non-core add-on, not grievance | cleared to 🟢 — two-taps-where-one-would-do is friction, not betrayal; the feature works | `risk‹────────★›clear`<br>clear |

**⚪ pile:** 1 finding scored ⚪ NONE — **S110** (internal cache-key collision; invalidated on
view, never surfaces a wrong value to a user). Fix for correctness, but it's not a rating risk.

> **In a widget-capable chat client** the Risk strip column renders as a live colored
> red→amber→blue bar with a white star marker, tucked under each verdict. Everywhere else (a
> written-back `UNFORGET.md`, a pasted report, CI, a terminal) it falls back to the ASCII form
> shown above — so the lean survives copy-paste. Either way the **band glyph + zone word** ride
> alongside it, so the strip is never read by colour alone.

*Bands are a contestable lens, ungrounded (no real reviews to learn from this run), and
overridable — not a measurement. S67 and S99 are worth doing for polish; S110 for correctness;
**S104 is the one that threatens the launch rating** and should be fixed (wire the gate, or drop
the claim from the paywall) before submission.*

---

## Why this is the right demonstration

The lens didn't just rank — it **talked itself down where it should and held firm where it
shouldn't.** Two findings entered as a scary-looking 🟡 and left as 🟢 because the skeptic pass
couldn't keep a trigger alive under a skeptical read. One (S104) entered 🟡 and *stayed* 🟡
because its trigger — a false paywall promise — survived every refutation: that's the lens
refusing to talk down a real risk just to look calm. And one (S110) never left ⚪ because it's
invisible to users.

That spread is the whole design: *a band you can't defend in one sentence is not a real band —
but a band you can defend doesn't get demoted to keep the report comfortable.* It also said,
loudly, that it had **no real reviews to ground on** — so the bands are hypotheses, not findings.
Confident-but-ungrounded output is exactly the failure this skill exists to avoid.
