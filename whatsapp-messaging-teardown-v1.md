<p align="center">
  <img src="images/whatsapp.png" alt="WhatsApp" width="120" />
</p>

# WhatsApp — Product Teardown

> Lens: Product Manager • Scope: **publicly observable behavior only** (no internal Meta data claims)

---

## Version history
- **v2 (current):** expanded segments, explicit funnels + activation definitions, loop-specific metric definitions, experiment ideas, and a PM instrumentation/event taxonomy.
- **v1:** baseline framing (core loop, funnel, north star metrics, trade-offs, and initial opportunities).

---

## 0) Product in one sentence
WhatsApp is a **default communication layer**: fast messaging + calls anchored to your phone number, designed to work across network quality, device types, and social contexts.

## 1) Who it serves (observable segments)
Even without internal data, WhatsApp’s UX reveals distinct usage modes:

1. **1:1 coordinators** (family, partners): high frequency, high expectation of delivery/read reliability.
2. **Group coordinators** (schools, neighborhoods, hobby groups): many-to-many, admin tools, noisy threads.
3. **Creators/SMBs** (broadcasting offers/updates; often using WhatsApp Business in parallel): need reach + trust.
4. **Low-bandwidth / low-typing users**: voice notes, lightweight media compression, resilient delivery.
5. **International connectors**: cross-border messaging/calls, link sharing, media.

**PM implication:** you don’t optimize a single funnel; you optimize a set of funnels that share primitives.

## 2) Value props, translated into measurable promises
WhatsApp’s value is unusually measurable because the product promises are operational:

- **Reliability** → message delivery success, call setup success
- **Speed** → send-to-delivered latency
- **Low friction** → time-to-first-message, permission acceptance, contact discovery
- **Safety** → spam/abuse incidence and user control (block/report)

A useful mental model: WhatsApp wins when it becomes the *lowest-effort path* from intent (“tell someone”) to outcome (“they know”).

## 3) The core loops (separating the big loop into smaller loops)
### 3.1 Reply loop (the primary retention engine)
**Trigger → Send → Receive → Reply → Thread continues**

Metrics to define:
- **Conversation starts (CS):** count of distinct chats where a user sends a message after ≥X hours of inactivity.
- **Time-to-first-reply (TTFR):** median + p90 from first send to first reply.
- **Conversation continuation rate (CCR):** % of conversation starts that see a reply within 24 hours.

### 3.2 Group formation loop (switching-cost engine)
**Need coordination → Create/join group → Add members → Repeated updates → More reliance**

Metrics:
- **Group creation rate** (per new active user, and per existing active user)
- **Invite acceptance rate**
- **Group 7-day survival:** % groups with ≥N messages on day 7

### 3.3 Media loop (frequency + expressiveness)
**Share photo/video/voice note → Faster comprehension/emotion → More sharing**

Metrics:
- **% of senders who send media** in week 1
- **Voice note adoption** (esp. in low-typing contexts)

### 3.4 Calls loop (high-stakes communication)
**Chat fails / complexity rises → Call → Stronger relationship → More dependence**

Metrics:
- **Call initiation rate**
- **Call setup success** and **drop rate**

## 4) Funnels (explicit, with “activation” definitions)
### 4.1 New user activation funnel (phone-number anchored)
1. Install
2. Phone number verification (OTP)
3. Contacts permission (optional but accelerates value)
4. Notification permission (drives re-engagement)
5. Contact discovery (see existing users)
6. **First outbound message**
7. **First inbound reply**

**Activation definition (PM-usable):**
- **A1 (shallow):** first outbound message within 24h
- **A2 (meaningful):** first inbound reply within 24h of first outbound message
- **A3 (sticky):** ≥3 distinct active days in first 7 days OR joins/creates ≥1 group in first 7 days

Why multiple definitions: A1 is easy to move; A2/A3 correlate better with retention.

### 4.2 Re-activation funnel (lapsed users)
1. Trigger (notification, deep link, invite)
2. Open app
3. Navigate to chat
4. **Send a message**
5. **Receive a reply**

Metric focus:
- **Open→Send conversion**
- **Send→Reply conversion**

