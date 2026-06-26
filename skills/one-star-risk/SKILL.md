---
name: one-star-risk
description: Re-score audit findings (a diff, a file, a TODO list, or another auditor's output) for App Store one-star-review risk. Assigns each finding a 🔴/🟡/🟢/⚪ band with a NAMED, overridable trigger, grounded in real competitor reviews when available. Use when asked to "scope for one-star risk", "which findings threaten my rating", "rank deferred work by review risk", "what would get me review-bombed", or before an App Store launch. A judgment LENS, not a detector — it does not find bugs, it scores findings you already have.
license: Apache-2.0
---

# One-Star Risk

A **judgment rubric, not a detector.** Other tools find what is true/false in code (a leak,
a missing dismiss button). This one assigns a *contestable opinion* — a one-star band — with
a *forced rationale*. It cannot be "right"; it can only be "well-reasoned and overridable."

The whole design serves one constraint: **never let "the agent assigned 🔴" start reading as
"this IS 🔴."** Every band carries a named trigger and survives a skeptic pass, or it gets
demoted. If you remember one thing: this skill shows its work, and a band you can't defend in
one sentence is not a real band.

## What it does NOT do

- It does **not** discover findings. Feed it findings (see Inputs). If you want discovery,
  run an auditor first (Axiom, radar-suite, workflow-audit, a plain `git diff`, your TODO
  list) and pipe its output here.
- It does **not** change a finding's truth. A ⚪ stays ⚪; this lens only assigns *review-risk*.
- It does **not** buy inflated scores with a paid data provider. Paid grounding deepens the
  *evidence*; the *rubric* is identical. A 🔴 is a 🔴 by the same three-part test whether you
  paid or not.

---

## The core rubric (reuse verbatim — this is the operational definition)

A finding is a one-star risk only if a real user would do **all three**. If any fails, it is
LOW or NONE (🟢/⚪), regardless of how bad it looks in code.

**The three-part test:**
1. **Notice** — a real user, in normal use, actually encounters it. (Not a dev-only path, not
   a 0.1%-edge case, not something behind a flag they'll never flip.)
2. **Feel wronged** — encountering it produces a sense of *grievance*, not mere annoyance.
   "The app lied to me / broke / took away what I paid for" — not "an extra was slightly clunky."
3. **Act publicly** — the grievance is strong enough to move them to leave a public one-star
   review (or tell others), rather than just sigh and move on.

**Bands:**
- 🔴 **HIGH** — passes all three with force. Immediate, visible, breaks an implied promise.
- 🟡 **MED** — passes the test but mildly; a noticed annoyance in a core-ish flow. Grievance
  is real but not white-hot.
- 🟢 **LOW** — fails at least one part, OR is a marketing/cosmetic mismatch a user could notice
  but wouldn't review over.
- ⚪ **NONE** — internal-only, dev-path, or invisible to users. **Most audit findings are ⚪.**
  This is correct and expected — the lens's value is triage, not alarm.

**The four sharpeners** (apply after the three-part test to break ties):
1. **Proximity to the default flow raises risk.** A broken *core-flow* feature reads as "the
   app broke," not "an extra didn't work." Distance from the happy path lowers a band.
2. **A defect that contradicts your marketing copy outranks a bigger one nobody was promised.**
   Felt-truth beats literal-truth. If a screenshot/store-description/onboarding promised X and
   the app does not-X, that is a grievance even if the gap is small.
3. **"Technically correct" is not a defense.** Code-honest ≠ experience-honest. "The severity
   classification was right at the code level" does not lower the band if the *user's*
   experience is betrayal.
4. **Pre-launch weights one-star risk harder** (the "no-review-buffer premium"). With zero
   existing reviews, the first ones set the public rating outright — there is no buffer of
   happy reviews to dilute a bad one. If the target product is pre-launch, bump borderline
   🟡→🔴 and treat 🟢-marketing-mismatches as more urgent than you otherwise would.

**The honesty guardrail (non-negotiable):** every non-⚪ band MUST be accompanied by a
one-line **named trigger** stating *which* of notice/feel-wronged/act-publicly it passes and
*why*. A band with no stated trigger is not allowed — demote it to ⚪ or write the trigger.
This is what keeps the confident-looking band overridable. The machine can't feel the user's
betrayal; it can only flag the conditions that produce it — *if* you force it to name why,
every time.

---

## The skeptic pass (always on, aggressive by default)

After you assign initial bands, run a second pass that tries to **refute** every non-⚪ band.
For each, ask: *"Can this band still name a trigger that survives a skeptical reading?"*

- If a finding's trigger leans on a path a real user won't hit → demote (fails **notice**).
- If the "grievance" is actually just annoyance → demote (fails **feel-wronged**).
- If the user would sigh but not review → demote (fails **act-publicly**).
- If the only angle is "technically it's wrong" with no felt-betrayal → demote.

**Default is aggressive: when refutation is plausible, demote.** The entire purpose of this
lens is to *not* over-scare — a deliberately skeptical second pass is what makes the top of
the list trustworthy. Record each demotion with its reason (e.g. *"🟡→🟢: re-examined, the
range is more honest, not a bug; only angle is a screenshot mismatch — marketing item, not a
user-facing defect"*). Surviving bands are the real ones.

A worked demotion this rubric's author did by hand: a finding that scout returns value *ranges*
instead of point estimates looked like a 🟡 regression, but the skeptic pass found the range is
*more* honest (a positive), and the only surviving angle was a locked-screenshot mismatch — a
marketing item — so it was demoted to 🟢. That is the skeptic working as intended.

---

## Grounding — and the honesty contract

Bands are far more defensible when grounded in **why apps like this one actually get one star**
— which is rarely the crash. It's the "used to work / I paid for this / the app told me X and
it was wrong" feeling. The skill grounds against real competitor reviews when it can, and is
**loud about how well it grounded** every run.

### The four-part honesty contract (state these in every run)

1. **Always report the grounding tier.** Open every run with a line naming the provider and
   depth: *"Grounded via App Store RSS: 480 recent reviews, 3 countries (recent themes, not
   exhaustive)"* or *"Grounded via Applyra: 4,200 reviews, 18 months"* or *"No grounding
   corpus — bands below are priors from the generic rubric, not from real reviews."*
2. **Degrade loudly to ungrounded priors.** If there's no corpus (no competitors named, feed
   failed, offline), say so plainly: *"No grounding corpus — treat bands as hypotheses from the
   rubric, not findings from real reviews."* Confident-but-ungrounded output is the exact slop
   failure this whole skill exists to avoid.
3. **Paid deepens evidence, never changes the rubric.** A richer corpus surfaces more/older
   themes; it does not move a band by itself. Paying buys better-*grounded* scores, not
   *better* scores. Say this so nobody thinks a subscription inflates bands.
4. **The grounding floor is shallow — label it.** The free RSS feed returns *recent* reviews,
   capped depth, per country. Always call it "recent themes," never imply all-time exhaustiveness.

### Tiered fallback (best-available wins; always lands somewhere)

1. **Paid provider** (if the user configured one) — deepest corpus.
2. **Public App Store RSS** — zero-setup default; everyone gets at least this.
3. **User-pasted corpus** — offline / feed-failure fallback.
4. **No corpus** — honest "ungrounded priors" degradation.

### How grounding plugs in (provider seam, not hardcoded vendors)

The skill talks to **a provider**, never to a specific vendor. Providers live in
`providers/`. Two ship in v1:

- **`providers/rss.md`** — free default, zero setup, the public path. Apple's public RSS
  customer-reviews feed. No key, no account.
- **`providers/applyra.md`** — a paid ASO API the author can test, which is what makes the
  seam trustworthy to ship. Reads its key from the **environment / OS keychain, never from a
  file in this repo.** Free users simply don't configure it and fall to RSS.

Select with `--provider=<name>` (default: `rss`). To add another vendor (App Radar, AppFollow,
Sensor Tower…), drop a new file in `providers/` following the same contract — see
`providers/CONTRACT.md`. **Fail soft, never hard:** if a configured provider errors (API
changed, key missing, network), catch it, print *"<Provider> grounding failed; falling back to
free App Store RSS,"* and continue. A broken paid adapter is a *degraded* run, never a *broken*
skill.

---

## Inputs (what you can score)

The core knows exactly **one finding shape**. Anything reducible to it can be scored:

```
finding = {
  id,                # stable handle (e.g. "S67", or a line ref, or just an index)
  description,       # what the issue is
  whereItSurfaces,   # where a user would hit it (flow / screen / "internal only")
  userFacing?        # optional hint; the rubric re-decides this anyway
}
```

Accepted input forms (the skill adapts each into the shape above):
- **A `git diff`** — each hunk/file becomes a candidate finding.
- **A single file** — read it, derive findings from its TODO/FIXME/known-issue comments.
- **A plain TODO / backlog list** — one finding per line/row.
- **Another auditor's output** — Axiom, radar-suite, workflow-audit, or any tool whose
  findings reduce to the shape above. This is the high-value use: *"of these 60 findings,
  which 3 threaten my rating?"*
- **Inline in the prompt** — the user just describes the findings in chat.

If the user names **competitor apps** (or the app's own App Store ID), use them to ground.
If they name none, ground is "none" — say so per contract item 2.

---

## Output

A report, in this order:

1. **Grounding line** (honesty contract item 1) — provider, depth, caveats.
2. **A ranked table**, scariest first, columns:
   `Band | ID | Finding | Trigger (named) | Skeptic result`
   - `Band` is the *post-skeptic* band.
   - `Trigger` names which of notice/feel-wronged/act-publicly it passes, in one line.
   - `Skeptic result` is `survived` or `demoted N→M: <reason>`.
3. **The ⚪ pile**, collapsed to a count + IDs (*"41 findings scored ⚪ NONE — internal/dev-path,
   not user-visible"*). Do not pad the report with ⚪ detail; the value is the non-⚪ few.
4. **A one-line honesty footer** restating: bands are a contestable lens, grounded at the tier
   named above, overridable — not a measurement.

Color/severity conventions: 🔴 CRITICAL, 🟡 HIGH, 🟢 MEDIUM, ⚪ LOW/NONE.

---

## Optional: UNFORGET write-back (detected, opt-in — never automatic)

If an `UNFORGET.md` (or similarly-structured ranked-backlog file with a `1★` column) is present
**and** the user passes `--write` or confirms, write the post-skeptic band into that file's `1★`
column per row, appending the named trigger as the row's `**1★:<band>**` note (matching the
existing in-file convention). This reproduces, automatically, the by-hand ranking the rubric's
author did.

Rules:
- **Never write without explicit opt-in.** Default is report-only.
- **Surgical edits only.** Update the `1★` cell and append the trigger note; do not reformat
  other rows or columns.
- **This is the only coupling to a private workflow.** It is optional by design — the public
  score-only path needs no UNFORGET file and never touches one.

If the backlog uses a numeric sort key that includes one-star as a factor (e.g. the author's
`Score = (Urgency × RiskIfNotFixed × ROI × OneStar) ÷ (Effort × RiskOfFixing)`), you may
recompute it — **but state plainly that this is a heuristic sort key, not a metric.** Multiplying
ordinal color-codes is not mathematically defensible; it just floats the scary stuff to the top,
which is the only thing it's for. Naming it as a heuristic is the honesty move; dressing it up as
measurement is the dishonest one.

---

## Procedure (what to actually do when invoked)

1. **Resolve inputs.** Identify the findings source and reduce to the finding shape. Identify
   named competitor apps / App Store ID (for grounding) or note there are none.
2. **Ground.** Select provider (`--provider`, default rss). Fetch the corpus via the chosen
   `providers/<name>.md`. On any provider error, fall soft to RSS, then to none. Record tier +
   depth + caveats for the honesty line.
3. **Mine the corpus** (if any) for the *recurring one-star themes* for this app class — the
   felt reasons, not the crash count. These sharpen which findings map to a real grievance.
4. **Score** each finding with the three-part test → initial band + named trigger. Apply the
   four sharpeners to break ties. Bump for pre-launch if applicable.
5. **Skeptic pass** (always, aggressive). Refute each non-⚪ band; demote any that can't keep a
   surviving trigger; record the reason.
6. **Report** in the output order above, honesty line first.
7. **(Optional)** If UNFORGET write-back was requested and a target file exists, write surgically.

---

## Reference

- `reference/rubric.md` — the criterion's full provenance and worked examples (the CloudKit
  21%-sync 🔴, the scout-range 🟡→🟢 demotion), for when you want the long form.
- `providers/CONTRACT.md` — the provider interface; how to add a vendor.
- `providers/rss.md`, `providers/applyra.md` — the two v1 adapters.

*This skill packages a way-of-thinking, not a checker. Its output is an opinion that shows its
work. If a band can't name a trigger that survives a skeptic, it isn't a band — it's noise.*
