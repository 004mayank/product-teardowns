# LinkedIn — Product Teardown (V1)

*(Scope: core LinkedIn experience — identity/profile, network graph, feed, engagement, messaging, jobs discovery. Not deep into ads/learning/courses.)*

## 0) One-liner
LinkedIn is a professional identity + network graph that turns **career intent** (hire, get hired, learn, sell) into a **feed + search** product with high-trust profiles and workflow-driven surfaces (jobs, messaging, company pages).

---

## 1) Who it’s for (personas)

1) **Job seeker**
- wants: discover roles, signal openness, get referrals, apply fast
- pain: low response, spammy roles, noisy feed

2) **Recruiter / hiring manager**
- wants: search/filter talent, outreach at scale, pipeline management
- pain: low reply rates, incomplete profiles, compliance

3) **Knowledge / career builder**
- wants: build reputation, learn, stay updated, grow network
- pain: content fatigue, engagement incentives

4) **Seller / BD (social selling)**
- wants: identify buyers, warm intros, nurture leads via content + DMs
- pain: outreach limits, trust, signal vs noise

---

## 2) Core loops

### 2.1 Identity → discovery → opportunity loop (career loop)
1) User creates/updates **profile** (skills, roles, education)
2) LinkedIn increases distribution in **search + recommendations**
3) Recruiters/users view profile → connection requests / messages
4) Opportunities (interviews, jobs) → user invests more in profile

### 2.2 Network graph loop
1) Import contacts / connect with colleagues
2) Graph grows → better recommendations (people, jobs, content)
3) More utility → more connections

### 2.3 Feed loop
1) Create content (post/comment)
2) Engagement from network increases reach
3) Creator sees value → posts more
4) Audience gets updates/opinions → returns daily

---

## 3) Main surfaces (what the user actually does)

### 3.1 Onboarding
- sign-up → headline, role, company, location
- quick prompts: add education, skills, upload resume (optional)
- suggestions: connect with coworkers/classmates

### 3.2 Profile (the “resume + reputation” page)
- summary/headline, experience, education, skills
- endorsements/recommendations (social proof)
- featured content, activity
- recruiter views: “open to work”, contact info (sometimes gated)

### 3.3 My Network
- connection requests, suggested people, growth prompts
- graph expansion via shared company/school

### 3.4 Feed
- ranked feed (people + company pages)
- post types: text, link, image, video, carousel
- actions: like/react, comment, repost, follow
- notifications drive return visits

### 3.5 Search
- global search entry point
- people, jobs, companies, posts
- filters: location, industry, seniority, etc.

### 3.6 Messaging
- DMs with connection-based affordances
- requests/inbox separation (anti-spam)
- “InMail” (paid/recruiter) as a monetized messaging lane

### 3.7 Jobs
- job recommendations + search
- apply flows (Easy Apply vs external)
- saved jobs, alerts

---

## 4) What LinkedIn is optimizing (North Stars)

- **DAU/WAU retention** (feed + notifications)
- **Profile completeness** (improves match quality)
- **Network density** (connections per active user)
- **Marketplace liquidity**
  - recruiter searches → qualified candidates → reply/apply rates
- **Monetization**
  - Talent solutions (recruiter seats, job postings)
  - Premium subscriptions (InMail, insights)
  - Ads (B2B)

---

## 5) Key product decisions (notable tradeoffs)

1) **Real identity + professional norms**
- higher trust than most social apps, enables hiring workflows
- tradeoff: higher friction onboarding and moderation expectations

2) **Feed engagement vs career utility**
- feed drives daily habit
- tradeoff: content incentives can drift toward “engagement bait”

3) **Messaging as a monetization surface**
- InMail / restricted outreach reduces spam and funds the marketplace
- tradeoff: can feel paywalled and increases cold outreach

4) **Jobs as an intent goldmine**
- job seeking is high-intent; fuels monetization
- tradeoff: requires strong matching + anti-scam controls

---

## 6) Competitive moats

- **Profile graph** (identity + history + endorsements)
- **Network effects** (connections improve relevance)
- **Recruiter adoption** (workflow + tooling)
- **Data advantage** (career trajectories, skills graph)

---

## 7) Open questions / V2 targets

- How is the feed ranking balancing: recency vs relationship vs topic?
- What are the key anti-spam levers in messaging and connection requests?
- How does jobs matching work conceptually (skills, title, seniority, location, company)?
- Which workflows to deepen next: recruiter funnel, creator tools, or job seeker conversion?
