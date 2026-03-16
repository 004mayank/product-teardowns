<p align="center">
  <img src="images/uber.png" alt="Uber logo" width="400"/>
</p>


# Uber - Ride-hailing Product Teardown

> Lens: Product Manager • Scope: **publicly observable behavior only** (no internal Uber data claims)

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

PM implication: this is not a single funnel-it’s **liquidity management** across time/geo with two sets of incentives.

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
- **Fairness / predictability** → earnings variance, incentive comprehension, dispute resolution time

---

## 3) Core loops (v3: add the “control system” view)
Uber isn’t just a loop; it’s a **closed-loop controller** that continuously adjusts matching, ETAs, and prices/incentives to keep service levels stable.

### 3.1 Match loop (liquidity)
**Rider intent → Request → Driver offer → Accept → Match → Pickup ETA shown**

Metrics:
- **Request-to-match rate** (% of requests that match)
- **Median match time** (p50/p90)
- **Driver acceptance rate** (offers accepted / offers shown)
- **Long-ETA share** (requests with ETA > threshold)

### 3.2 Pickup coordination loop (highest-friction)
**Driver navigates → Arrives → Rider locates car → Pickup succeeds**

Metrics:
- **Arrive→Start time** (p50/p90)
- **Pickup failure rate** (arrived but canceled / no-show)
- **Pickup confusion signals** (chat/call usage rate, reroutes, GPS drift)

### 3.3 Pricing & incentives loop (stability)
**Demand spike → price rises / incentives appear → supply increases → ETAs normalize**

Metrics:
- **Conversion at checkout** (destination_set → request_confirmed)
- **Surge exposure rate** and **surge abandonment rate**
- **Supply response** (drivers online / repositioning after incentive)

Guardrails:
- Rider trust: pricing complaint/dispute rate
- Driver trust: incentive payout dispute rate

### 3.4 Trust & safety loop (retention)
**Safe trips → confidence → repeat; incidents → churn**

Metrics:
- **Incidents per 100k trips** (category-based)
- **Safety tool usage rate** (share trip, emergency assist, PIN where present)
- **Rating health** (low-rating concentration by geo/time)

### 3.5 Support & dispute loop (hidden but critical)
**Problem → contact support → resolution → trust restored (or churn)**

Metrics:
- **Contact rate per 1k trips** (by issue category)
- **Time to first response / time to resolution**
- **Refund rate** and **appeal rate**
- **Post-resolution retention** (next-30d trip probability after case)

PM implication: the support loop is a *quality backstop*; when it degrades, it amplifies churn and distrust faster than most “in-app” changes can compensate.

---

## 4) Funnels (make the marketplace explicit)
### 4.1 Rider funnel (single on-demand trip)
1. Open app
2. Set pickup
3. Set destination
4. See price + ETA options
5. Select product (X/Comfort/XL etc.)
6. Confirm
7. Match
8. Pickup
9. Trip complete
10. Rate / tip

Typical drop-offs:
- Pickup pin uncertainty
- Sticker shock (surge)
- Long ETAs / “no cars available”

### 4.2 Rider re-activation funnel (lapsed)
1. Trigger (push/email/deep link/promo)
2. Open app
3. Destination set (or repeat destination)
4. Price/ETA acceptable
5. Request confirmed

Metrics:
- Open→destination_set conversion
- destination_set→request_confirmed conversion

### 4.3 Scheduled ride funnel (higher intent)
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

## 5) North Star + scorecards (v3: separate “volume” from “quality”)
### North Star (volume)
**Weekly Completed Trips (WCT)**

### Rider Experience Scorecard (guardrails)
- p90 **match time**
- p90 **pickup ETA** and p90 **arrive→start**
- **post-match cancel rate** (rider + driver)
- **support contact rate**
- **pricing dispute rate**

### Driver Experience Scorecard (guardrails)
- **earnings/hour** (net proxy if available), earnings variance
- **paid-mile share** (paid miles / total miles)
- **deadhead minutes** per completed trip
- **offer accept rate** (by geo/time)
- **payout dispute rate**

