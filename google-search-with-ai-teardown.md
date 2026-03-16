# Teardown: Google Search — Adding AI Answers Without Breaking the Web

**Product:** Google Search (consumer search + ads + publisher ecosystem)
**Author:** Mayank Malviya
**Date:** 16 Mar 2026
**Status:** v1 — product teardown focused on AI answers / AI-assisted search

> Notes on sources: This teardown is based on product understanding and public industry patterns. Exact ranking/ads policies, model behavior, and UI labels are *representative*, not official.

---

## TL;DR (what matters)
- “AI in Search” is not a feature; it’s a **re-architecture of the SERP contract**: from “links first” to “answers + actions first” while still funding the ecosystem via **ads** and sustaining the **open web**.
- The core product tension is **helpfulness vs. attribution vs. monetization**:
  - AI answers reduce clicks (good for users), but risk **publisher value collapse** (bad for the web) and can reduce **commercial intent capture** (bad for ads) unless redesigned carefully.
- The real moat is distribution + infrastructure: **query volume**, **freshness**, **index quality**, **anti-abuse**, **latency**, and a **feedback loop** that turns user interactions into relevance and trust.
- The hardest PM problem: deciding **when to answer** vs **when to cite + send traffic** vs **when to ask a clarifying question**, with guardrails for safety, policy, and correctness.

---

## 1) What Google Search is (in one line)
A **universal intent router**: it takes a query, infers intent, and returns the best next step (answer, website, product, map, call, reservation), monetized primarily through **ads aligned to intent**.

---

## 2) The user’s job-to-be-done (JTBD)
**Primary JTBD:** “Help me get the right outcome fast, with minimal effort.”

**Intent buckets (practically useful):**
- **Know**: learn/explain/summarize (e.g., “why is my phone hot”).
- **Do**: complete a task (e.g., “renew passport online”).
- **Go**: navigate locally (e.g., “coffee near me”).
- **Buy**: compare/purchase (e.g., “best noise cancelling headphones under 200”).

AI changes the experience most in **Know** and high-consideration **Buy** queries.

---

## 3) The core funnel (macro)
Search is unusual: users don’t “onboard” much. The funnel is per-query.

**Query loop:**
1. **Query issued** (typed/voice/image)
2. **SERP viewed** (answers, ads, modules, links)
3. **Action taken** (click, refine, follow-up question, call, navigate)
4. **Outcome** (task done / purchase / learning)
5. **Satisfaction signals** (short-click/long-click, reformulations, dwell, back-to-SERP)

AI adds a parallel path:
- **AI answer consumed** → follow-up prompts → cited sources → deeper exploration.

---

## 4) What changes when you add AI to Search
### 4.1 The SERP contract shifts
Traditional contract: *“We’ll rank the best documents.”*

AI contract: *“We’ll produce the best **response** (and ideally show the best sources) — and sometimes we won’t even need you to click.”*

That forces new decisions:
- When to show an AI answer vs featured snippet vs “10 blue links”
- How to keep **attribution** credible and visible
- How to preserve **choice** (multiple perspectives) when the AI synthesizes

### 4.2 Retrieval + generation becomes a product primitive
The experience depends on:
- **Retrieval** (what sources are eligible)
- **Grounding** (how the model uses sources)
- **Presentation** (citations, quotes, source diversity)
- **Follow-ups** (conversational refinement without losing intent)

### 4.3 New failure modes
- Confident wrong answers (hallucinations)
- Citation errors (wrong source for the claim)
- Stale info (freshness gaps)
- Over-summarization (nuance loss)
- Publisher harm (traffic cannibalization without value return)

---

## 5) Information architecture (IA): the “AI SERP”
A representative AI-enhanced SERP tends to contain:
- **AI answer block** (summary + steps + structured sections)
- **Citations** (links, sometimes with quoted snippets)
- **Traditional results** (organic links)
- **Vertical modules** (maps, shopping, news, videos)
- **Ads** (text ads, shopping ads, local ads)
- **Follow-up bar / prompts** (ask next question)

