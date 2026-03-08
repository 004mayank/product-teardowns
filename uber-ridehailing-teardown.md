# Uber — Ride-hailing Product Teardown

> Lens: Product Manager • Scope: **publicly observable behavior only** (no internal Uber data claims)

---

## Version history
- **v2 (current):** expanded loops (pickup, pricing, safety), added reactivation + scheduled rides funnel, explicit metric definitions/guardrails, diagnostics, and experiment backlog (building on v1).
- **v1:** baseline framing (core loop, marketplace funnel, north star metrics, trade-offs, initial opportunities).

---

## Product in one sentence
Uber matches **riders with nearby drivers** and coordinates the full trip (pricing, ETA, routing, payment, and trust/safety) to deliver a reliable “tap → ride” transportation utility.

---

## 1) Who it serves (two-sided market)
### Demand (riders)
Observable rider segments by intent:
1. **Commute / routine**: repeat routes, time sensitivity, price sensitivity.
2. **Airport / long trips**: higher willingness-to-pay, luggage, reliability > price.
3. **Late-night / safety-sensitive**: trust, pickup clarity, share-trip features.
4. **Tourists / unfamiliar areas**: address entry friction, pickup confusion, language.
5. **Group rides**: larger vehicle needs, **Split Fare**, multiple stops.
6. **Constrained mobility users** (wheelchair accessible / accessibility needs where available): higher sensitivity to reliability and pickup UX.

### Supply (drivers)
1. **Full-time earners**: utilization, predictable earnings, low downtime.
2. **Part-time / opportunistic**: flexible hours, rapid start/stop.
3. **Multi-apping drivers**: will switch based on near-term earnings and friction.
4. **Location-constrained drivers** (prefer certain areas/times): acceptance behavior shaped by deadhead distance, safety, and expected fares.

PM implication: this is not a single funnel—it’s **liquidity management** across time/geo with two sets of incentives.

---

## 2) What Uber promises (value props → measurable)
For riders:
- **Fast availability** → request→match time, pickup ETA
- **Price transparency** → upfront fare accuracy vs final, dispute rate
- **Reliability** → trip completion rate, cancellations (both sides)
- **Safety & trust** → incident reports per trip, safety tool usage, rating health
- **Low friction** → time-to-book, payment success

For drivers:
- **Earning power** → earnings/hour, earnings/trip, paid-mile share
- **Low downtime** → idle minutes between trips, dispatch efficiency
- **Fairness / predictability** → earnings variance, dispute resolution time

---

## 3) The core loops (v2: break the marketplace into loops)
### 3.1 Match loop (liquidity loop)
**Rider intent → Request → Driver accept → Match → Pickup ETA shown**

Metrics:
- **Request-to-match rate** (% of requests that match)
- **Median match time** (p50/p90)
- **Driver acceptance rate** (offers accepted / offers shown)
- **Supply-demand imbalance indicators** (e.g., “no cars available” rate, long ETA share)

### 3.2 Pickup coordination loop (highest-friction moment)
**Driver navigates → Arrives → Rider finds car → Pickup succeeds**

Metrics:
- **Arrive→Start time** (p50/p90)
- **Pickup failure rate** (driver arrived but trip canceled / no-show)
- **Wrong-pickup / wrong-side-of-road proxies** (manual reports, reroute events)

### 3.3 Pricing & incentives loop (stability loop)
**Demand spike → price rises / incentives appear → more supply comes online → ETAs normalize**

Metrics:
- **Conversion at checkout** (destination set → request_confirmed)
- **Surge exposure rate** and **surge abandonment rate**
- **Driver online hours response** to incentives (by geo/time)

Guardrails:
- Rider trust: complaints/disputes about pricing
- Driver trust: incentive comprehension and payout dispute rate

### 3.4 Trust & safety loop (long-term retention loop)
**Safe trip → confidence → repeat usage; unsafe incident → churn**

Metrics:
- **Incidents per 100k trips** (category-based)
- **Safety tool usage rate** (share trip, emergency assist, PIN verification where present)
- **Rating health** (distribution skew, low-rating concentration by geo/time)

---

## 4) Funnels (make the marketplace explicit)
### 4.1 Rider funnel (single on-demand trip)
1. Open app
2. Set pickup
3. Set destination
4. See price + ETA options
5. Select product (X/Comfort/XL etc.)
6. Confirm
7. **Match**
8. **Pickup**
9. **Trip complete**
10. Rate / tip

Typical drop-offs:
- Pickup pin uncertainty
- Sticker shock (surge)
- Long ETAs / “no cars available”

### 4.2 Rider re-activation funnel (lapsed)
1. Trigger (push, email, deep link, promo)
2. Open app
3. Destination set (or repeat destination suggestion)
4. Price/ETA acceptable
5. **Request confirmed**

Metrics:
- Open→destination_set conversion
- destination_set→request_confirmed conversion

### 4.3 Scheduled ride funnel (higher intent, reliability-first)
1. Schedule intent (airport / morning commute)
2. Set time window
3. Price/terms understood
4. Scheduled booking created
5. Day-of: driver assigned + pickup success

