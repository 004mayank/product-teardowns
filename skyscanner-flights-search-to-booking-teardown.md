# Teardown: Skyscanner — Flights Search → Booking Handoff

**Product:** Skyscanner (Flights marketplace metasearch)
**Author:** Mayank Malviya
**Date:** 20 Feb 2026
**Status:** v2 — end-to-end flow + concrete UX patterns/copy + ranking hypotheses + trust pack spec + experiment ideas

> Notes on sources: This teardown is based on product understanding and common Skyscanner / metasearch patterns; treat UI labels and exact copy below as *representative*, not pixel-perfect.

---

## TL;DR (what matters)
- Skyscanner is best understood as a **ranking + trust + routing** system wrapped in a flight search UI.
- The SRP (search results page) is the core: it’s where Skyscanner **compresses choice** into a few obvious picks (Best/Cheapest/Fastest).
- Monetization happens at **partner click-out**; the product’s long-term advantage comes from **price accuracy + partner quality governance**.
- If you were building a competitor, the differentiator isn’t UI polish—it’s the **trust stack** (mismatch prevention, rule clarity, partner reliability).

---

## 1) What Skyscanner is (in one line)
A **flight metasearch marketplace** that helps users **discover, compare, and choose** itineraries, then **hands them off** to an airline/OTA partner to complete booking — monetizing primarily via **referral / CPA / CPC** and partner placements.

---

## 2) The user’s job-to-be-done (JTBD)
**Primary JTBD:** “Help me find the best flight for my constraints (price, time, stops, airline, baggage, refundability), fast — and get me to a booking I can trust.”

**Sub-JTBDs:**
- Reduce search space quickly (filters, sorting, flexible dates).
- Build confidence in: final price, baggage/refund rules, and partner credibility.
- Preserve intent across sessions (recent searches, alerts, price tracking).

---

## 3) The core funnel (macro)
**A. Intent capture** → **B. Search** → **C. Result scan** → **D. Narrow** → **E. Select itinerary** → **F. Choose partner** → **G. Booking handoff**

Funnel reality:
- Skyscanner doesn’t “own purchase”, but it *does* own **qualified click-outs** and **trust**.

---

## 4) Information architecture (IA)
Typical primary tabs / entry points:
- Flights
- Hotels
- Car hire
- Explore / Everywhere (inspiration)
- Trips / Saved / Alerts (varies by platform)

For this teardown we focus on **Flights**.

---

## 5) End-to-end flow (Flights)

### 5.1 Entry → Search form
**Inputs**
- From / To (airport/city)
- Dates (one-way/return)
- Travelers + cabin

**Notable UX patterns (representative)**
- Autocomplete disambiguation: “London (Any)”, “London Heathrow (LHR)”, etc.
- Recent searches chip: “DEL → BOM · 1 adult · Mar 12–15”
- Date picker optimized for scanning cheaper dates:
  - “Cheapest month” / “Whole month” / “Flexible” modes
  - Inline price hints per day (or a price graph)

**Representative copy**
- “Where from?” / “Where to?”
- “Add nearby airports”
- “Direct flights only”
- “Flexible dates”


### 5.2 Search execution (loading)
**System goals**
- Provide a **fast first set** of results, then refine (streaming, sorting updates).
- Handle partial/stale pricing gracefully.

**Representative patterns**
- “Searching 1000+ sites…”
- Progressive disclosure: show top results early, keep refining in background.


### 5.3 Search results list (SRP)
This is the **highest-leverage screen**.

**Core SRP objects**
- Itinerary card: total price, duration, stops, departure/arrival times
- Airline(s)
- Badges: “Best”, “Cheapest”, “Fastest” (or variants)

**Representative SRP card fields**
- “₹18,430 · 2h 10m · Non-stop · 06:10–08:20”
- “1 stop · 6h 30m (1h 20m layover)”
- Airline/alliances: “IndiGo”, “Air India”

**Metasearch tradeoff**
- Show enough detail to support a choice **without** turning SRP into a policy document.

**Common SRP patterns**
- Sticky filter/sort bar
- “Best” as default sort (reduces cognitive load)
- Inline date-flex affordance (price calendar / graph)


### 5.4 Filters & sorting (narrowing)
Typical filters:
- Stops
- Times (depart/arrive)
- Duration
- Airlines
- Airports
- Bags (sometimes)
- Refundability / changeability (sometimes)

Sorting:
- Best / Recommended
- Cheapest
- Fastest
- Earliest

