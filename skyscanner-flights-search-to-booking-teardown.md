# Teardown: Skyscanner — Flights Search → Booking Handoff

**Product:** Skyscanner (Flights marketplace metasearch)  
**Author:** Mayank Malviya  
**Date:** 19 Feb 2026  
**Status:** v1 — end-to-end flow + key UX decisions + monetization + opportunities

> Notes on sources: I’m writing this teardown from product understanding and common Skyscanner patterns; web research isn’t available in this environment (no search API key configured). Treat specifics (labels, exact UI copy) as representative, not pixel-perfect.

---

## 1) What Skyscanner is (in one line)
A **flight metasearch marketplace** that helps users **discover, compare, and choose** itineraries, then **hands them off** to an airline/OTA partner to complete booking — monetizing primarily via **referral / CPA / CPC** and partner placements.

---

## 2) The user’s job-to-be-done (JTBD)
**Primary JTBD:** “Help me find the best flight for my constraints (price, time, stops, airline, baggage, refundability), fast — and get me to a booking I can trust.”

**Sub-JTBDs:**
- Reduce search space quickly (filters, sorting, flexible dates).
- Provide confidence in: final price, baggage/refund rules, and partner credibility.
- Preserve intent across sessions (recent searches, alerts, price tracking).

---

## 3) The core funnel (macro)
**A. Intent capture** → **B. Search** → **C. Result scan** → **D. Narrow** → **E. Select itinerary** → **F. Choose partner** → **G. Booking handoff**

Funnel reality:
- The funnel is less about “purchase completion” (Skyscanner doesn’t own it) and more about **qualified click-outs**.

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

**Why this matters**
- Skyscanner’s job is to **reduce friction** for frequent searchers while still supporting long-tail complexity.

**Notable UX patterns**
- Autocomplete with airport disambiguation.
- Remembered recent searches.
- Date picker optimized for scanning cheapest dates (often via “month view” or “flexible”).


### 5.2 Search execution (loading)
**System goals**
- Provide a **fast initial set** of results, then stream/improve.
- Handle stale/partial pricing gracefully.

**User expectation**
- “Show me something quickly; I’ll refine.”


### 5.3 Search results list (SRP)
This is the **highest-leverage screen**.

**Core SRP objects**
- Itinerary card: total price, duration, stops, departure/arrival times
- Airline(s)
- Badges: “Best”, “Cheapest”, “Fastest” (or variants)

**The metasearch tradeoff**
- You must show enough detail to support a choice, without overwhelming.

**Common SRP patterns**
- Sticky sort + filter bar
- Price graph/calendar (date flexibility)
- “Best” ranking combining price + duration + stops (Skyscanner’s secret sauce is ranking)

**Why “Best” matters**
- It’s both a **decision simplifier** and a **power lever** for marketplace optimization.


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

**Design tension:**
- More filters = higher decision quality, but also higher cognitive load and “no results” cliffs.


### 5.5 Itinerary detail + partner selection
When a user taps an itinerary, they usually see:
- Full legs, layovers
- Baggage rules summary
- Price breakdown (sometimes)
- A list of **partners** (airlines/OTAs) offering the same itinerary at different prices

**Partner list design goals**
- Maximize *trust* and *click-out conversion*.
- Avoid “bait price” perception.

**Common mechanisms**
- “Best deal” highlight
- Partner rating/labeling (“Official”, “Trusted”, “Cheapest”, etc.)
- Clear disclosure: “You’ll be redirected to X to complete booking.”


### 5.6 Booking handoff (click-out)
**This is Skyscanner’s ‘moment of monetization’.**

Click-out payload typically includes:
- itinerary + fare class
- passenger count
- currency/locale
- tracking parameters (partner attribution)

**Success criteria for Skyscanner**
- Click-out (CTR)
- Downstream conversion (if CPA)
- Low partner mismatch complaints

---

