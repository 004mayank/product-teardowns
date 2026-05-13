# Stripe - Product Teardown

**Version:** v3 - Final teardown
**Changes from v2:** Resolved all four v2 open questions (crypto/stablecoin rail, Sigma data products, Terminal/unified commerce, LLM-native API). Added strategic vectors section (2025-2027), metrics framework, 30-60-90 day PM plan, enhanced experiment backlog with rollout phases and acceptance criteria, and version history update.
**Lens:** Product Manager - Scope: publicly observable product behaviour, developer experience, and inferred business logic

---

## Version history

| Version | Key additions |
|---|---|
| v1 | Core thesis, positioning, product model, core loops, business model, competitive landscape, trade-offs, open questions |
| v2 | Activation funnel, event schemas, loop edge cases, competitive benchmarks, open question resolution, authorisation rate mechanics, Connect mix, Capital & AI billing, experiment backlog |
| v3 | Resolved v2 open questions (crypto rail, Sigma, Terminal, LLM API), strategic vectors 2025-2027, metrics framework, PM 30-60-90 plan, enhanced experiment backlog with acceptance criteria |

---

## TL;DR

Stripe won the payments market not by building a cheaper payment processor, but by solving the developer's problem first. Every competitor before Stripe made you sign a merchant agreement, wait two weeks, and then integrate a SOAP API from 2004. Stripe made it seven lines of code and a test key you get in 30 seconds. The developer experience was the product. Now, a decade later, the dev experience is the trojan horse. Stripe's real moat is a compounding stack: the fraud signal network (millions of merchants' transaction data), the merchant identity graph (verified business + banking relationships for millions of companies), and the financial services rails built on top of that trusted identity. Stripe's strategic bet is that every internet business eventually becomes a financial services business - and Stripe wants to own that moment with products like Connect, Issuing, Treasury, and Radar. The risk is that it's building a financial conglomerate with the UX of a startup, and enterprise sales motions are fundamentally different from PLG API adoption.

In 2024-2025 Stripe made three bets that define the next decade: (1) the $1.1B acquisition of Bridge to own stablecoin payment rails before they become commodity infrastructure, (2) expanded LLM-agent-friendly API surfaces targeting agentic AI workflows, and (3) Stripe Terminal's quiet evolution toward unified commerce. The thesis is no longer just "payments for the internet" - it is "financial infrastructure for both human and AI-operated commerce."

---

## 1) Positioning

**What it is:** Stripe is a financial infrastructure platform that enables internet businesses to accept payments, manage subscriptions, run marketplaces, issue cards, embed banking, and handle global compliance - all via developer-first APIs.

**Core promise:** Go from zero to accepting payments in minutes, then scale to global financial infrastructure without switching vendors.

**Design principle:** The API is the product. Every Stripe surface - Dashboard, Radar, Connect, Billing - is a wrapper around an API-first data model. If a PM can't explain a feature in terms of its API objects, the feature doesn't exist yet in Stripe's world.

**Why it wins:** Before Stripe, payment integration meant weeks of paperwork, opaque merchant account fees, and fragile SOAP integrations maintained by specialists. Stripe collapsed that to a single API key, PCI compliance handled by default, and documentation that a junior developer could follow. It won on time-to-first-payment - the critical moment a developer ships something that makes real money.

**PM thesis:** Stripe's primary expansion vector is not acquiring new payment volume - it is increasing the share of financial operations each merchant runs through Stripe. A merchant who uses Payments + Billing + Radar + Connect + Issuing generates 5-8x the revenue of a Payments-only merchant. The product strategy is to be the complete financial stack for internet businesses so that switching any one piece means switching all of it.

### Target users

| Segment | Primary JTBD | Activation trigger | Revenue model fit |
|---|---|---|---|
| Solo developer / indie hacker | Accept payments for a side project without paperwork | Copy-paste Stripe.js snippet; first test charge succeeds | Low ARPU; high word-of-mouth; seed for future business growth |
| Early-stage startup (seed to Series A) | Go live with payments in days, not weeks | First live charge; first webhook received | Grows with the startup; high LTV if retained through growth |
| SaaS company | Manage recurring subscriptions, trials, proration, and churn | `subscription` object created; first invoice sent | Billing add-on; high attachment rate |
| Marketplace / platform (Connect) | Enable third parties to accept payments through their platform | First connected account onboarded and first payout sent | Connect pricing premium; highest ARPU segment |
| Enterprise (large-volume merchant) | Optimise authorisation rates, reduce fraud, manage global tax and compliance | Custom contract; migration from existing processor | Interchange-plus pricing; dedicated support; highest margin |
| Non-technical business owner | Accept payments without writing code (via Stripe Checkout, Payment Links, Invoicing) | First Payment Link created and shared | Pays for no-code wrappers; lower churn than developer accounts |
| AI company / LLM product | Monetise token usage with metered or credit-based billing | First API call billed; first usage record submitted | High-growth volume; strong attach for Billing metered features |

*Revenue model fit is inferred from public information, Stripe pricing pages, and industry benchmarks - not internal Stripe data.*

### When Stripe wins vs. loses

**Wins when:**
- Speed to first payment is critical - a developer can go live in a day with Stripe vs. 2-4 weeks with legacy processors.
- International expansion matters - Stripe supports 135+ currencies and 50+ countries natively, handling local payment methods (SEPA, iDEAL, Alipay, etc.) in a single integration.
- SaaS billing complexity is high - trials, prorations, metered usage, seats, and coupons are first-class objects in the Billing product.
- Marketplace or platform model requires splitting payments to sub-merchants - Connect is purpose-built for this; competitors force custom workarounds.
- Fraud is a live concern - Radar uses cross-network ML trained on millions of Stripe merchants' transactions; no single-merchant fraud system can match its signal density.
- The team values API quality and documentation - Stripe's developer experience is the strongest in the category.
- The product is AI-native - Stripe already processes payments for most large AI companies and is building primitives for token-based and agentic commerce.

**Loses when:**
- Processing volume is extremely high (>$1B/year) and a merchant can negotiate better rates directly with acquiring banks - Adyen's interchange-plus direct model offers lower effective rates for the highest-volume merchants.
- Point-of-sale hardware is the primary channel - Square wins on terminal hardware, retail workflows, and the integrated Square POS ecosystem.
- The company is deeply embedded in a SAP/Oracle ERP ecosystem - legacy processor integrations at this layer are deeply entrenched; Stripe's enterprise play is still maturing.
- Local market dominance requires local payment method depth that Stripe hasn't prioritised - Razorpay in India, Mercado Pago in Latin America, and WeChat/Alipay direct integrations still outperform Stripe in their home markets.
- The procurement process is finance-team led, not developer led - Stripe's PLG motion has less leverage when a CFO rather than an engineer makes the vendor decision.

---

## 2) Product model (objects + primitives)

### Core API objects

| Object | Role | Key behaviour |
|---|---|---|
| `PaymentIntent` | Represents a single payment attempt | Stateful; tracks the full lifecycle of a payment from creation to confirmation or failure |
| `Customer` | Represents a reusable payer | Stores payment methods, default source, email, and metadata; the anchor for Billing subscriptions |
| `PaymentMethod` | Stored payment instrument | Card, bank account, wallet; attached to a Customer or used one-time; replaces legacy `Card` and `Source` objects |
| `Charge` | Record of a completed payment capture | Immutable record created when a PaymentIntent is confirmed; source of truth for financial ledger |
| `Subscription` | Recurring billing plan | Links a Customer, a Price, and a billing interval; drives invoice generation, trial logic, and proration |
| `Invoice` | Billable document generated per period | Auto-generated by Subscriptions; or manual for usage-based or one-off billing |
| `Price` | Pricing configuration | Amount, currency, interval, and billing scheme (flat, per-seat, tiered, metered) attached to a Product |
| `Product` | What is being sold | Top-level entity; has one or many Prices; used across Checkout, Payment Links, and Billing |
| `Account` (Connect) | A sub-merchant on a platform | Standard, Express, or Custom; has its own Stripe account managed through the platform's Connect integration |
| `Transfer` / `Payout` | Moving funds to a connected account or bank | Transfer moves funds between Stripe accounts (Connect); Payout sends funds to an external bank |
| `Radar Rule` | Custom fraud logic | IF-THEN logic written in Stripe Radar's rule language; layered on top of ML model scores |
| `Webhook Event` | Async notification of state change | Every significant state change in Stripe emits an event; merchant systems subscribe and react |
| `Meter` | Usage tracking unit for AI/usage-based billing | Introduced 2024; tracks API-level events and aggregates for billing; the foundation of Stripe's AI billing primitive |

### Key event schema

Every Stripe event follows a consistent envelope. Understanding this schema is essential for any integrator building reliable downstream workflows.

```json
{
  "id": "evt_1OxNxXLkdIwHu7ixY2hKJtFs",
  "object": "event",
  "api_version": "2023-10-16",
  "created": 1713811200,
  "type": "payment_intent.succeeded",
  "livemode": true,
  "pending_webhooks": 3,
  "request": {
    "id": "req_abc123",
    "idempotency_key": "order_123_attempt_1"
  },
  "data": {
    "object": {
      "id": "pi_3OxNxXLkdIwHu7ix0BEkBNZC",
      "object": "payment_intent",
      "amount": 2000,
      "amount_received": 2000,
      "currency": "usd",
      "status": "succeeded",
      "customer": "cus_PXqF8HJkLm9nOp",
      "payment_method": "pm_1OxNwXLkdIwHu7ix6R4TfZ2k",
      "metadata": {
        "order_id": "ord_9871",
        "user_id": "usr_4421"
      }
    }
  }
}
```

**Critical event types for a well-integrated merchant:**

| Event | When it fires | Correct merchant action |
|---|---|---|
| `payment_intent.succeeded` | Payment captured successfully | Fulfill order; update internal ledger |
| `payment_intent.payment_failed` | Payment declined or errored | Notify customer; offer retry or alternative method |
| `invoice.payment_failed` | Subscription renewal failed | Trigger dunning sequence; set account to grace period |
| `customer.subscription.deleted` | Subscription cancelled or expired | Revoke access; trigger win-back flow |
| `customer.subscription.updated` | Plan changed, trial ended, or status changed | Re-provision access; update billing record |
| `charge.dispute.created` | Cardholder filed a chargeback | Immediately begin evidence collection; respond within 7 days |
| `account.updated` (Connect) | Connected account status changed | Check `payouts_enabled` and `charges_enabled`; gate flows accordingly |
| `payout.failed` | Payout to bank failed | Alert connected account owner; re-trigger or redirect payout |
| `billing.meter.error_report_triggered` | Usage event ingestion error for metered billing | Alert engineering team; replay missed events to prevent revenue leakage |

### Key primitives

- **API key (publishable + secret):** The entire integration model in two keys. Publishable key is safe to expose client-side (used in Stripe.js); secret key is server-side only. First-time activation happens the moment the test key works.
- **Stripe.js / Stripe Elements:** Client-side JavaScript library that handles card input and tokenisation. PCI compliance is delegated to Stripe because card data never touches the merchant's server. This is the single most important product decision in Stripe's history - it made PCI compliance a non-problem for most merchants.
- **Webhooks:** Every event in Stripe emits a webhook. This is the async integration backbone - merchants subscribe to `payment_intent.succeeded`, `invoice.payment_failed`, `customer.subscription.deleted` etc. and react in their own systems. Reliable webhook delivery (with retries and signatures) is load-bearing infrastructure for the entire ecosystem.
- **Stripe Dashboard:** Web UI for non-developers. Dispute management, refunds, customer search, subscription management, revenue reports. The Dashboard is where a business owner lives; the API is where developers live. Both must be excellent because every Stripe account has both roles.
- **Idempotency keys:** Every mutating API call accepts an idempotency key. If the request is sent twice with the same key, Stripe returns the same response. This single primitive eliminates an entire class of double-charge bugs. It's a deeply thoughtful API design decision that signals Stripe's engineering philosophy.
- **Connect account types:** Standard (merchant has their own Stripe account; platform gets access), Express (Stripe handles onboarding/dashboard; platform controls the experience), Custom (platform owns the entire UX; Stripe is invisible). This three-tier model lets Stripe serve everything from simple referral integrations to fully embedded financial products.
- **Billing Meters:** The `Meter` object (GA in 2024) tracks usage events with idempotency, supports high-ingestion rates, and aggregates for billing. This is the foundational primitive for AI token billing, API call billing, and any real-time usage-based pricing model.

---

## 3) Activation funnel

### Developer-led activation funnel

This is the highest-volume funnel (PLG motion). It converts organic developer discovery into live payment volume.

```
Discover Stripe
(docs search, Hacker News, tweet, word-of-mouth)
         |
         v [Drop-off: ~40% never create an account]
Create account + verify email
         |
         v [Drop-off: ~25% stop here]
Retrieve test API keys
         |
         v [Drop-off: ~30% never attempt a test charge]
First successful test charge (PaymentIntent confirmed in test mode)
  --> AHA MOMENT #1
         |
         v [Drop-off: ~50% never activate live mode]
Activate live mode (business verification completed)
  --> ACTIVATION EVENT
         |
         v [Drop-off: ~40% process <$100 in first 30 days]
First live charge processed
  --> ACTIVATION EVENT #2 (monetisation begins)
         |
         v
$1,000 cumulative live volume
  --> Engaged cohort: high retention signal
         |
         v
Product expansion (Billing / Radar / Connect / Issuing)
  --> Compounding ARPU
```

**Funnel benchmarks (estimated, based on public signals and developer survey data):**

| Stage | Estimated conversion from prior step | Key lever |
|---|---|---|
| Discover -> Account created | ~60% | SEO on payment API terms; documentation quality |
| Account created -> Test charge | ~55% | Time-to-first-working-example (target: <60 min) |
| Test charge -> Live mode activated | ~50% | KYB friction (business verification); simplify identity step |
| Live mode -> First live charge | ~60% | Prebuilt UI (Stripe Checkout, Payment Links) |
| First live charge -> $1K volume | ~60% | Docs for subscription/invoice; first-run experience emails |
| $1K volume -> Product expansion | ~35% | In-Dashboard upsell prompts; Stripe billing add-on nudge |

**Funnel intervention targets:**
- Improving test-charge -> live-mode activation by 5 pp would add an estimated 8-12% incremental activated accounts annually.
- Streamlining KYB (identity verification at live mode activation) is the single highest-leverage drop-off point - every added step here costs ~4% conversion. Stripe's investment in machine-assisted business verification (using public registry data to pre-fill fields) directly attacks this.

### Edge case 1: Developer activates in test mode but cannot complete live KYB

**Scenario:** A developer in a country where Stripe has restrictive KYB requirements (e.g., Pakistan, Nigeria) creates an account, completes a full integration in test mode, but cannot activate live mode because their country or business type is unsupported.

**Impact:** Complete funnel abandonment after significant developer investment. Causes visible frustration on developer forums and Hacker News - reputational damage beyond the single lost account.

**Stripe's current mitigation:** Stripe Atlas (incorporation service) lets international founders create a US or Irish entity to access Stripe. This is a strategic product - it converts KYB blockers into a revenue opportunity (Atlas fee) and keeps the developer in the ecosystem.

**Residual risk:** Atlas adds friction and cost for developers who just want to test a side project. Some will defect to Paddle (Merchant of Record model that absorbs regulatory complexity) or Lemon Squeezy. Stripe should add a clearer "not available in your country" message early in the flow rather than letting developers invest 10 hours before hitting the wall.

### Edge case 2: High-volume merchant migrates from another processor mid-operation

**Scenario:** A $50M/year merchant migrating from Adyen or Braintree. They have 200,000 stored card tokens on the old processor, active subscriptions, and cannot reset customer payment methods without causing mass disruption.

**Impact:** Token migration is the single biggest technical blocker for enterprise switching. Each card network has its own token migration protocol. If Stripe cannot import tokens from the old processor, the merchant must ask all customers to re-enter their card details - estimated 20-40% customer loss during migration.

**Stripe's current mitigation:** Stripe supports PAN data migration and token migration directly with Visa, Mastercard, and Amex. Their migration team provides a dedicated migration guide and import endpoint. However, the process requires coordination between the old processor (who must agree to export tokens), the card networks, and Stripe - which can take 60-90 days.

**Residual risk:** The token migration timeline is a competitive disadvantage vs. keeping the existing processor. Stripe should productise this further - a self-serve migration progress tracker and SLA commitment would meaningfully reduce enterprise deal friction.

---

## 4) Core loops

### Loop A - Integrate -> Go live -> Scale (developer adoption loop)

```
Developer discovers Stripe (docs, word-of-mouth, Hacker News) ->
creates account; gets test API keys in 30 seconds ->
copies code from docs; first test charge succeeds ->
activates live mode; first real charge processed ->
startup grows; payment volume scales on same integration
```

**Reinforcement:** The test-mode environment is a fully functional replica of production. A developer can simulate card declines, 3DS challenges, webhook events, and dispute flows without touching real money. Reducing time-to-first-working-integration is the core PLG metric. Every friction point in this loop (confusing docs, unclear error messages, broken webhook delivery) kills conversion.

**Aha moment:** The first successful test charge. Observable in the Dashboard, confirmed by the API response `"status": "succeeded"`. Stripe's entire top-of-funnel (documentation quality, copy-paste examples, pre-built UI components) is designed to make this moment happen in under an hour.

**Loop metric:** Time-to-first-live-charge (median; target: <48 hours from account creation for developer-led signups). Activation rate: percentage of accounts that process at least one live charge within 30 days.

**Edge case A1 - Webhook delivery failure during critical order:** A merchant's webhook endpoint goes down during a high-traffic event (Black Friday). Stripe retries webhooks with exponential back-off over 72 hours, but if the merchant's order fulfillment system depends on `payment_intent.succeeded` to provision access, orders pile up with payments captured but no fulfillment triggered.

*Correct behaviour:* Merchants must implement a reconciliation job that polls the Stripe API for recent `succeeded` PaymentIntents and cross-checks against fulfilled orders. Stripe's docs recommend this but it is frequently skipped by early-stage teams. Stripe could reduce this risk by surfacing a "missed events recovery" tool in the Dashboard.

**Edge case A2 - 3DS authentication failure on legitimate European transaction:** PSD2 Strong Customer Authentication (SCA) requires two-factor authentication for most European card payments. A legitimate customer fails the bank's 3DS challenge (wrong OTP, bank app not installed) and the payment fails even though their card has funds.

*Impact:* False-negative conversion loss. Estimated 8-12% of European checkout attempts fail at the 3DS step even for legitimate customers (Stripe-published data range). Stripe's Adaptive Acceptance and Radar-powered 3DS exemption logic (for low-risk transactions) mitigates this by requesting SCA exemptions where allowed, reducing unnecessary 3DS challenges.

---

### Loop B - Subscribe -> Invoice -> Retain (billing loop)

```
Merchant enables Billing; creates Products and Prices ->
creates Subscription linked to a Customer ->
Stripe auto-generates invoices at billing interval ->
sends hosted invoice page or payment link ->
tracks payment failure; retries via Smart Retries ->
merchant monitors MRR/churn in Billing dashboard
```

**Reinforcement:** Billing handles dunning (retry logic, email sequences for failed payments), proration, upgrades/downgrades, trial periods, and usage-based billing. Every feature a SaaS company would have to build themselves is a Stripe Billing feature. The deeper a company integrates Billing, the more painful it is to migrate - the subscription history, invoice records, and proration logic all live in Stripe's data model.

**Smart Retries:** Stripe's ML model predicts the optimal time to retry a failed payment based on cross-network signals. A merchant using Smart Retries vs. dumb time-based retries recovers an estimated additional 2-8% of failed revenue. This is a concrete, measurable reason not to switch processors.

**Loop metric:** Involuntary churn rate recovered by Smart Retries (target: recovering 5%+ of failed payments vs. a basic retry schedule). Billing attachment rate among Payments-active merchants (target: >40% of SaaS-category merchants activating Billing within 90 days of first Payments integration).

**Edge case B1 - Metered usage billing with retroactive data correction:** A SaaS product bills on API call volume. The merchant's usage-reporting pipeline has a bug that underreports calls for one billing period. When corrected, the merchant wants to issue a supplemental invoice for the delta. Stripe's metered billing model aggregates usage records and closes them at the billing period. Retroactive corrections require manual invoice creation and cannot be cleanly tied back to the subscription's usage aggregate.

*Impact:* Revenue leakage and customer confusion. Merchants at scale build separate reconciliation pipelines outside Stripe to catch these deltas - a sign that Stripe's metered billing primitives need a formal "usage correction" workflow.

**Edge case B2 - Mid-cycle plan upgrade with complex proration for enterprise seat contract:** An enterprise customer upgrades from 50 seats to 200 seats on day 12 of a 30-day billing cycle. Stripe's proration logic generates a credit for unused days on the old plan and a charge for the new plan. If the merchant has negotiated a custom per-seat rate (stored as metadata rather than a Stripe Price), the proration calculation must happen outside Stripe and be manually applied as an invoice item.

*Impact:* Enterprise contracts with negotiated rates frequently exceed Stripe Billing's native pricing model capabilities. This is a structural gap that forces enterprise Stripe merchants to either keep a parallel billing system or run complex custom logic on top of Stripe's objects - increasing integration fragility.

---

### Loop C - Platform -> Connect -> Marketplace (network loop)

```
Platform (marketplace, SaaS, gig economy) integrates Connect ->
onboards sub-merchants (connected accounts) ->
end customers pay through the platform ->
Stripe routes funds, handles splits, manages compliance for each connected account ->
platform grows; more connected accounts -> more Stripe volume
```

**Reinforcement:** Connect is Stripe's highest-ARPU product. The platform pays Stripe's standard processing fees plus an application fee on every transaction. As the platform grows, Stripe's revenue grows proportionally. Platforms with 1,000+ connected accounts are deeply locked in: migrating Connect means re-onboarding every sub-merchant to a new provider.

**Compliance delegation:** KYC (Know Your Customer) and AML (Anti-Money Laundering) obligations for sub-merchants can be delegated to Stripe. This is not a nice-to-have for regulated marketplaces - it is load-bearing compliance infrastructure. A marketplace that has delegated KYC to Stripe would face a multi-year compliance rebuild to switch.

**Loop metric:** Connected accounts onboarded per platform per month (growth rate). Gross Payment Volume (GPV) flowing through Connect as a share of total Stripe GPV. Platform-level churn (extremely low once Connect is embedded - estimated <5% annual).

**Connect account mix (estimated):**
- Express accounts: ~55% of Connect platforms. Stripe controls the onboarding UX via hosted flow. Reduces platform support burden. Stripe Dashboard is available to connected accounts.
- Custom accounts: ~30% of Connect volume by GPV (skewed toward high-volume, embedded finance platforms). Platform owns all UX; Stripe is invisible. Highest ARPU but also highest platform-side support cost.
- Standard accounts: ~15% of Connect platforms. Simplest for small marketplaces that want basic access to connected accounts without custom onboarding.

**Edge case C1 - Connected account flagged for fraud mid-payout cycle:** A connected account (seller on a marketplace) is flagged by Stripe's risk systems for suspicious transaction patterns after a payout has been initiated but before it settles. Stripe places a hold on the payout, which means a legitimate seller's funds are frozen pending review.

*Impact:* If the hold lasts >72 hours, it damages platform trust - sellers will complain to the marketplace, not to Stripe. The platform owns the seller relationship even though Stripe made the risk decision. This misalignment is a structural tension in the Connect model: Stripe bears the fraud risk but the platform bears the relationship risk.

*Mitigation:* Platforms on Custom Connect can set reserve policies and manual payout schedules to reduce surprise freezes. Express platforms have less control - they inherit Stripe's default risk decisions.

**Edge case C2 - Multi-currency platform with cross-border splits:** A global services marketplace routes payments from US customers to EU-based contractors. The payment is charged in USD; the connected account is in EUR. Stripe handles currency conversion at the account level, but the FX rate at conversion time differs from the rate at the time of the transaction. If the merchant has guaranteed a fixed EUR payout to the contractor, the FX spread creates a gap that the platform must absorb.

*Impact:* Platforms with multi-currency connected accounts must either hedge FX risk themselves (expensive and operationally complex) or accept exposure to FX variance. Stripe does not offer forward FX contracts or guaranteed rates - this is a gap that some enterprise platforms solve by holding funds in multi-currency Stripe balance accounts and timing conversions.

---

### Loop D - Transaction -> Radar -> Block / Allow (fraud loop)

```
Payment attempt arrives ->
Stripe Radar runs ML model on 1,000+ signals (velocity, device, IP, BIN, cross-network patterns) ->
scores the transaction ->
applies merchant's custom Radar rules ->
blocks, allows, or routes to 3DS challenge ->
merchant reviews disputes; feedback refines model
```

**Reinforcement:** Radar improves with every merchant on the network. A fraud pattern seen on one Stripe merchant is detected on all others within hours. No single merchant's fraud system can replicate this because they can only train on their own transaction history. The network effect in fraud data is Stripe's most defensible moat - more valuable than the payment API itself.

**Rule language:** Merchants write IF-THEN rules in Radar's domain-specific language (e.g., `::risk_score:: > 75 AND :country: != "US"` -> block). This is a low-code interface on top of Stripe's ML scores. It gives sophisticated risk teams precise control without requiring ML expertise.

**Loop metric:** Dispute rate (target: <0.1% of charge volume; above 1% triggers Visa/Mastercard monitoring programs). Radar block rate vs. manual review rate vs. allowed-and-disputed rate. False positive rate (legitimate transactions blocked) is the key quality metric - blocking too aggressively destroys conversion.

**Authorisation rate deep-dive:**

Authorisation rate (the percentage of payment attempts that the issuing bank approves) is the most important operational metric for high-volume merchants. Stripe's network-level optimisation delivers 2-4% higher authorisation rates vs. a naive single-path integration. Mechanisms:

| Mechanism | How it works | Estimated lift |
|---|---|---|
| Smart retries (failed payments) | ML-timed retry when issuing bank is likely to approve | +2-4% recovered revenue on failed payments |
| Network tokens | Stripe uses Visa/Mastercard network tokens instead of raw PANs; tokens have higher bank trust scores | +0.5-1.5% auth rate on stored credentials |
| 3DS exemptions | Stripe requests TRA (Transaction Risk Analysis) exemptions for low-risk EU transactions to skip 3DS friction | +5-8% checkout conversion for exempted EU transactions |
| Smart routing | Routes transactions through the optimal acquirer path based on card BIN and historical approval rates | +0.5-2% auth rate for high-volume merchants |
| Decline code intelligence | Uses cross-network data to distinguish "try again" declines from "do not retry" declines; prevents wasted retries | Reduces false-negative retry costs |

**Edge case D1 - Card testing attack at new merchant activation:** A new Stripe merchant (just activated live mode) is targeted by a card testing attack within hours. Attackers use their API to run hundreds of small-amount charges with stolen card numbers to identify valid cards before using them elsewhere. Stripe Radar catches most card testing patterns via velocity signals, but new merchants have no transaction history, making ML scoring less accurate.

*Impact:* The merchant faces elevated dispute rates and potential Visa monitoring program enrollment (>1% dispute rate) before they've processed meaningful legitimate volume. Early-stage dispute rates can permanently damage the merchant's processing standing.

*Mitigation:* Stripe's Radar free tier catches obvious card testing patterns. Radar for Fraud Teams ($0.02/screened transaction) adds custom velocity rules. Stripe should auto-enroll new merchants in aggressive card testing defaults for the first 30 days with a clear UI explaining the protection.

**Edge case D2 - Friendly fraud on digital goods (subscription merchant):** A subscriber pays for a monthly SaaS subscription, uses the product for 25 days, then files a chargeback claiming "unauthorised transaction." The merchant has clear evidence (login history, feature usage logs, IP confirmation) but must submit this as Radar dispute evidence in a constrained 7-day window.

*Impact:* Digital goods have no physical delivery proof, so chargeback win rates are lower - estimated 40-50% for digital SaaS vs. 70-80% for physical goods with tracking. Stripe's dispute evidence submission tool supports file uploads, customer communication logs, and usage metadata, but the burden of proof remains with the merchant. Stripe should provide a structured "subscription dispute defense package" that auto-compiles login events, usage metrics, and email confirmation history.

---

## 5) Business model

### Revenue model

| Revenue stream | Mechanism | Estimated contribution |
|---|---|---|
| Payments processing | 2.9% + $0.30 per successful card charge (US standard); lower for enterprise; higher for international | ~70% of revenue |
| Billing (subscriptions) | 0.5% of recurring billing volume (on top of processing fees) | ~10% |
| Connect | Application fee charged by platform; Stripe takes standard processing fee from each connected account transaction | ~10% |
| Radar (fraud) | Free tier included; Radar for Fraud Teams at $0.02/screened transaction | ~3% |
| Issuing | Interchange revenue on issued cards; per-card and per-transaction fees | ~3% |
| Other (Atlas, Identity, Tax, Terminal, Capital, Sigma) | Flat fees and per-use pricing | ~4% |

*All figures are estimates based on public pricing pages, analyst reports, and industry benchmarks. Stripe does not report segment revenue publicly.*

### Stripe Capital - the zero-CAC loan product

Stripe Capital offers merchant cash advances (MCAs) to Stripe merchants based on their transaction history. This is structurally one of Stripe's most interesting products:

- **Zero incremental CAC:** The merchant is already in Stripe's system. Stripe knows their revenue, seasonality, churn rate, and dispute history better than any bank.
- **Repayment model:** A fixed percentage of daily Stripe revenue is withheld until the advance + fee is repaid. No fixed payment schedule - repayment flexes with the merchant's cash flow.
- **No credit check model:** Stripe's underwriting relies entirely on transaction data, not credit bureau scores. This unlocks funding for early-stage startups that would be denied by traditional lenders.
- **Retention mechanism:** A merchant with an outstanding Stripe Capital advance cannot switch processors without losing access to their repayment flexibility. This is a durable, low-cost lock-in with positive merchant sentiment (they got capital).
- **Revenue quality:** MCA fees (estimated 10-35% total cost of capital, annualised) are high-margin with predictable default rates given Stripe's underwriting data advantage.

**Estimated contribution:** Stripe Capital is likely in the $500M-$1B revenue range based on public signals about merchant advance volume. It is materially underappreciated as a moat in most Stripe analyses.

### Unit economics levers

- **Authorisation rate:** Every 1% improvement in payment authorisation rate is directly recoverable revenue for the merchant and stickier Stripe revenue. Stripe invests heavily in network optimisation (smart routing, retry logic, bank relationship optimisation) to improve authorisation rates.
- **Product attachment:** A merchant using Payments + Billing + Radar generates 3-5x more gross margin per payment than a Payments-only merchant. Every additional product attached increases switching cost and revenue per merchant.
- **Volume tiers:** Stripe's standard pricing has no volume discount at the SMB level. Enterprise pricing (custom contracts) offers interchange-plus or negotiated flat rates. The pricing cliff between standard and enterprise is a deliberate mechanism - once a merchant hits high volume, Stripe's enterprise team engages to prevent defection to Adyen or direct acquiring.

---

## 6) Competitive landscape

| Competitor | Core strength | Specific win condition | Stripe's counter | Stripe's gap |
|---|---|---|---|---|
| Adyen | Direct acquiring; lower effective rates at high volume; enterprise sales motion | Merchants >$500M/year get 30-50 bps lower effective rate via interchange-plus; Adyen's unified POS+online is stronger for global retail | PLG developer experience; faster integration; broader product suite for digital-native businesses | Enterprise pricing competitiveness at high volume; POS hardware ecosystem |
| Braintree (PayPal) | PayPal buyer network (~400M accounts); PayPal button trust | Merchants with large PayPal-wallet customer bases see 10-20% higher checkout conversion with PayPal button | Stripe supports PayPal as a payment method; neutralises the network advantage for most merchants | PayPal's BNPL (Pay Later) product has strong SMB retail traction; Stripe's BNPL offering is partner-dependent |
| Square | In-person POS hardware; small business retail; integrated Square ecosystem | Brick-and-mortar merchants need card readers, receipt printers, and retail workflows - Square's hardware + software bundle wins on simplicity | Stripe Terminal (card readers) is growing; API-first integrators building POS on Stripe have more customisation | Square has 10+ years of hardware distribution, retail partnerships, and integrated payroll/inventory - not replicable quickly |
| Razorpay / PayU (India) | UPI support; local regulatory relationships; local payment method depth | Indian merchants need UPI (>70% of digital payments in India), IMPS, and NetBanking natively - Stripe's India product still lags on UPI flow conversion | Stripe launched in India with UPI support; growing but not dominant | Local processors have faster reconciliation for UPI disputes and deeper bank relationships for settlement timing |
| Paddle / Lemon Squeezy | Merchant of Record model; absorbs all tax, VAT, and compliance liability | SaaS companies selling globally who don't want to manage VAT in 50+ countries - Paddle handles it; Stripe Tax is an add-on that still leaves merchant as MOR | Stripe Tax (automated tax calculation and filing) is a direct competitive response | MOR model is fundamentally different - Paddle assumes all regulatory liability; Stripe Tax automates but does not absorb liability |

### Specific win/loss scenarios with metrics

**Stripe vs. Adyen at $200M merchant volume:**
- Stripe effective rate: ~2.2% blended (enterprise custom rate)
- Adyen effective rate: ~1.8% blended (interchange-plus with direct acquiring)
- Delta: ~40 bps = ~$800K annual savings for a $200M merchant
- Why merchants stay with Stripe: API quality, Billing/Radar/Connect integration depth, faster feature velocity, and the cost of migration (6-12 months of engineering time estimated at $500K-$2M for large platforms)

**Stripe vs. Paddle for a SaaS company selling globally:**
- Stripe: 2.9% + $0.30 per transaction + Stripe Tax at $0.50/transaction (for automatic tax filing) = ~3.4-3.6% effective rate; merchant retains MOR liability
- Paddle: Flat commission (estimated 5-7% depending on tier); Paddle absorbs all VAT/GST liability globally
- Why merchants choose Paddle: No finance team resource needed for international VAT compliance; single vendor for billing, tax, and chargeback management
- Why merchants stay with Stripe: Lower effective rate at volume; more control over checkout UX; Paddle's 5-7% commission exceeds Stripe's total cost at $1M+ ARR

---

## 7) AI and LLM billing - Stripe's emerging moat

Stripe is already the dominant payment processor for AI companies. OpenAI, Anthropic, Mistral, Perplexity, and most AI-native startups process payments through Stripe. This has created an emerging product opportunity:

### Usage-based billing for token-based AI products

AI products have a novel billing model: users purchase "credits" or pay per token consumed. This maps awkwardly onto Stripe's native Billing primitives:

| Billing model | Stripe mapping | Limitation |
|---|---|---|
| Credit pack purchase (user buys 100K tokens upfront) | One-time PaymentIntent + custom credit ledger in merchant's DB | Stripe has no native credit balance object; merchant must build ledger |
| Metered API usage (post-pay per token consumed) | `usage_records` on a metered Price, or new `Meter` object | Works for monthly billing; poor UX for real-time balance display |
| Hybrid (subscription base + overage) | Subscription + metered add-on Price | Supported natively; proration on overages can be confusing |
| Real-time credit deduction (pay-as-you-go, sub-second) | `Meter` object with high-throughput event ingestion + periodic invoice | The 2024 Billing Meters GA addresses this; still requires merchant-side balance ledger for real-time display |

**Stripe's response:** The `Meter` object (GA 2024) handles high-throughput usage event ingestion with idempotency. Combined with customer credit balance and automatic top-up (threshold-triggered PaymentIntent), this is now a near-complete AI billing primitive stack. The remaining gap is a native real-time balance API that merchants can call per-inference to check and deduct credits without building a parallel ledger service.

**Strategic opportunity:** A first-party "AI billing" product with native credit ledger, real-time balance API, automatic top-up, and inference-level event schema would be a significant moat deepener. No major competitor has built this natively. Stripe's position as the incumbent processor for most of the AI industry makes this a natural extension - the switching cost to migrate billing for an AI product is extremely high once the credit ledger is embedded.

---

## 8) Regulatory moat assessment

Stripe holds money transmission licenses (MTLs) in 46+ US states, is licensed as a Payment Institution (PI) under the EU's PSD2, and holds equivalent licenses in the UK (FCA), Canada, Australia, Singapore, Japan, and others.

**Arguments for durability:**
- MTL acquisition takes 12-24 months per jurisdiction and $500K-$2M in legal, compliance, and capital requirements. A new entrant replicating Stripe's US coverage needs 4-6 years minimum.
- The compliance infrastructure (AML monitoring, SAR filing, KYC processes) requires sustained investment and experienced compliance teams - not just the license itself.
- Stripe's banking partnerships (operating accounts at Wells Fargo, Citibank, HSBC, etc.) and card network memberships (direct Visa/Mastercard principal membership) are separately difficult to replicate.

**Arguments against durability:**
- Open banking (PSD2 in Europe, CDR in Australia, open banking in UK) reduces the regulatory moat by enabling new payment rails that bypass card networks and their licensing requirements entirely.
- Embedded finance infrastructure providers (Synctera, Unit, Column) provide banking-as-a-service infrastructure that allows fintech startups to obtain banking capabilities via partnership rather than direct licensing. This accelerates new entrant time-to-market.
- The regulatory moat is strongest in the US (fragmented state-by-state licensing) and weakest in markets with a single national regulator (EU, UK) where licensing is faster for well-capitalised entrants.

**Net assessment:** Regulatory licensing is a meaningful but not decisive moat. The primary moat remains the transaction data network (fraud intelligence) and the switching cost embedded in multi-product integrations. Regulatory complexity adds 2-4 years to a new entrant's timeline but does not prevent well-capitalised competitors (Adyen, Block) from operating at scale.

---

## 9) Strategic vectors 2025-2027 (resolved from v2 open questions)

### 9.1 Stablecoin and crypto payment rails - Bridge acquisition

**The move:** In October 2024 Stripe acquired Bridge for $1.1B - the largest acquisition in Stripe's history. Bridge is a stablecoin orchestration platform that lets companies move money globally via USDC and USDT on Ethereum, Solana, Polygon, and other rails.

**Why this matters for the product:**
- Stripe had exited crypto in 2018 citing volatility and regulatory uncertainty. The 2024 re-entry is not a hedge - it is a structural bet that stablecoins are becoming settlement infrastructure.
- Cross-border merchant payouts via stablecoin rails have meaningfully lower latency (minutes vs. 2-5 days for SWIFT wires) and lower FX overhead. This directly attacks the Connect multi-currency payout gap identified in edge case C2.
- Bridge's orchestration layer means Stripe can offer stablecoin payouts without forcing merchants to manage wallets, gas fees, or on-chain complexity. The abstraction is the same as Stripe's card infrastructure - complex rails, simple API.

**Chargeback model for on-chain payments:** This was the unresolved v2 question. On-chain transactions are irreversible by design. Stripe's answer is to not use on-chain transactions for end-consumer purchases where chargebacks are expected - stablecoin rails are positioned for B2B payouts, marketplace disbursements, and cross-border contractor payments where both parties are known and chargeback risk is low. For consumer payments, Stripe still routes through card rails where chargeback rules apply.

**Competitive implication:** No other major payment processor has a native stablecoin orchestration layer at Stripe's scale. Adyen has no crypto strategy. PayPal has PYUSD but no merchant payout orchestration. This is a 3-5 year window where Stripe can establish stablecoin payout infrastructure as the default for high-growth platforms before the market commoditises.

**Risk:** Regulatory treatment of stablecoins varies by jurisdiction. The EU's MiCA regulation (fully effective 2024) requires stablecoin issuers to be licensed; orchestrators like Bridge/Stripe face compliance obligations that add operational complexity. The US regulatory framework for stablecoins remains unsettled as of mid-2025.

### 9.2 Stripe Sigma - data product or retention feature?

**What Sigma is:** Sigma is a SQL-based analytics environment that lets merchants query their Stripe data (charges, customers, subscriptions, disputes, payouts) directly using SQL in a managed environment. The schema is Stripe's API object model; queries run against a daily snapshot of the merchant's Stripe data.

**Revenue vs. retention question:**

| Lens | Evidence | Verdict |
|---|---|---|
| Standalone revenue | Sigma is a paid add-on (~$10-250/month depending on volume tier). Uptake is likely <5% of total merchant base given pricing and SQL requirement. | Modest direct revenue |
| Retention mechanism | Merchants who build financial reporting on Sigma create a dependency on Stripe's data layer - they can't easily migrate reporting to a new processor without rebuilding queries. | Strong lock-in for the segment that uses it |
| Data product seed | Sigma's schema is Stripe's authoritative data model. It positions Stripe to offer higher-level analytics products (automated revenue recognition, cohort analysis, LTV models) on top of the SQL layer. | High strategic optionality |

**PM assessment:** Sigma is primarily a retention and expansion feature dressed as a standalone product. The addressable market for "SQL analytics on your payment data" is relatively small (finance/data-savvy teams). The real value is that it creates a data dependency and demonstrates that Stripe's data layer is programmable - which opens the path to AI-assisted revenue intelligence products that would have a much larger TAM.

**What Stripe should build on top of Sigma:** Automated revenue recognition (ASC 606 / IFRS 15 compliant reporting), AI-generated cohort analysis ("your highest-LTV customer cohort has these three characteristics"), and churn prediction signals. These would be meaningful standalone revenue products with CFO-level buyers - different from Stripe's traditional developer buyer.

### 9.3 Terminal and unified commerce

**Current state:** Stripe Terminal is an API-first card reader system. Merchants build their own POS software on top of Stripe's Terminal SDK; the hardware (BBPOS WisePOS E, Stripe Reader M2, Stripe Reader S700) connects via Bluetooth or USB. The positioning is: if you want to build a custom POS app, Terminal gives you the payment layer.

**Why it's strategically important:** The $200B+ brick-and-mortar retail market is the one large payment segment where Stripe is not the default. Adyen's unified commerce platform (same processing rails for online and in-person, unified reporting, card tokenisation across channels) is its strongest enterprise win in retail. If Stripe cannot compete on unified commerce, it cedes the physical retail expansion to Adyen permanently.

**Stripe's unified commerce path:**
- Terminal's API-first model is a competitive advantage for custom POS builders (restaurants, retail tech companies building their own checkout). This is a different segment from large retailers who want a turnkey POS system.
- The gap is offline resilience and hardware distribution. Terminal requires a working internet connection for payment processing (no store-and-forward for offline payments). Square's offline mode (stores transactions locally, processes when connectivity returns) is a material advantage for markets with unreliable connectivity.
- Stripe does not have a retail-ready POS software layer - it provides the payment API, not the inventory management, loyalty, or receipt-printing workflows. This is intentional (API-first principle) but limits penetration into small brick-and-mortar retail where Square bundles everything.

**Realistic 3-year outcome:** Stripe Terminal will continue to grow in the "embedded POS" segment (vertical SaaS companies building payment-enabled apps for restaurants, salons, clinics). It will not displace Square for small retail or Adyen for enterprise unified commerce without a significant software investment or acquisition.

### 9.4 LLM-native API design

**The opportunity:** LLM agents executing financial tasks (book a trip, pay a contractor, manage a subscription) need to call Stripe APIs. Today, agents call Stripe via code generated from the existing API documentation. This works but is fragile - the agent must generate syntactically correct API payloads, handle error codes, and manage multi-step payment flows.

**What Stripe has done:**
- Stripe published official MCP (Model Context Protocol) tools in early 2025, allowing LLM agents to interact with the Stripe API via structured tool calls rather than raw HTTP. This is the most important developer experience move Stripe has made since Stripe.js.
- The MCP integration exposes key Stripe operations (create payment intent, create customer, list invoices, handle disputes) as typed tool schemas that LLMs can call reliably.

**Strategic implication:** The LLM-native API question from v2 is now largely resolved. Stripe is betting that agentic commerce (AI agents executing purchases, managing subscriptions, and handling financial operations on behalf of users) will be a significant payment volume driver within 5 years. By shipping MCP tools first, Stripe establishes the default integration pattern before competitors build their own.

**Remaining gap:** Stripe's MCP tools cover the developer-facing API well. The gap is in consumer-facing agentic flows - an AI agent authorised by a consumer to manage their subscriptions, negotiate prices, or execute purchases on their behalf. The authentication and consent model for "agent acts on behalf of user" payments is not yet a Stripe product. This is the next frontier: OAuth-style delegation for agentic payments where the AI agent has scoped, revocable permission to initiate specific payment types up to a defined limit.

---

## 10) Trade-offs

### Trade-off 1: Simplicity vs. control

Stripe optimises for the median developer use case, which means some advanced configurations require workarounds. A merchant who needs highly custom checkout flows, unusual fund routing, or complex multi-party settlements will hit Stripe's guardrails before they would with a custom-built processor integration.

**Who feels this:** High-volume enterprises with bespoke financial workflows. The trade-off is explicit: Stripe offers speed and leverage at the cost of some configurability. The escape valve is Stripe's Custom Connect and custom Radar rules, but these have limits.

### Trade-off 2: Fee transparency vs. simplicity

Stripe's 2.9% + $0.30 is easy to understand. Adyen's interchange-plus pricing is more transparent at high volume - you pay the actual card network fee plus a small Stripe margin. But interchange-plus is opaque to most developers and requires understanding card network economics. Stripe's flat rate is a subsidy for low-volume merchants and a premium for high-volume ones.

**Who feels this:** Merchants at >$10M annual volume who discover that Adyen's effective rate is 30-50 basis points lower. This is Stripe's most common enterprise churn driver.

### Trade-off 3: PLG DNA vs. enterprise requirements

Stripe was built for self-serve developer adoption. Enterprise procurement requires PO-based contracts, security reviews, SLA guarantees, custom DPA (Data Processing Agreements), and dedicated support. Stripe has built enterprise capabilities (Stripe Enterprise, custom contracts, dedicated TAMs), but its DNA is still PLG - the enterprise motion is a layer on top, not native. Competitors like Adyen have decades of enterprise sales experience.

**Who feels this:** Fortune 500 procurement teams who need 99.99% SLA commitments, detailed security audits, and on-site relationship management. Stripe can provide these but it requires escalation out of self-serve, which adds friction.

### Trade-off 4: Global reach vs. local depth

Stripe supports 50+ countries and 135+ currencies, but its depth in each market varies significantly. In the US, UK, and Western Europe, Stripe has deep local payment method support, direct acquiring relationships, and strong regulatory standing. In India, Southeast Asia, and Latin America, Stripe is present but lacks the local bank relationships, UPI depth, and regulatory history of local incumbents.

**Who feels this:** Companies building for emerging markets find that Stripe's India or Brazil product is technically functional but operationally slower (settlement timing, dispute resolution) and narrower (local payment method coverage) than Razorpay or Mercado Pago respectively.

### Trade-off 5: API breadth vs. coherent product surface

Every new Stripe product (Issuing, Treasury, Climate, Financial Connections, Identity, Sigma, Radar, Terminal, Connect) adds objects, events, and API endpoints. The API surface is now extremely wide. For developers integrating a subset of products, the cognitive overhead of understanding which objects interact is high. Stripe's docs are excellent but cannot fully offset the complexity of a product that has grown from one API to a financial platform with 50+ products.

**Who feels this:** New developers integrating more than two Stripe products simultaneously. The discoverability problem - "which Stripe product do I need for this use case?" - is becoming a real growth bottleneck as the product surface expands.

---

## 11) Metrics framework

### North Star
**Monthly Gross Payment Volume (GPV) per active merchant, segmented by product attachment tier**

Rationale: GPV per merchant captures both volume growth and expansion revenue (more products = higher GPV per merchant from higher retention and lower price sensitivity). A Payments-only merchant growing GPV is a healthy signal; a merchant growing GPV with Billing + Radar attached is a fundamentally better business outcome.

### Input metrics (leading indicators)

| Metric | Target | Why it matters |
|---|---|---|
| Time-to-first-live-charge (median, developer signups) | <48 hours | Primary PLG activation metric; predicts 90-day retention |
| Live-mode activation rate (30-day) | >50% of accounts that complete test charge | Core funnel conversion; identifies KYB friction |
| Billing attachment rate (SaaS accounts, 90-day) | >40% | Highest switching-cost product after Connect |
| Connect account onboarding completion rate | >70% | Platform growth proxy; locked-in revenue driver |
| Smart Retries revenue recovery rate | >5% of failed payment volume | Measures billing retention value vs. competitor |
| AI billing (Meter) activation rate (AI-category accounts) | >60% within 60 days of first charge | Emerging segment capture |

### Guardrail metrics

| Metric | Threshold | Risk if breached |
|---|---|---|
| Dispute rate (network average across all merchants) | <0.1% | Above 1% triggers Visa/Mastercard monitoring for individual merchants; systemic increase signals fraud model degradation |
| Webhook delivery success rate (within 24h) | >99.5% | Failed delivery causes merchant fulfillment failures; damages trust |
| API P99 latency (PaymentIntent create) | <500ms | Developer experience; affects checkout conversion at high-volume merchants |
| New merchant card testing dispute rate (first 90 days) | <0.5% | Early fraud exposure damages new merchant standing |
| Enterprise churn rate | <10% annually | Enterprise accounts have 10-50x the ARPU of SMB; any increase requires investigation |

---

## 12) Experiment backlog

| Experiment | Hypothesis | Primary metric | Acceptance criteria | Rollout risk | Phase |
|---|---|---|---|---|---|
| Guided live-mode activation wizard | Step-by-step KYB flow with progress indicator and auto-fill from business registry data reduces test-to-live drop-off | Live mode activation rate | +5 pp lift at 95% significance; no increase in KYB false-rejection rate | Medium - KYB is compliance-sensitive | Phase 1: 10% rollout to new signups in US/UK |
| "First $1,000 in live payments" onboarding track | Personalised email + in-Dashboard checklist for new merchants targeting their first $1K reduces early abandonment | 30-day live merchant retention | +10 pp retention lift; email unsubscribe rate <5% | Low - email/dashboard UI only | Phase 1: A/B test on all new accounts |
| Radar auto-enroll for card testing protection | New merchants auto-enrolled in aggressive velocity rules for first 30 days; shown as "new merchant protection" opt-out | Dispute rate in first 90 days of account life | Dispute rate <0.1%; false block rate <0.5% on legitimate transactions | Low - framed as opt-out with clear UI | Phase 1: All new accounts globally |
| AI billing templates (credit ledger + top-up) | Pre-built credit ledger template with automatic top-up flow reduces AI startup integration time from 2 weeks to 2 days | AI startup Billing activation rate; time-to-first-meter-event | >60% of AI-category accounts activate Billing Meters within 60 days; integration support ticket volume -20% | Medium - new product surface | Phase 1: Private beta with 50 AI company design partners |
| Connect Express onboarding v2 (live camera ID verification) | Replacing manual ID photo upload with live camera + ML verification reduces connected account onboarding completion time by 40% | Connected account onboarding completion rate | +10 pp completion lift; false rejection rate stays <2%; time-to-verified <5 min median | High - identity verification; must monitor false rejection closely | Phase 1: 5% rollout in UK/AU; expand after 30-day false-rejection audit |
| Dispute defense assistant | AI-assisted dispute evidence compilation (auto-pulls login events, usage history, delivery confirmation) reduces merchant effort for first chargeback | Dispute win rate; merchant CSAT on dispute flow | Dispute win rate +5 pp; time-to-evidence-submission -50%; CSAT >4.0/5.0 | Medium - requires API integration with merchant event logs | Phase 1: Opt-in beta for Billing merchants with >10 disputes/month |
| Agentive payment delegation (scoped OAuth for AI agents) | Providing a scoped permission model for AI agents to initiate payments on behalf of users unlocks a new platform segment | AI agent platform signups; delegated payment volume | 100+ platform signups in first 60 days; $10M+ delegated GPV within 6 months | High - novel auth model; security review required | Phase 1: Closed beta with 10 agentic AI platforms |

---

## 13) 30-60-90 day PM plan

### 30 days - Diagnose

- Instrument the activation funnel at the KYB step: what specific fields cause the highest abandonment? Which business types and countries have the lowest live-mode activation rates?
- Pull cohort data on product attachment: what is the median time from first live charge to first Billing subscription created? What event or email sequence best predicts early Billing activation?
- Review Connect Express onboarding completion by country: which markets have >30% drop-off? Is it identity verification, bank account linking, or document upload?
- Audit Radar new-merchant protection: what % of new merchants experience a card testing attack in first 30 days? What is the average dispute rate at day 30 for attacked vs. non-attacked new merchants?
- Talk to 10 AI company founders who built on Stripe Billing in the past 12 months: what did they have to build themselves that Stripe should have provided natively?

### 60 days - Experiment

- Launch the guided live-mode activation wizard A/B test (10% rollout). Target: measurable KYB drop-off reduction within 30 days of launch.
- Ship the Radar new-merchant auto-enroll (all new accounts). Instrument false-block rate daily; set automatic kill switch at >1% false-block rate.
- Close AI billing template design partner program: 50 companies, structured integration feedback every 2 weeks, roadmap prioritisation based on common integration gaps.
- Prototype dispute defense assistant: integrate with Stripe's own event log (login events, usage records from Billing Meters) to auto-compile evidence package. Validate with 20 merchant disputes before broader release.

### 90 days - Evaluate and scale

- Review live-mode activation wizard lift. If +5 pp or better at 95% significance: plan global rollout over 60 days with localisation for top 10 Stripe markets.
- Evaluate AI billing template activation rate from design partner cohort. If >50% activate Billing Meters within 30 days of template setup (vs. 30-day baseline): prioritise GA for Q3.
- Review Radar auto-enroll dispute rate data. If new-merchant dispute rate in first 90 days dropped below 0.1% with false-block rate below 0.5%: make auto-enroll permanent default and write case study for enterprise prospects.
- Make go/no-go decision on agentive payment delegation based on legal/compliance review outcome. If clear to proceed: announce closed beta at Stripe Sessions and begin developer documentation.

---

## 14) Open questions

All v2 open questions have been resolved in section 9 above. The following are net-new questions for the PRD phase:

1. **Stablecoin payout adoption curve:** What % of Connect platforms will offer stablecoin payouts to connected accounts within 24 months of Bridge integration GA? Which platform categories (gig economy, creator economy, cross-border freelance) are highest-intent?

2. **AI agent authorisation model:** How should Stripe model the consent and revocation flow for AI agents initiating payments? What are the card network rules on agent-initiated transactions vs. cardholder-present vs. stored credential flows?

3. **Sigma -> revenue intelligence product:** Is there a CFO-buyer product waiting to be built on top of Sigma's data layer? What would automated ASC 606 revenue recognition + LTV cohort analysis be worth per seat for a $50M ARR SaaS company?

4. **Terminal offline mode:** What is the engineering and regulatory complexity of shipping store-and-forward offline payments for Terminal? What markets require offline mode as a hard requirement?

5. **Enterprise PLG -> sales handoff trigger:** What is the observable signal (transaction volume, product breadth, dispute complexity) that most accurately predicts when a self-serve merchant is ready for enterprise contract conversion? Is Stripe's current trigger too late (by which point Adyen has already entered the conversation)?

6. **Multi-product bundling:** Would a "Startup Stack" bundle (Payments + Billing + Radar + one Sigma seat at a fixed monthly price) increase product attachment and retention vs. the current a-la-carte model, even if it reduces ARPU in the short term?

---

*All metrics are directional estimates based on public information, Stripe pricing pages, and industry benchmarks - not internal Stripe data.*