**Design tension**
- More filters increases decision quality but increases complexity and “no results” cliffs.

**Representative microcopy patterns**
- “Show only direct flights”
- “Best (recommended)”
- “Stops: Any / Non-stop / 1 stop”
- “Airports: Include nearby”


### 5.5 Itinerary detail + partner selection
When a user taps an itinerary, they usually see:
- Full legs, layovers
- Baggage rules summary
- Fare class cues (where available)
- A list of **partners** (airlines/OTAs) offering the same itinerary at different prices

**Partner list design goals**
- Maximize **trust** and **click-out conversion**.
- Reduce “bait price” perception.

**Common mechanisms**
- “Best deal” highlight
- Partner labeling: “Official site”, “Trusted”, “Cheapest”, “Top pick”
- Clear disclosure: “You’ll be redirected to X to complete booking.”

**Representative partner list rows**
- “Airline Official Site — ₹19,200”
- “OTA Y — ₹18,430 (Cheapest)”
- “OTA Z — ₹18,900”


### 5.6 Booking handoff (click-out)
**This is Skyscanner’s moment of monetization.**

Click-out payload typically includes:
- itinerary + fare class
- passenger count
- currency/locale
- tracking parameters (partner attribution)

**Success criteria**
- Click-out rate (SRP → partner)
- Downstream conversion (if CPA)
- Low mismatch/complaint rates (protects repeat usage)

---

## 6) How “Best” ranking likely works (hypotheses)
Skyscanner’s “Best/Recommended” is a **multi-objective ranker**. A plausible structure:

### 6.1 Candidate features (per itinerary)
**Value**
- Total price (normalized by market distribution)
- Hidden cost risk (bags, fare rules uncertainty)

**Convenience**
- Total duration
- Stops count
- Layover duration and airport quality (connection pain)
- Departure/arrival time fit (e.g., red-eye penalty)

**Reliability & trust**
- Partner reliability (mismatch rate, complaints)
- Itinerary stability (fare volatility / refresh freshness)

**User preference fit** (implicit/explicit)
- Airline preference / loyalty
- Avoid certain airports
- Past click/booking behavior

### 6.2 How the “Best” badge could be generated
Two common strategies:
1) **Global best**: top-ranked itinerary from the model.
2) **Pareto best**: choose a flight that’s near-optimal on price + duration + stops (a compromise winner).

### 6.3 Marketplace-aware adjustments (risky if overused)
- Minor boosts for higher EPC partners (earnings per click)
- Penalize partners with mismatch issues

**Guardrail principle**
- A mature system optimizes **repeat usage + trust** over short-term revenue.

---

## 7) Trust architecture (what Skyscanner must get right)
Metasearch trust problems:
1. **Price mismatch** at partner site (bait vs final).
2. **Baggage/refund confusion** (rules differ across partners).
3. **Partner credibility** (unknown OTA).
4. **Information loss on redirect** (user must re-verify details).

Skyscanner’s trust tools:
- Partner labels/ratings (or inferred trust)
- Clear redirect disclosure
- Clear tradeoff cues for “Best” vs “Cheapest”
- Price alerts and saved trips

---

## 8) The “Trust Pack” spec (concrete, measurable)
A v2 product bet: make trust legible *before* click-out.

### 8.1 Trust Pack components
1) **Price Confidence Score** (0–100)
   - Meaning: likelihood that the displayed price remains available on partner site.
   - Inputs: historical mismatch rate for that partner+route, fare freshness age, fare volatility on route, inventory scarcity signals.
   - UI: “Price confidence: High / Medium / Low” with tooltip.

2) **Rules Clarity Badge**
   - Meaning: how clearly baggage + change/refund rules are known/normalized.
   - Inputs: rule completeness %, airline policy availability, partner rule consistency.
   - UI: “Baggage rules: Clear” / “Some details missing”.

3) **Partner Reliability Index** (0–5 or 0–100)
   - Meaning: operational quality of partner experience.
   - Inputs: mismatch rate, cancellation/refund complaint rate, customer support response proxy, chargeback/dispute signals.
   - UI: “Trusted partner” with “Why?” breakdown.

### 8.2 Measurement & guardrails
- Primary: reduce **mismatch rate** and **complaints per 1k click-outs**.
- Secondary: maintain/improve **click-out rate** and **repeat searches (7/30-day)**.
- Guardrail: don’t reduce choice diversity (avoid over-penalizing small partners unfairly without evidence).