## 6) Trust architecture (what Skyscanner must get right)
Metasearch trust problems:
1. **Price mismatch** at partner site (bait vs final).
2. **Baggage/refund confusion** (rules differ across partners).
3. **Partner credibility** (unknown OTA).
4. **Session decay** (user wants to compare later).

Skyscanner’s trust tools:
- Partner labels and/or ratings.
- Clear redirect disclosure.
- Highlighting “best” vs “cheapest” with tradeoff cues.
- Price alerts and saved trips to keep user in ecosystem.

---

## 7) Monetization model (practical)
### 7.1 Primary
- **Referral / CPA**: paid when booking converts at partner.
- **CPC / click-out**: paid per qualified click.

### 7.2 Secondary
- Sponsored placements / boosted partners.
- Ads (display/native) on SRP or inspiration surfaces.

### 7.3 The ranking lever
The “Best/Recommended” rank is where:
- user value (conversion, satisfaction)
- and marketplace value (revenue per session)
…compete.

A mature system optimizes for **long-term trust**, not short-term revenue.

---

## 8) Growth loops & retention hooks
- **Price alerts** (high-intent loop): alert → reopen → search → click-out.
- **Recent searches / saved trips**: reduces future friction.
- **Flexible dates / Everywhere**: creates discovery sessions that can convert later.

---

## 9) Key metrics (what a Skyscanner team likely watches)
### Acquisition & activation
- Search starts / DAU
- % sessions that reach SRP

### Marketplace efficiency
- SRP click-through to itinerary detail
- Partner click-out rate
- Revenue per search/session (RPS)

### Trust & quality
- Price mismatch rate (user-reported + inferred)
- Partner complaint rate / refund disputes (proxy)
- Repeat search rate (7/30-day)

### Performance
- Time to first result (TTFR)
- SRP render p95

---

## 10) What’s notably “great” in this product (strengths)
1. **Massive choice, still usable**: SRP design + ranking reduce overwhelm.
2. **Strong discovery affordances**: flexible dates/explore.
3. **Clear handoff model**: user understands Skyscanner is the comparison layer.
4. **Retention through alerts**: converts indecision into future return.

---

## 11) What’s structurally hard (weaknesses / product debt)
1. **Price accuracy is a constant battle** (partners, caching, fare volatility).
2. **Post-click experience is out of Skyscanner’s control**.
3. **Incentive misalignment risk**: short-term monetization can degrade trust.
4. **Information loss on redirect**: users must re-verify details on partner site.

---

## 12) Opportunities (product bets)
### 12.1 “Trust Pack” for metasearch (analogous to our UPI work)
- Price confidence score (“price likely to change”)
- Baggage clarity badge (normalized)
- Partner reliability index (mismatch rate + cancellation issues)

### 12.2 Smarter comparisons
- True total trip cost (bags + seat + refundability where possible)
- Time-cost tradeoff visualization (₹ per hour saved)

### 12.3 Reducing redirect pain
- “Preflight” confirmation screen: recap itinerary + baggage + refund rules + partner terms.
- Deep-link back from partner (post-booking) to store booking in Trips (if partnerships allow).

### 12.4 Better intent persistence
- Compare sets (user selects 2–4 itineraries, get a side-by-side table)
- Shareable comparison link

---

## 13) Risks & pitfalls (if you were building a competitor)
- Underinvesting in **data quality / fares freshness**.
- Not having strong **partner governance**.
- Overcomplicating filters leading to “no flights found” dead-ends.
- Optimizing ranking for revenue at the expense of trust.

---

## 14) If I had to summarize the system in one sentence
Skyscanner is a **ranking + trust + marketplace routing** system wrapped in a flight search UI.

---

## 15) Open questions for v2
1. What are Skyscanner’s specific “Best” ranking ingredients (price/duration/stops/CO2/partner quality)?
2. How do they message and measure price mismatch, and what controls exist per partner?
3. How are baggage and refund rules normalized across partners?
4. How are price alerts tuned to avoid notification fatigue while maximizing return?
5. What are the biggest drop-off points: SRP → itinerary detail, or itinerary → click-out?