Metrics:
- Schedule creation rate
- Day-of assignment success
- On-time pickup rate (scheduled)

### 4.4 Driver funnel (supply session)
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

### 4.5 Activation (practical definitions)
Riders:
- **A1 (shallow):** completes first trip within 7 days of install
- **A2 (meaningful):** completes 2nd trip within 30 days
- **A3 (sticky):** completes ≥3 trips or uses in ≥2 distinct days in first 30 days

Drivers:
- **D1:** completes first trip within 14 days of signup
- **D2:** completes ≥N trips or ≥H online hours in first 30 days

---

## 5) North Star + supporting metrics
A workable North Star for core ride-hailing:

**Weekly Completed Trips (WCT)** with healthy experience quality.

Supporting drivers + guardrails:
- **Coverage / liquidity:** % requests served, supply hours by geo/time
- **Speed:** p50/p90 match time, pickup ETA, arrive→start time
- **Reliability:** completion rate, cancel rate (rider/driver), no-show rate
- **Economics (observational framing):** price index, driver earnings/hour, paid-mile share
- **Trust:** incident rate, fraud rate, dispute rate, rating health

---

## 6) Key product trade-offs (what’s structurally hard)
1. **Low prices vs driver earnings**
   - Lower prices increase demand; higher driver earnings increase supply.
   - The platform must balance both, often via dynamic pricing/incentives.

2. **Fast matching vs pickup efficiency**
   - Nearest-driver matching reduces ETAs but can create bad pickups (complex intersections, gated areas).

3. **Surge pricing vs perceived fairness**
   - Surge solves liquidity but can feel exploitative; transparency and predictability matter.

4. **Strict safety controls vs booking friction**
   - More verification and checks reduce harm but increase funnel drop-offs.

5. **Marketplace efficiency vs user control**
   - Too much choice can slow matching; too little can reduce trust (“why this driver/route/price?”).

---

## 7) Diagnostics: where things break (v2)
1. **Long match times**
   - Likely: supply shortage, low driver acceptance due to low expected earnings, or deadhead distance.
   - Watch: request-to-match rate, acceptance rate, p90 match time.

2. **High cancellation after match**
   - Likely: pickup confusion, rider not ready, driver changed mind, traffic.
   - Watch: post-match cancel rate split by rider vs driver, arrive→start time.

3. **Checkout abandonment**
   - Likely: surge, poor ETA, lack of price confidence.
   - Watch: destination_set→request_confirmed conversion, surge abandonment.

4. **Trust incidents / perception drift**
   - Likely: safety events, scams, poor support resolution.
   - Watch: incident rate, dispute rate, support time-to-resolution proxies.

---

## 8) What I’d test (experiment backlog)
### Experiment 1: Pickup precision upgrade (reduce cancels, improve ETAs)
- Guided pickup pin: landmark selection + “which side of road” micro-choices
- Stronger driver↔rider rendezvous cues (e.g., confirm clothing/car color, clearer “I’m here” states)

Success:
- Post-match cancel rate ↓, arrive→start time ↓, pickup failures ↓

### Experiment 2: Surge transparency + “wait & save” as a first-class choice
- Explain why price is high right now and offer a clear wait-time trade
- Add a price confidence indicator (how likely fare changes)

Success:
- destination_set→request_confirmed conversion ↑ at same supply health, disputes ↓

### Experiment 3: Reliability mode for airport / high-stakes trips
- Make scheduled rides clearer; reduce day-of uncertainty (assignment notifications, arrival confidence)

Success:
- On-time pickup ↑, airport segment repeat rate ↑

### Experiment 4: Driver acceptance improvement via pickup-quality signals
- Show pickup difficulty cues (complex intersections, gates) and compensate/route accordingly

Success:
- Acceptance rate ↑ in hard-pickup zones, pickup failures ↓

### Experiment 5: Re-activation with intent shortcuts
- Smart “repeat destination” + “time-to-leave” suggestions for commuters

Success:
- Lapsed open→request conversion ↑, A2/A3 retention ↑

---

## 9) Instrumentation plan (PM-usable)
Event taxonomy (illustrative):
- rider_onboarding_*: signup_completed, payment_added
- rider_trip_*: app_open, pickup_set, destination_set, product_viewed, request_started, request_confirmed, match_found, driver_arrived, trip_started, trip_completed, trip_canceled
- rider_scheduled_*: schedule_started, schedule_confirmed, schedule_canceled
- driver_supply_*: go_online, go_offline, request_received, request_accepted, request_rejected, pickup_arrived, pickup_success, trip_started, trip_completed, trip_canceled
- pricing_*: fare_shown, surge_shown, wait_and_save_shown, fare_changed, promo_applied
- trust_*: report_submitted, safety_tool_used, identity_verification_step, dispute_opened, dispute_resolved

Always log time deltas:
- open→destination_set
- destination_set→request_confirmed
- request_confirmed→match_found
- match_found→driver_arrived
- driver_arrived→trip_started
- trip_started→trip_completed