### 4.3 Invite funnel (network growth without a feed)
1. User intent: “I need to message X”
2. X not on WhatsApp (observable via contact list gaps)
3. Invite via link/SMS
4. Install + verify
5. First message to inviter

Metrics:
- Invite send rate
- Invite acceptance/install rate
- Invitee time-to-first-message to inviter

### 4.4 Group scaling funnel (from small groups to communities)
1. Create group
2. Add members
3. Establish norms (admin rules, name/icon)
4. Sustained interaction
5. (Optional) organize groups-of-groups

Metrics:
- Member add completion rate
- Group “noise” indicators: mute rate, leave rate
- Admin action frequency (approvals, removals)

## 5) Entry points (what the UI prioritizes)
WhatsApp’s home is the **recent chats list**, which acts as a “to-do list” for replies.

Primary session starts:
- Notification tap (reply-driven)
- Deep links (invite links, wa.me)
- Share sheet (send this content)
- Search (contact/group)

**PM observation:** by minimizing feed-like surfaces, WhatsApp makes “replying” the default work unit.

## 6) Product surfaces as “jobs-to-be-done”
### Messaging
Job: *“Transmit a thought with minimal effort and high confidence it arrives.”*
- Text, reactions, replies
- Media + documents
- Voice notes (low-bandwidth / low-typing utility)

### Groups / Communities
Job: *“Coordinate many people without losing the thread.”*
- Admin controls, invites, join links
- Organization layer (communities)

### Status
Job: *“Share an update without forcing a conversation.”*
- Ambient sharing
- Reduces DM load while maintaining connection

### Calls
Job: *“Resolve ambiguity quickly.”*
- Escalation path from chat to call

## 7) Constraints and trade-offs
1. **Phone-number identity vs flexibility**
   - Pro: instant identity + easy discovery.
   - Con: SIM swaps, number changes, multi-device complexity.

2. **Encryption posture vs abuse handling** (public perception + UX)
   - Users want privacy *and* protection from scams.
   - UX must provide clear controls (block/report) while keeping false positives low.

3. **Low friction vs consent** (permissions)
   - Contacts/notifications accelerate time-to-value but can feel invasive.
   - PM lever: progressive permissioning tied to user intent (“to find friends”, “to not miss replies”).

4. **Group growth vs group quality**
   - Easier invites increase growth; too much growth increases noise → mutes/leaves.
   - Admin tooling and norms become retention tools, not just safety tools.

5. **Simplicity vs power-user affordances**
   - Simple chat model supports billions; power users want pinning, labeling, workflows.
   - Risk: too many knobs makes the product feel like “work software”.

## 8) What I’d test if I owned activation (concrete, public-UX-consistent)
These are testable without making internal claims; success measured via the activation definitions above.

### Experiment 1: “First reply helper” (reduce TTFR)
- When a user sends a first message to a contact, show lightweight guidance:
  - “Turn on notifications to see replies instantly”
  - “Invite another contact” (if the first contact isn’t responsive)
- Measure: TTFR, A2 uplift, notification opt-in rate.

### Experiment 2: Permission prompts tied to immediate intent
- Ask for contacts permission at the moment the user tries to start a new chat/search contacts.
- Measure: contacts opt-in, time-to-first-message, A2.

### Experiment 3: Group onboarding for coordinators
- After a user participates in ≥2 back-and-forths with ≥2 distinct contacts, suggest: “Create a group for this plan”.
- Measure: group creation rate, group 7-day survival, WAU retention.

## 9) Instrumentation plan (what a PM would insist on)
Event taxonomy (illustrative):
- onboarding_*: otp_sent, otp_verified, permission_contacts_granted, permission_notif_granted
- discovery_*: contacts_found_count, first_chat_opened
- messaging_*: message_sent, message_delivered, message_read, media_sent, voice_note_sent
- conversation_*: conversation_start, reply_received
- group_*: group_created, members_added, invite_link_created, invite_accepted, group_left, group_muted
- calls_*: call_started, call_connected, call_failed, call_ended
- safety_*: blocked_user, reported_user, unknown_message_received

And always track **latency** + **failure reasons** where visible (network/timeout/etc.).
