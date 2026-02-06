<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/6/6b/WhatsApp.svg" alt="WhatsApp" width="120" />
</p>

# WhatsApp — Product Teardown (v1)

> Lens: Product Manager • Scope: **publicly observable behavior only** (no internal Meta data claims)

## 1) What problem WhatsApp solves (in one line)
A **fast, reliable, low-friction messaging layer** that works across phones, networks, and geographies—good enough for 1:1 chats and extensible to groups, media, and lightweight coordination.

## 2) The core product loop (what keeps users coming back)
**Trigger → Message → Response → Ongoing thread → More participants / more contexts**

- A conversation is a “container” for recurring utility (plans, work, family, communities).
- Switching costs are social, not technical: your contacts + groups live here.

## 3) North Star + primary success metrics (v1)
### Likely North Star Metric (PM framing)
- **Weekly active communicators (WAC)**: users who send at least one message in a 7‑day window.

### Core activity metrics
- **DAU/WAU/MAU** (especially WAU) and retention cohorts
- **Messages sent per active user** (by 1:1 vs group)
- **Response rate & time-to-first-reply** (conversation health)
- **Threads with continued activity** (e.g., % chats active again within 7 days)

### Reliability metrics (product-critical, user-visible)
- **Delivery success rate** (sent → delivered → read)
- **Median / p95 send-to-deliver latency**
- **Call setup success rate** (voice/video)

### Trust & safety metrics
- **Spam reports per 1k users**
- **Account integrity**: blocks, reports, ban appeals
- **Abuse containment**: forwarded content volume, virality caps effectiveness (user-visible via “Forwarded” labels)

## 4) Core funnel (activation → habit)
WhatsApp’s funnel is unusually **front-loaded**: if you can reach even a few contacts quickly, habit forms.

### Step-by-step funnel
1. **Install & open**
2. **Phone number verification** (OTP)
3. **Permissioning** (contacts, notifications)
4. **Contact discovery** (who’s already on WhatsApp)
5. **First message sent** (activation event)
6. **First reply received** (value confirmation)
7. **Create / join a group** (social lock-in)
8. **Media sharing / voice note** (higher frequency usage)
9. **Calls** (expands “communication surface area”)

### Key drop-off points (v1)
- OTP / SIM issues, device changes, network constraints
- Permission denial (contacts/notifications) → slower time-to-value
- Empty contact graph (few contacts on WhatsApp)
- Fear/uncertainty about privacy, scams, unknown messages

## 5) Entry points (how users start a session)
- Notification tap (reply-driven)
- Open app → recent chats
- Search for a contact/group
- Share sheet from another app (send link/photo/document)
- Deep links (invite links, wa.me)

## 6) Feature set, mapped to user jobs
### Messaging primitives
- Text, emojis/stickers, photos/videos, documents
- Voice notes (high utility in low‑typing or multilingual contexts)
- Reactions, replies, mentions (thread clarity)

### Coordination primitives
- Groups + admin controls
- Broadcast lists (creator/SMB lightweight reach)
- Communities (group-of-groups organization)
- Polls, events (in some markets)

### Calling
- Voice + video calls as “fallback” for complex coordination

### Status
- Low-effort “ambient sharing” without direct message overhead

## 7) What WhatsApp is *not* (clear boundaries)
- Not a full public social feed (Status is intentionally limited)
- Not a complex enterprise collaboration suite (competes on simplicity)
- Not a payments-first superapp globally (feature exists/varies, but core value is messaging)

## 8) Product strategy (v1 hypothesis)
WhatsApp’s strategy is to:
- **Win trust through reliability + simplicity**
- **Expand use cases without breaking the mental model** (“it’s still just messaging”)
- **Increase social switching costs** via groups/communities and cross-context utility

## 9) Key trade-offs (v1)
- **Simplicity vs power**: fewer knobs keeps onboarding easy, but limits advanced workflows.
- **Privacy positioning vs platform safety**: strong encryption posture while still needing anti-spam measures.
- **Cross-platform consistency vs OS-native polish**: consistent UX reduces confusion; native affordances vary.
- **Virality vs misinformation risk**: forwarding constraints and labels reduce spread but can frustrate power users.

## 10) PM “if I owned this” — v1 opportunities (high-level)
These are directionally plausible and testable without internal data:

1. **Faster time-to-first-reply**
   - Improve prompts after first send (“invite 3 people”, “start a group”) and reduce permission anxiety.
2. **Group clarity at scale**
   - Better summarization/organization for noisy groups (without turning it into a feed).
3. **Anti-spam UX that feels respectful**
   - Make unknown-message handling clearer, with less fear and fewer false positives.

## Appendix: v1 assumptions checklist
- No claims about Meta’s internal ranking, monetization performance, or private metrics.
- Conclusions are based on observable UX patterns and common messaging product dynamics.