### 8.3 Representative UI block (itinerary detail)
> “Before you go: price verified 2 min ago · baggage rules available · partner rated 4.3/5”

---

## 9) Monetization model (practical)
### 9.1 Primary
- **Referral / CPA**: paid when booking converts at partner.
- **CPC / click-out**: paid per qualified click.

### 9.2 Secondary
- Sponsored placements / boosted partners
- Ads (display/native)

### 9.3 The ranking lever
The “Best/Recommended” rank is where user value (conversion, satisfaction) and marketplace value (revenue) collide.

---

## 10) Likely drop-offs + diagnostic hypotheses
These are the most plausible “leak” points in the funnel:

### 10.1 SRP overwhelm → no click
Symptoms:
- Long scroll, lots of filter toggles, no itinerary detail opens.
Hypotheses:
- Ranking isn’t surfacing obvious winners.
- Too many similar options; users need clustering (“same flight, different partner”).

### 10.2 Itinerary detail → partner click-out drop
Symptoms:
- Users open detail but don’t click any partner.
Hypotheses:
- Partner list looks untrustworthy.
- Baggage/refund ambiguity creates anxiety.
- Price dispersion raises “bait price” suspicion.

### 10.3 Click-out → user returns immediately (bounce-back)
Symptoms:
- Quick return from partner site.
Hypotheses:
- Price mismatch.
- Partner login/signup friction.
- Itinerary not found / changed.

### 10.4 Key instrumentation (minimum viable)
- SRP → detail open rate
- Detail → click-out rate (by partner)
- Bounce-back rate within 30–120 seconds
- Mismatch proxy: partner landing price != shown price

---

## 11) Growth loops & retention hooks
- **Price alerts**: alert → reopen → search → click-out.
- **Recent searches / saved trips**: reduces friction.
- **Flexible dates / Everywhere**: creates discovery sessions.

---

## 12) Key metrics (what a Skyscanner team likely watches)
### Acquisition & activation
- Search starts / DAU
- % sessions that reach SRP

### Marketplace efficiency
- SRP click-through to itinerary detail
- Partner click-out rate
- Revenue per search/session (RPS)

### Trust & quality
- Price mismatch rate (user-reported + inferred)
- Partner complaint rate (proxy)
- Repeat search rate (7/30-day)

### Performance
- Time to first result (TTFR)
- SRP render p95

---

## 13) What’s notably great (strengths)
1. **Massive choice, still usable**: SRP design + ranking reduce overwhelm.
2. **Strong discovery affordances**: flexible dates/explore.
3. **Clear handoff model**: user understands Skyscanner is the comparison layer.
4. **Retention through alerts**: converts indecision into future return.

---

## 14) What’s structurally hard (weaknesses / product debt)
1. **Price accuracy is a constant battle** (partners, caching, fare volatility).
2. **Post-click experience is out of Skyscanner’s control**.
3. **Incentive misalignment risk**: monetization can degrade trust.
4. **Information loss on redirect**: users must re-verify details.

---

## 15) Opportunities (product bets)
### 15.1 Smarter comparisons
- True total trip cost (bags + seat + refundability where possible)
- Time-cost tradeoff visualization (₹ per hour saved)
- “Same flight, different sellers” clustering

### 15.2 Reduce redirect pain
- Preflight confirmation: recap itinerary + baggage + refund rules + partner terms
- Deep-link back post-booking to store booking in Trips (if partnerships allow)

### 15.3 Intent persistence
- Side-by-side compare table for 2–4 itineraries
- Shareable comparison link

---

## 16) Experiment backlog (practical)
1) **Trust Pack A/B** on itinerary detail
   - Success: mismatch ↓, click-out steady/↑, repeat searches ↑.
2) **Partner list redesign**
   - Add “Why this partner?” disclosure and reliability labels.
3) **Clustering identical flights**
   - Reduce SRP cognitive load; increase detail opens.
4) **Price freshness indicator**
   - “Updated X minutes ago” + auto-refresh on open.

---

## 17) Open questions for v3
1. What exactly are Skyscanner’s “Best” ranking ingredients (price/duration/stops/CO₂/partner quality)?
2. How do they operationalize and message mismatch, and what controls exist per partner?
3. How are baggage/refund rules normalized across partners?
4. How are price alerts tuned to avoid notification fatigue while maximizing return?
5. Biggest drop-off: SRP → detail, or detail → click-out?