Why scorecards: WCT can be “bought” (by pushing price down or over-incentivizing) while quietly destroying trust. The scorecards prevent that.

---

## 6) Cancellation taxonomy (v3: treat cancels like a product)
Cancellations are the most expensive UX failure because they waste time on both sides and create distrust.

### 6.1 Rider cancels (common root causes)
- **ETA shock** (ETA worsened after request)
- **Price regret** (surge discomfort)
- **Pickup confusion** (wrong pin, complex pickup)
- **Rider not ready** (multi-tasking)

Mitigations:
- ETA confidence bands (“likely pickup 6–8 min”)
- “Wait & Save” / alternatives before confirm
- Guided pickup pin + landmark confirmation
- Lightweight “I’m coming out now” readiness prompt

### 6.2 Driver cancels (common root causes)
- **Low expected earnings** (short trips, traffic)
- **Hard pickup** (gates, unsafe stops)
- **Multi-apping opportunity cost**

Mitigations:
- Pickup difficulty signals + compensation
- Reduced deadhead via smarter dispatch / staging
- Penalties only when coupled with fairness + clarity (avoid adversarial dynamics)

---

## 7) Fraud / abuse surfaces (publicly observable)
You don’t need internal data to know these exist; you design for them.

Rider-side examples:
- False “driver didn’t pick me up” claims
- Chargebacks / payment failures

Driver-side examples:
- GPS spoofing / trip manipulation attempts
- Collusion around surge zones (hard to prove; design deterrents)

Platform mitigations (product-facing):
- Strong receipts, trip timelines, and transparent dispute flows
- Anomaly detection + friction only for high-risk cohorts (protect baseline conversion)

---

## 8) Key product trade-offs (still true, sharper framing)
1. **Low prices vs driver earnings** (demand vs supply)
2. **Fast matching vs pickup quality** (speed vs rendezvous success)
3. **Surge vs perceived fairness** (stability vs trust)
4. **Safety controls vs booking friction** (harm reduction vs conversion)
5. **Automation vs explainability** (efficiency vs “why did this happen?”)

---

## 9) What I’d build/test next (v3: a focused plan)
### Bet 1: Pickup Quality Pack (reduce post-match cancels)
Features:
- Guided pin: landmark + side-of-road + gate/entry hints
- Driver-facing pickup difficulty cue + route to best stopping point
- “I’m here” state machine with clearer rendezvous cues

Success:
- Post-match cancel rate ↓
- Arrive→Start p90 ↓
- Support contact rate about pickup ↓

### Bet 2: ETA Confidence + Requote Transparency
Features:
- ETA range + “confidence” indicator
- If ETA changes materially: explicit “requote” moment (with easy cancel before wasting time)

Success:
- Rider cancels due to ETA shock ↓
- Ratings health ↑

### Bet 3: Support-first Trust Repair (fast resolution for the top 3 issues)
Start with the biggest-volume issues (typically: pricing questions, cancellations, lost item / trip problems).

Success:
- Time-to-resolution ↓
- Repeat trips after case ↑

Guardrails (all bets):
- Checkout conversion doesn’t fall
- Driver acceptance doesn’t fall in affected geos

---

## 10) Instrumentation (PM-usable)
Event taxonomy (illustrative):
- rider_trip_*: app_open, pickup_set, destination_set, product_viewed, request_started, request_confirmed, match_found, driver_arrived, trip_started, trip_completed, trip_canceled
- driver_supply_*: go_online, go_offline, request_received, request_accepted, request_rejected, pickup_arrived, trip_started, trip_completed, trip_canceled
- pricing_*: fare_shown, surge_shown, wait_and_save_shown, fare_changed
- trust_*: safety_tool_used, report_submitted, dispute_opened, dispute_resolved
- support_*: help_opened, contact_initiated, agent_assigned, refund_issued, case_resolved

Always log time deltas:
- open→destination_set
- destination_set→request_confirmed
- request_confirmed→match_found
- match_found→driver_arrived
- driver_arrived→trip_started
- trip_started→trip_completed
- driver_arrived→trip_canceled (pickup failure shape)
