<p align="center">
  <img src="images/zomato-logo.png" alt="Zomato logo" width="200"/>
</p>

# Zomato - Search to Checkout Funnel Teardown

> This teardown is based on publicly observable product behavior and reasonable assumptions.  
> Metrics mentioned are directional, not actual internal data.

**Status:** v1 - Initial analysis

---

## 1. Product Context

Zomato is a hyperlocal food discovery and delivery platform operating a two-sided marketplace between consumers and restaurants.

This teardown focuses on Zomato’s **core revenue-generating flow**:
**Restaurant discovery → menu exploration → checkout → order completion**.

Given the high frequency and high intent nature of food ordering, even small funnel inefficiencies can have an outsized impact on revenue and retention.

---

## 2. Core User Problem

### Primary Persona
**Urban working professional / student**
- Limited time
- Price-aware but convenience-driven
- Orders food multiple times per week

### Problems Before Zomato
- Discovering nearby restaurants reliably
- Comparing menus and prices quickly
- Uncertainty around delivery time and order fulfillment

Zomato’s core job is to **reduce decision time and delivery uncertainty** for a high-intent user.

---

## 3. User Journey (Ordering Funnel)
### Ordering Funnel

1. **App Open**  
   Entry point driven by hunger, habit, or notifications.

2. **Homepage (Discovery)**  
   Personalized restaurant and cuisine recommendations.

3. **Search / Scroll / Filters**  
   Intent refinement based on cuisine, price, distance, or ratings.

4. **Restaurant Page**  
   Trust-building through ratings, ETA, and offers.

5. **Menu Exploration**  
   High cognitive load zone; pricing and choice complexity peak here.

6. **Add to Cart**  
   First strong intent signal.

7. **Cart Review**  
   Price aggregation and offer application.

8. **Address & Payment**  
   Friction zone due to fees, taxes, and payment failures.

9. **Order Placed**  
   Activation moment; shifts focus from conversion to fulfillment.



### Activation Moment
The first successful **order completion**.  
Everything before this is friction; everything after this is retention.

---

## 4. North Star Metric

**Orders per Monthly Active User (Orders / MAU)**

This metric:
- Captures frequency and habit formation
- Directly ties to revenue
- Avoids vanity metrics like installs or sessions

For a transactional product, repeat behavior matters more than raw usage.

---

## 5. Supporting Metrics

### Acquisition
- Homepage → restaurant click-through rate
- Search usage vs scroll discovery

### Activation
- Menu views per session
- % of users adding at least one item to cart

### Conversion
- Cart → checkout conversion rate
- Checkout → successful order rate

### Retention
- Orders per user per week
- 7-day and 30-day reorder rate

### Monetization
- Average order value (AOV)
- Platform + delivery fee contribution
- Zomato Gold adoption

---

## 6. What’s Working Well

### Strong Restaurant Discovery
- Time-of-day personalization
- Cuisine-based clustering
- Visual restaurant cards reduce decision fatigue

### Menu as the Primary Decision Surface
- Clear pricing and item images
- Customizations and add-ons encourage higher AOV

### Trust & Predictability
- Delivery ETA shown early
- Real-time order tracking
- Clear order status updates

These choices optimize for **speed of decision-making**, not just choice abundance.

---

## 7. What’s Broken or Risky

### Menu Overload
Large menus, especially from cloud kitchens, increase:
- Cognitive load
- Time to first cart addition

This likely causes drop-offs before activation, particularly for first-time users.

---

### Late Price Revelation
Delivery fees, platform fees, and taxes often appear **late in the funnel**.

This creates:
- Cart shock
- Checkout abandonment

Earlier price expectation setting could reduce this friction.

---

### Subscription Value Visibility (Gold)
Zomato Gold benefits are not consistently visible at key decision points.

This reduces the perceived value of the subscription as a friction-removal tool.

---

## 8. Key Product Trade-offs

| Trade-off | Current Bias |
|---------|-------------|
| Choice vs Speed | Choice |
| Transparency vs Conversion | Partial transparency |
| Power users vs New users | Power users |

These appear to be **intentional trade-offs**, not accidental design flaws.

---

## 9. What I’d Do as PM (90-Day Plan)

### First 30 Days - Diagnose
- Measure menu scroll depth vs cart addition
- Identify checkout drop-offs by fee exposure
- Segment first-time vs repeat users

### Next 30 Days - Experiment
- Default “Top Picks” section above the fold
- Earlier fee transparency experiments
- Gold benefit nudges on restaurant pages

### Final 30 Days - Scale or Kill
- Roll out winning experiments
- Kill low-impact nudges
- Optimize for **Orders / MAU**, not just AOV

---

## 10. Key Takeaway

Zomato’s ordering funnel is optimized for **repeat, power users**, but leaks value with new and low-frequency users due to late friction revelation and menu complexity.

The biggest opportunity lies not in adding features, but in **reducing cognitive and price friction earlier in the journey**.