The IA challenge is preventing the AI block from becoming a **dead-end**.

---

## 6) Metrics that matter (north star + guardrails)
### North Star (user)
- **Successful sessions per user** (task completed with high satisfaction)

### Supporting metrics
- **Query success rate** (implicit: fewer reformulations, longer dwell on successful outcomes)
- **Time-to-satisfaction** (TTX)
- **Follow-up completion rate** (AI conversational refinement)
- **Citation interaction rate** (healthy clicks to sources when appropriate)

### Ecosystem + business guardrails
- **Publisher traffic health** (especially for informational queries)
- **Commercial intent capture** (ads CTR/conv on eligible queries)
- **Trust & safety incidents** (policy violations, unsafe advice)
- **Answer accuracy / correction rate** (human eval + user feedback)

A mature AI Search setup treats these as a **balanced scorecard**, not a single metric.

---

## 7) Product decisions: when should AI answer?
A good heuristic policy (not official):

**AI should answer when:**
- Intent is informational and can be grounded to multiple sources
- The task is multi-step but generic (e.g., “how to clean a coffee grinder”)
- The query benefits from synthesis (comparison, pros/cons, structured plan)

**AI should not answer (or should heavily constrain) when:**
- High-risk domains (medical, legal, finance) without strong grounding + disclaimers
- Highly time-sensitive breaking news without freshness guarantees
- Queries where the user likely wants a **specific source** (navigational)

**AI should ask a clarifying question when:**
- The query is underspecified and branching (e.g., “best laptop” without budget/OS)

---

## 8) Ads + monetization: integrating intent with AI
Search monetization works because it captures **intent at the moment of need**.

AI can either:
- **Help ads** (better intent understanding, better matching, better landing decisions)
- or **hurt ads** (fewer clicks, fewer result views, less surface area)

A sensible product approach:
- Keep ads clearly labeled and separated
- Use AI to improve the user journey *after* the click (e.g., better pre-qualification)
- Ensure AI doesn’t “answer away” high-commercial queries without offering routes to compare/buy

Key constraint: any AI integration must preserve **user trust** that results are not covertly biased.

---

## 9) Trust, safety, and abuse
AI raises the stakes on:
- **Spam/SEO manipulation** (models may over-trust optimized pages)
- **Content farms** designed to be “LLM-friendly”
- **Prompt injection** via retrieved content

Guardrails (product + system):
- Source reputation and diversity heuristics
- Grounding requirements for factual claims
- Defensive rendering (don’t execute instructions from retrieved pages)
- Prominent “report” and feedback loops

---

## 10) What I’d do as the PM (30–60–90 day plan)
### First 30 days: define the contract
- Define eligibility policy by intent bucket + risk class
- Decide citation UX (how many, where, and what they represent)
- Ship a robust feedback capture: “wrong / outdated / unsafe / missing sources”

### 60 days: improve outcomes, not just answers
- Add clarifying question patterns for underspecified queries
- Add “action pathways” (checklists, comparisons, local next steps)
- Build ecosystem metrics: publisher traffic deltas by query cluster

### 90 days: scale with governance
- Add model versioning by geo/language, kill switches, and audits
- Expand evaluation harness (offline + online) and introduce counterfactual metrics
- Establish policy review for high-risk categories and breaking news behavior

---

## 11) Moats and threats
### Moats
- Index quality + freshness + multilingual coverage
- Anti-abuse + trust signals at scale
- Distribution (default placements) + habit
- Data flywheel (query/session feedback)

### Threats
- Verticalized AI assistants that solve tasks end-to-end
- Publisher backlash and regulatory pressure if traffic collapses
- Trust erosion from a few high-profile incorrect answers

---

## 12) Open questions (worth debating)
- What is the right **economic exchange** with publishers in an AI-first SERP?
- How do we communicate **uncertainty** without confusing users?
- What’s the right UX for “multiple perspectives” when the model synthesizes?
- Should AI answers be cached/served like snippets, or generated per user/context?
