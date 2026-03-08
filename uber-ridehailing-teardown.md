# Uber — Ride-hailing Product Teardown (v1)

> Lens: Product Manager • Scope: **publicly observable behavior only** (no internal Uber data claims)

---

## Version history
- **v1 (current):** baseline framing (core loop, marketplace funnel, north star metrics, trade-offs, initial opportunities).

---

## Product in one sentence
Uber matches **riders with nearby drivers** and coordinates the full trip (pricing, ETA, routing, payment, trust/safety) to deliver a reliable “tap → ride” transportation utility.

---

## 1) Who it serves (two-sided market)
### Demand (riders)
Observable rider segments by intent:
1. **Commute / routine**: repeat routes, time sensitivity, price sensitivity.
2. **Airport / long trips**: higher willingness-to-pay, luggage, reliability > price.
3. **Late-night / safety-sensitive**: trust, pickup clarity, safety features.
4. **Tourists / unfamiliar areas**: address entry, language friction, pickup confusion.
5. **Group rides**: need larger vehicle, split fare.

### Supply (drivers)
1. **Full-time earners**: utilization, predictable earnings, low downtime.
2. **Part-time / opportunistic**: flexible hours, easy start/stop.
3. **Multi-apping drivers**: will switch based on near-term earnings and friction.

PM implication: the product is not a single funnel—it’s **liquidity management** across time/geo.

---

## 2) What Uber promises (value props → measurable)
For riders:
- **Fast availability** → pickup ETA, match time
- **Price transparency** → upfront fare accuracy vs final fare, fare disputes
- **Reliability** → trip completion rate, cancellation rates (both sides)
- **Safety & trust** → incident reports per trip, safety feature usage, rating distributions
- **Low friction** → time-to-book, payment success

For drivers:
- **Earning power** → earnings/hour, earnings/trip, utilization
- **Low downtime** → idle minutes between trips
- **Fairness / predictability** → earnings variance, dispute resolution time

---

## 3) The core loop (marketplace loop)
A useful “v1” loop:

**Rider intent → Request → Match → Pickup → Trip → Payment → Rating → Future intent returns**

The loop is only healthy when both sides win:
- Riders get short ETAs and acceptable prices
- Drivers get enough paid minutes and fair earnings

Key loop metrics (north-star-adjacent):
- **Request-to-match rate**
- **Median match time** (p50/p90)
- **Pickup ETA accuracy** (predicted vs actual)
- **Trip completion rate**
- **Rider cancellation rate** (pre-match and post-match)
- **Driver cancellation rate**
- **Re-order rate** (next 7/30 days)

---

## 4) Funnels (make the marketplace explicit)
### 4.1 Rider funnel (single trip)
1. Open app
2. Set pickup location
3. Set destination
4. See price + ETA options
5. Select product (X/Comfort/XL etc.)
6. Confirm
7. **Match**
8. **Pickup**
9. **Trip complete**
10. Rate / tip

Where drop-offs happen (typical):
- Pickup pin uncertainty
- Sticker shock (surge)
- Long ETAs / “no cars available”

### 4.2 Driver funnel (supply session)
1. Go online
2. Receive request
3. Accept
4. Navigate to pickup
5. Pickup success
6. Complete trip
7. Repeat / stay online

Drop-offs:
- Low earnings expectation
- Bad pickup experiences (hard-to-find riders)
- Long deadhead distances

### 4.3 Activation (practical definitions)
Riders:
- **A1:** completes first trip within 7 days of install
- **A2:** completes 2nd trip within 30 days (early retention)

Drivers:
- **D1:** completes first trip within 14 days of signup
- **D2:** completes ≥N trips in first 30 days (indicates habit)

---

## 5) North Star + supporting metrics (v1 framing)
A workable North Star for the core ride-hailing product:

**Weekly Completed Trips (WCT)** with healthy experience quality.

Supporting metrics (guardrails + drivers):
- **Coverage / liquidity:** % requests served, supply hours by geo/time
- **Speed:** p50/p90 match time, pickup ETA
- **Reliability:** completion rate, cancel rate, on-time pickup
- **Economics:** take rate (conceptually), rider price index, driver earnings/hour
- **Trust:** incident rate, fraud rate, dispute rate, rating health

---

## 6) Key product trade-offs (what’s structurally hard)
1. **Low prices vs driver earnings**
   - Lower prices increase demand; higher driver earnings increase supply.
   - The platform must balance both, often via dynamic pricing/incentives.

2. **Fast matching vs pickup efficiency**
   - Nearest-driver matching reduces ETAs but can create bad pickups (wrong side of highway, gated complexes).

3. **Surge pricing vs perceived fairness**
   - Surge solves liquidity but can feel exploitative; transparency and predictability matter.

4. **Strict safety controls vs booking friction**
   - More verification and checks reduce harm but increase funnel drop-offs.

5. **Marketplace efficiency vs user control**
   - Too much driver choice (or rider choice) can slow matching.

---

## 7) What I’d prioritize (initial opportunities)
### Opportunity 1: Pickup precision (reduce cancels, improve ETAs)
Why: pickup is the highest-leverage failure point.
Ideas (public-UX consistent):
- Better pickup pin UX (landmarks, “which side of road”, gate codes)
- Real-time pickup confirmation (“I’m at the blue gate” prompts)

Measure:
- Rider/driver cancel rate ↓, pickup time ↓, pickup ETA accuracy ↑

### Opportunity 2: Price transparency at decision time
Why: sticker shock drives drop-off.
Ideas:
- Explain “why price is high right now” + “wait & save” suggestions
- Price confidence indicator (how likely fare changes)

Measure:
- Set-destination → request conversion ↑, fare dispute rate ↓

### Opportunity 3: Reliability for high-stakes trips (airport mode)
Why: some trips are reliability-first.
Ideas:
- “On-time guarantee” style packaging (where feasible)
- Earlier driver assignment / scheduled ride UX improvements

Measure:
- On-time pickup ↑, airport segment retention ↑

---

## 8) Minimal instrumentation plan (PM basics)
Event taxonomy (illustrative):
- rider_*: app_open, pickup_set, destination_set, product_viewed, request_started, request_confirmed, match_found, driver_arrived, trip_started, trip_completed, trip_canceled
- driver_*: go_online, request_received, request_accepted, pickup_arrived, pickup_success, trip_started, trip_completed, trip_canceled
- pricing_*: fare_shown, surge_shown, wait_and_save_shown, fare_changed
- trust_*: report_submitted, safety_tool_used, identity_verification_step

Always log time deltas:
- open→request, request→match, match→pickup, pickup→start, start→complete
