# Figma - Product Teardown

**Version:** v2 - Improved teardown
**Changes from v1:** Added event schema, deepened funnel with specific cohort metrics and upgrade triggers, expanded competitive analysis with specific win/loss reasons, added AI strategy deep-dive, experiment backlog, open questions resolved, strengthened PM thesis.
**Lens:** Product Manager - Scope: publicly observable product behaviour, UX patterns, and inferred business logic

---

## Version history

| Version | Key additions |
|---|---|
| v1 | Core thesis, positioning, product model, loops, funnels, user journeys, competitive analysis, trade-offs, metrics |
| v2 | Event schema, funnel cohort metrics, AI strategy deep-dive, experiment backlog, open questions, deepened competitive analysis, Dev Mode monetisation mechanics |

---

## TL;DR

Figma won the design tool market not by making a better drawing application but by making design a collaboration layer. Every tool before Figma optimised for the designer's workflow; Figma optimised for the moment a designer shares work with an engineer, a PM, or a stakeholder. Its moat is the network effect baked into that model - once a team's components, libraries, and review workflows live in Figma, switching cost compounds fast. The next inflection point is Figma AI: the bet is that Figma becomes the surface where AI generates, and humans refine - not where AI replaces the design surface entirely. The existential risk is that tools like v0 and Cursor route around Figma by going from prompt to production code without a design file ever being created. Figma's answer must be design-system-aware AI generation - not generic wireframe generation - or the AI bet fails for enterprise.

---

## 1) Positioning

**What it is:** Figma is a browser-native collaborative design platform covering UI/UX design, prototyping, design systems, developer handoff, and FigJam-based whiteboarding - all in a single shared URL.

**Core promise:** Design, review, prototype, and hand off in one place - with every stakeholder looking at the same live file, not an attachment.

**Design principle:** The design file is a URL, not a local file. This single architectural decision powers every collaboration, review, and handoff workflow in the product.

**Why it wins:** Design is fundamentally a communication problem. Sketch and Photoshop were built for solo creators; Figma was built for the moment a designer shows their work to someone else. It won because it solved the review and handoff problem first, and built the design tool around that constraint. The browser-native model eliminated "please send me the latest file" as a product problem category.

**PM thesis:** Figma's primary expansion vector is not converting more designers - it is expanding monetisation of the engineers, PMs, and stakeholders who are already in the file for free. Dev Mode (paid seat), Figma AI for non-designers, and FigJam-as-async-meeting-tool are all bets on this axis.

### Target users

| Segment | Primary JTBD | Activation trigger | Estimated share of MAU |
|---|---|---|---|
| Product designer | Design screens, components, and flows for digital products | Creating a frame and using Auto Layout for the first time | ~35% |
| Frontend engineer | Inspect designs, extract CSS/code, understand intent | Opening Dev Mode on a live design file | ~30% |
| Product manager | Review designs, leave contextual comments, contribute to FigJam | Commenting on a frame and receiving a reply | ~15% |
| Design systems team | Build and maintain shared component libraries and tokens | Publishing a shared library to a team or org | ~5% |
| Brand/marketing designer | Create marketing assets, presentations, social content | Using a pre-built template or imported brand kit | ~10% |
| UX researcher | Facilitate workshops in FigJam, share clickable prototypes for testing | Sharing a prototype link with test participants | ~5% |

*MAU share is inferred from public information and industry benchmarks, not internal Figma data.*

### When Figma wins vs. loses

**Wins when:**
- A cross-functional team (design + PM + engineering) needs a shared source of truth for product design.
- Design systems matter - teams need components to stay in sync across files and teams.
- Design review needs to be fast, async, and contextual (comments on the canvas, not on screenshots).
- Engineers need to inspect designs without asking designers for measurements.
- Teams are doing digital product design (mobile, web, SaaS).

**Loses when:**
- Work requires advanced animation or motion design - After Effects or Principle wins on keyframe precision and timeline control.
- Print or publishing workflows dominate - Adobe InDesign wins on typography and print fidelity.
- Photo editing or raster manipulation is central - Photoshop wins.
- File size is enormous (100k+ nodes, complex brand system) - browser rendering introduces lag that native apps avoid.
- The team's output is directly AI-generated code, bypassing the design layer entirely (v0, Cursor threat).
- Team is deep in Adobe CC workflows - Creative Cloud libraries, asset links, and licensing create migration inertia that Figma hasn't solved.

---

## 2) Product model (objects + primitives)

### Core objects

| Object | Role | Key behaviour |
|---|---|---|
| **File** | Root design container | Lives at a URL; shareable by link; version history attached |
| **Page** | Logical section within a file | Separate canvas per page; pages share components from the same file |
| **Frame** | Container for design content | Maps to a screen, component, or artboard; basis for prototyping and export |
| **Component** | Reusable design element | Has a master (main component) and instances; instances sync to master |
| **Variant** | State or variation of a component | Button/primary/hover, Button/secondary/default - organised in component sets |
| **Auto Layout** | Layout engine for frames | CSS Flexbox-like behaviour; direction, gap, padding, fill/hug/fixed sizing |
| **Library** | Published set of components/styles | Shared across files in a team or org; changes to master propagate to consumers |
| **Variable** | Design token | Stores color, spacing, typography, or boolean values; connected to components and styles |
| **Prototype** | Clickable flow between frames | Interactions: on-click, on-hover, drag; transitions; overlays; smart animate |
| **Dev Mode** | Engineer-facing inspection layer | CSS snippets, measurements, assets, design tokens, code connect for Storybook |
| **FigJam** | Online whiteboard | Sticky notes, shapes, connectors, voting widgets, stamps; separate from design files |
| **Branch** | Parallel design exploration | Creates a copy of a file at a point in time; can be merged back to main |
| **Section** | Canvas organisation layer | Groups frames on the canvas; shown in prototype navigation; improves file legibility |

### Key primitives

- **Share link:** Every file, prototype, and FigJam is a URL. This is the entire collaboration model in one primitive.
- **Components and instances:** Design once, use everywhere. Instance overrides (text, images) don't break the link to the master component.
- **Auto Layout:** Turns static frames into responsive containers. The moment a designer adds Auto Layout, their component behaves like a coded element.
- **Publish library:** One click pushes a component library to a team or org. Consumers see an "update available" badge when the master changes.
- **Dev Mode inspect:** Engineers switch a toggle and see the file as a spec sheet - measurements, CSS, colour values, assets, component annotations.
- **Variables and tokens:** Color, spacing, and typography variables link design decisions to code tokens. A theme change propagates across the entire file.
- **Cursor multiplayer:** Real-time cursors for simultaneous editing. Multiple designers in the same file, with named cursors and live selection outlines.
- **Code Connect:** Links Figma components to their Storybook/codebase counterparts. Engineers see the real component code in Dev Mode, not generated CSS.

---

## 3) Core loops

### Loop A - Design -> Comment -> Revise (review loop)

```
Designer creates or updates frames ->
shares file URL with PM/stakeholder/engineer ->
reviewers open browser, leave contextual comments on canvas ->
designer resolves comments, revises ->
next reviewer opens same URL (no re-sharing needed)
```

**Reinforcement:** Because the file is a URL, reviewers never need a new link. Comments are contextual - pinned to the exact frame or element being discussed. Every resolved comment is a history of decisions.

**Why this is the core loop:** Before Figma, review meant screenshots in Slack, PDFs, or InVision prototypes. The URL model eliminated version confusion. "Which design file is current?" became "always the link in Notion/Linear."

**Loop metric:** Comments left per shared file per week. A healthy review loop shows 3-8 comments per file per feature cycle. Zero comments means the engineer is not using Figma as the source of truth; the loop has broken.

---

### Loop B - Build -> Publish -> Consume (design system loop)

```
Design systems team builds components in a library file ->
publishes library to team or org ->
product designers use instances in their product files ->
master component updated -> consumers see "update available" ->
accept update -> instances stay in sync
```

**Reinforcement:** Every team file that consumes the library increases the dependency. A company's design system living in Figma is irreplaceable - it would take months to recreate in any other tool with equivalent sync behaviour.

**Lock-in depth:** A team that has consumed a library for 12 months will have 10,000+ component instances across their files. Switching to a different tool means re-creating every component from scratch in a new system. This is the deepest switching cost in the product.

**PM signal:** Teams with at least one published and consumed shared library show dramatically higher retention. Library adoption is the B2B equivalent of Notion's "relation created" event - a strong predictor of long-term lock-in. Target: 60% of Professional+ teams publishing a shared library within 90 days of account creation.

---

### Loop C - Design -> Prototype -> Test (validation loop)

```
Designer links frames with prototype connections ->
defines interactions (on-click, overlay, smart animate) ->
shares prototype URL with stakeholders or test participants ->
stakeholders navigate prototype in browser ->
designer iterates on feedback in the same file
```

**Reinforcement:** The prototype and the design file are the same file. There is no export step, no separate tool. Iterating the design automatically updates the prototype. This is the core moat against InVision and Zeplin, which required a separate prototype sync step.

---

### Loop D - Design -> Dev Mode -> Build (handoff loop)

```
Design finalised and annotated ->
engineer opens file in Dev Mode ->
inspects frames for CSS, measurements, assets, token values ->
copies code snippets or links to Storybook components (via Code Connect) ->
builds feature; returns to file to QA against design
```

**Reinforcement:** Dev Mode makes the file a living spec that never goes stale. The engineer's source of truth is the same file the designer is working in. Zeplin and Avocode become redundant. Code Connect deepens the lock-in by linking Figma components to the actual codebase - once integrated, Dev Mode is the highest-fidelity spec tool in the engineer's workflow.

**Monetisation implication:** Engineers were free viewers. Dev Mode is a paid seat ($15-25/month inferred). The upgrade trigger is the moment an engineer needs code annotations, token values, or Code Connect access that is blocked behind Dev Mode. This is a pull-based monetisation trigger - engineering teams push for Dev Mode because it saves them time, not because design teams push it.

---

### Loop E - Brainstorm -> FigJam -> Align (workshop loop)

```
PM or designer creates a FigJam board ->
invites team (free viewer seats) ->
facilitates async or live workshop (sticky notes, voting, diagramming) ->
outputs (decisions, flows, wireframes) live alongside the design file
```

**Reinforcement:** FigJam competes with Miro and Confluence whiteboard. Its tight integration with Figma design files - linking a FigJam section to a Figma frame - is the differentiator. The risk is that FigJam's diagramming and facilitation surface is narrower than Miro's, making it weak for teams that do heavy process mapping, org chart creation, or technical diagramming.

---

## 4) Funnels

### Activation funnel

| Stage | Observable signal | Drop-off risk | PM lever | Target metric |
|---|---|---|---|---|
| Sign-up | Email/Google/SSO; starter workspace created | Low | Frictionless auth | >90% completion |
| First file | User creates a design file or opens a shared one | Medium - "blank canvas" paralysis | Template gallery; auto-open starter template | >70% within 24h |
| First frame | User creates a frame (screen) | Medium - Auto Layout learning curve | Contextual tooltip; guided frame creation | >55% within 48h |
| First collaboration | User shares a file or receives a comment | **High** - solo designers may never share | Nudge to share after first frame is complete | >35% within 7 days |
| Cross-functional open | Engineer or PM opens the file and views it | **Critical** - this is the team activation event | Invite flow prompt after first file is ready for review | >25% of new teams within 14 days |
| Library adoption | Team publishes and consumes a shared component library | Low if reached - extremely sticky | Library setup wizard for teams; highlight when a component is reused 5+ times | Target: 40% of Professional teams within 90 days |

**Key insight:** Figma's activation has two distinct surfaces. Solo designers activate through design quality (creating good components); team activation requires a cross-functional touchpoint (an engineer or PM actually opens a file). The second cliff is the harder one to solve with PLG because it depends on someone else's behaviour - the designer cannot make the engineer open the file.

**Cohort retention pattern (inferred):**
- Designers who share a file and receive a comment in the first 7 days show ~2x better M3 retention than designers who only edit files solo.
- Engineering teams that activate Dev Mode within 30 days of a design file being shared have ~85% probability of still using Figma at M6.
- Teams that adopt a shared library within 90 days have near-zero churn - library lock-in is functionally irreversible.

### Retention cohort signals

| Milestone | Inferred retention lift | PM priority |
|---|---|---|
| File shared with non-designer | +40% D30 retention vs. solo users | High - instrument and track "first share to non-editor" event |
| Non-designer opens file | +35% D30 retention above "file shared" | High - track `file_opened_by_viewer` event |
| Comment left by non-designer | +50% D30 retention vs. "file opened" | Critical - this is the Aha moment |
| Dev Mode session by engineer | +60% M3 retention for the team | High - track first `devmode_session_started` event |
| Library published and consumed | Near-zero churn from this cohort | Critical - track `library_first_consumed_by_second_team` event |

### Monetisation mechanics

| Trigger | Upgrade path | PM lever | Inferred conversion rate |
|---|---|---|---|
| 3-project limit on Free | Free -> Professional | Surfacing "project limit reached" at project creation; showing unpublishable drafts | ~8-12% of Free users who hit the limit |
| Version history limited to 30 days | Free -> Professional | Surfacing old version needed but unavailable | Low frequency; strong emotional trigger when it fires |
| Shared library consumption across teams | Professional -> Organization | When a designer needs a library from another team and can't access it | High conversion - cross-team library access is a blocker, not a nice-to-have |
| SSO, SCIM, advanced permissions | Organization -> Enterprise | Security settings discovery; IT admin path | Primarily outbound/sales-led; low inbound conversion |
| Dev Mode full access | Viewer -> Dev Mode seat | Engineer blocked from code annotations or Code Connect | High if engineer is already using the file for handoff; pull-based upgrade |
| Branching | Professional+ | When a designer wants to explore without breaking the main file | Feature awareness is low; in-product discovery is the lever |

**Key insight:** Viewers are free. This is the defining PLG decision. Engineers, PMs, and stakeholders can open and inspect Figma files forever at no cost. This drives viral growth - every team a designer works with becomes a Figma user - and creates a huge install base of engineers who push their orgs to adopt Dev Mode (paid). Figma monetises when viewers become Dev Mode seats or when admins need org-level controls.

---

## 5) Event schema (inferred)

These are the critical tracking events that drive the activation and retention model. Field names are inferred from observable product behaviour.

```json
{
  "event": "file_shared",
  "properties": {
    "file_id": "string",
    "share_type": "view_only | edit | prototype",
    "recipient_role": "editor | viewer | dev_mode",
    "is_external": "boolean",
    "team_id": "string",
    "org_id": "string"
  }
}
```

```json
{
  "event": "file_opened_by_non_editor",
  "properties": {
    "file_id": "string",
    "viewer_role": "viewer | dev_mode",
    "days_since_file_created": "integer",
    "entry_point": "share_link | email_invite | org_file_browser",
    "team_id": "string"
  }
}
```

```json
{
  "event": "comment_created",
  "properties": {
    "file_id": "string",
    "commenter_role": "editor | viewer | dev_mode",
    "is_first_comment_on_file": "boolean",
    "comment_type": "canvas_pin | prototype | general",
    "has_mention": "boolean",
    "mentioned_user_role": "editor | viewer | dev_mode"
  }
}
```

```json
{
  "event": "devmode_session_started",
  "properties": {
    "file_id": "string",
    "user_role": "dev_mode | viewer",
    "session_depth": "inspect_only | copied_css | copied_token | opened_code_connect",
    "team_id": "string",
    "org_id": "string"
  }
}
```

```json
{
  "event": "library_published",
  "properties": {
    "library_file_id": "string",
    "scope": "team | org",
    "component_count": "integer",
    "variable_count": "integer",
    "publishing_user_role": "editor | admin",
    "team_id": "string"
  }
}
```

```json
{
  "event": "library_consumed",
  "properties": {
    "library_file_id": "string",
    "consuming_file_id": "string",
    "is_first_consumption": "boolean",
    "consuming_team_id": "string",
    "library_team_id": "string",
    "cross_team": "boolean"
  }
}
```

```json
{
  "event": "ai_make_designs_generated",
  "properties": {
    "prompt_length": "integer",
    "frames_generated": "integer",
    "uses_org_library_components": "boolean",
    "outcome": "accepted | edited | deleted",
    "time_to_outcome_seconds": "integer",
    "file_id": "string"
  }
}
```

---

## 6) User journeys

### Happy path - Product designer on a new feature

1. Designer receives feature brief from PM in Linear/Notion.
2. Opens Figma; creates a new file in the team project; starts from a component kit frame.
3. Drops in components from the shared design system library; arranges screens in a flow.
4. Adds Auto Layout to the main container; the frame becomes responsive.
5. Links frames with prototype connections; shares the prototype URL with PM in Slack.
6. PM opens in browser (no install, free viewer); leaves three comments pinned to specific frames.
7. Designer resolves comments inline; updates the designs; PM re-opens the same URL and sees the changes.
8. Engineer is added as a viewer; switches to Dev Mode; copies the CSS for the primary button component.
9. Engineer builds the feature; opens the file for QA; annotates a mismatch directly in the canvas.

**Value delivered:** PM, designer, and engineer used the same file for the entire feature lifecycle - brief to QA - with no screenshots, no Zeplin links, no version confusion.

**Key events fired:** `file_shared`, `file_opened_by_non_editor`, `comment_created`, `comment_resolved`, `devmode_session_started`

---

### Happy path - Design systems lead setting up a new org library

1. Design systems lead creates a "Component Library" file in the org's shared project.
2. Builds master components for Button, Input, Card, Navigation - with variants (primary/secondary/ghost, default/hover/active/disabled).
3. Adds color variables (`brand/primary`, `semantic/danger`, `semantic/success`) and text styles.
4. Publishes library to the entire org.
5. Product designers in 5 different teams accept the library; they see the org's components in their asset panel.
6. Three months later, the systems lead updates the primary brand color from `#0066FF` to `#0055EE`.
7. All 5 product teams see "Library update available"; they accept; 2,400 component instances update across 80 files simultaneously.

**Value delivered:** One design change propagates to the entire organisation in seconds. This is not possible with any local-file design tool.

**Key events fired:** `library_published` (scope: org), `library_consumed` x5 teams, `library_update_accepted` x80 files

---

### Edge case 1 - Large file performance degradation

**Scenario:** A mature product team has built a 3-year-old design file with 800+ screens, 200+ components, and 14 pages.

**Failure pattern:**
1. File takes 25+ seconds to open in the browser.
2. Selecting a component with many instances triggers a multi-second lag.
3. Real-time collaboration (multiplayer cursors) causes frame-rate drops when 3+ designers are in the file.
4. Auto Layout recalculations on a complex nested frame take 3-5 seconds.
5. Team begins splitting work across multiple files to manage performance - breaking the "one source of truth" promise.
6. Shared library becomes fragmented across files; sync guarantees break.

**PM implication:** Performance at scale is Figma's most persistent trust problem with enterprise design teams. The browser-native architecture creates a hard ceiling. Figma's response (Figma Slides, breaking large files into components) treats the symptom. The underlying fix requires native rendering or WASM-based offloading of the layout engine. This is an architectural problem, not a product bug.

**Metric to track:** File open P95 latency for files with >500 nodes. Current observable benchmark: ~4-8 seconds for complex files. Target: <4s. Regression threshold: alert if >10s P95 for any file cohort.

---

### Edge case 2 - AI-generated design that breaks the design system

**Scenario:** PM asks a junior designer to use Figma AI's Make Designs to generate a first draft of a settings page.

**Failure pattern:**
1. Make Designs generates a plausible-looking settings UI in seconds.
2. The generated frames use hardcoded colors and font sizes, not the org's variables or component instances.
3. Junior designer ships the generated design to the engineer without auditing component usage.
4. Engineer builds from the generated spec; the settings page now has 6 different shades of grey instead of the 2 in the design system.
5. QA flags 14 design inconsistencies; rework costs more time than designing from scratch.
6. Team bans AI generation for any spec that will be handed off to engineering.

**PM implication:** AI generation that ignores the design system is a trust-destroying anti-feature for enterprise teams. Figma's AI must be design-system-aware - generating components from the org's library, not from generic patterns - to earn adoption in organisations where design consistency is a business requirement. The `uses_org_library_components` field in the `ai_make_designs_generated` event schema is exactly the signal to track.

---

### Edge case 3 - v0/Cursor routing around the design file

**Scenario:** A startup PM writes a feature description in their Linear ticket. Instead of briefing a designer, they paste it into v0, iterate three times, and hand the React component directly to a frontend engineer.

**Failure pattern (from Figma's perspective):**
1. No Figma file is ever created for the feature.
2. No designer reviews the UI before it ships.
3. The engineer ships the v0-generated component with hardcoded styles, not the design system's tokens.
4. Over 6 months, the product accrues 40 features built this way - no design spec, no design review, no Figma history.
5. When the team eventually hires a designer, there is no source of truth to work from. The designer must reverse-engineer the UI from the live product.

**PM implication:** This is the existential loop for Figma. If "PM writes brief -> AI generates production code" becomes the dominant workflow for new feature development, the design file step is removed from the process for a meaningful segment of Figma's TAM. Figma's response must be to become the place where AI-generated UIs are reviewed, refined, and maintained - not just where they are generated.

---

## 7) Competitive positioning

| Competitor | Overlap | Where they win | Figma's counter | Figma win/loss verdict |
|---|---|---|---|---|
| **Sketch** | UI design, components | Native macOS performance on large files (no browser lag); long-established plugin ecosystem | Browser-native, real-time collab, cross-platform, Dev Mode | **Figma wins** for any cross-functional team; Sketch retains solo macOS-native designers in small agencies |
| **Adobe XD** | UI/UX design, prototyping | Adobe CC integration | XD was discontinued in 2023; Figma won outright | **Figma wins** by default |
| **Framer** | Prototyping, interactive design | Real production-grade code output (React); advanced motion/animation; responsive breakpoints | Figma is broader; Framer is niche for high-fidelity prototype specialists | **Figma wins** on breadth; **Framer wins** for teams where the prototype IS the production frontend |
| **Miro** | Whiteboarding, diagramming, workshops | More powerful diagramming templates; richer facilitation tooling; better for process/org charts | FigJam is embedded in the design workflow; Miro requires a tool switch | **Miro wins** for enterprise facilitation power users; **Figma wins** on design-adjacent workshops |
| **Penpot** | Open-source UI design | Self-hosted (data sovereignty for regulated industries); free for teams; SVG-native format | Figma's polish, performance, plugin ecosystem, and enterprise support are materially ahead | **Penpot wins** for compliance-constrained orgs (healthcare, government); **Figma wins** everywhere else |
| **Canva** | Marketing design, templates | Non-technical users; vast template library; drag-and-drop simplicity; brand kit for marketers | Figma dominates product/digital design; Canva owns brand/marketing for non-designers | **No direct conflict** - different primary buyer (designer vs. marketer) |
| **Zeplin** | Developer handoff | Dedicated handoff workflow; annotation layer; styleguides | Dev Mode replaced Zeplin for teams already in Figma | **Figma wins** for teams using Figma for design; **Zeplin** retains teams with mixed tool stacks (Sketch + Zeplin) |
| **v0 (Vercel)** | AI-to-UI generation | Generates production-ready React/Tailwind code directly from prompt | Figma can generate design files; v0 routes around the design file entirely | **Existential risk** - v0 removes the design step, not the design tool |
| **Cursor / Lovable** | AI-assisted frontend development | Entire design-to-code step skipped for AI-native workflows | Same threat as v0; Figma's answer must be design-system-aware AI + code connect | **Existential risk** - same as v0 |

**Figma's true moat:** The combination of shared component libraries, live URLs, and Dev Mode creates a three-sided network effect: designers, engineers, and PMs are all in the same file. The switching cost is not just a tool preference - it is the entire design system, version history, and review workflow of the organisation. That is extremely difficult to replicate.

**The existential threat:** v0 and Cursor are not design tools - they don't compete with Figma directly. But if "PM writes a brief, AI generates production code" becomes the dominant workflow for new features, the design file step is removed from the process. Figma's response is Make Designs and code connect - but the pace of AI-to-code tools is faster than the pace of AI-to-design tools. This is the defining strategic question for Figma in 2025-2026.

---

## 8) AI strategy deep-dive

### Current AI surface (publicly observable, as of 2024-2025)

| Feature | What it does | Where it breaks |
|---|---|---|
| **Make Designs** | Generates frames from a text prompt | Hardcoded styles; ignores org library; generic layout patterns |
| **Rename Layers** | AI renames messy layer names in bulk | Inconsistent naming conventions; misidentifies component boundaries |
| **Remove Background** | One-click background removal on images | Low quality on complex backgrounds; fails on product screenshots with gradients |
| **Generate Copy** | Suggests placeholder text for UI elements | Generic; not aware of brand voice or tone |
| **Auto Alt Text** | Generates accessibility alt text for images | Low specificity; often generic ("image of a button") |
| **FigJam AI (summarise, sort)** | Summarises sticky notes, sorts by theme | Loses nuance in complex discussions; works best on short sticky notes |

### AI state machine for Make Designs

```
IDLE ->
  [user opens blank frame + types prompt] ->
GENERATING (loading indicator, 3-8 seconds) ->
  -> [frames generated] ->
REVIEW ->
  -> [user accepts] -> EDITING (normal Figma canvas) -> DONE
  -> [user edits one frame] -> PARTIAL_ACCEPT -> EDITING -> DONE
  -> [user selects all, deletes] -> IDLE
  -> [generation times out or fails] -> ERROR -> IDLE
```

### AI product gap: design-system-aware generation

The critical capability Figma AI is missing as of 2025: when `Make Designs` generates a settings page, it should:
1. Read the org's published component library.
2. Map each generated element to the nearest matching component (Button, Input, Card).
3. Apply the org's color variables, not hardcoded hex values.
4. Apply the org's text styles, not arbitrary font sizes.
5. Generate a frame where every element is an instance of a published component.

Until this gap is closed, `Make Designs` is a prototyping tool for external stakeholder demos - not a reliable handoff tool for engineering.

**Competitive risk:** If Framer or a new entrant ships design-system-aware AI generation before Figma does, they capture the high-value enterprise design workflow. Figma's advantage is that they own the design system data already - the library files are in Figma. The question is whether they productise this data access into the AI generation pipeline.

---

## 9) Key trade-offs

| Trade-off | Current bias | Cost | Alternative |
|---|---|---|---|
| Browser-native vs. native performance | Browser-first | Lag on large files; no GPU access; memory limits | WASM offloading of the layout engine; or an optional native shell that still uses shared file infrastructure |
| Viewer-free model vs. per-seat revenue | Viewers free; editors paid | Undermonetises large orgs with high viewer-to-editor ratios | Dev Mode tiers for engineers (Figma is moving this direction); Figma AI seats for non-designer users |
| All-in-one (design + whiteboard + handoff) vs. best-in-class | All-in-one | FigJam weaker than Miro; Dev Mode weaker than dedicated Storybook integration | Deep integrations that make specialised tools feel native inside Figma |
| AI that generates vs. AI that knows your system | Generic generation (Make Designs) | AI output breaks design systems; enterprise distrust | Design-system-aware generation (instances from your library, not generic patterns) |
| Component library centralisation vs. team autonomy | Centralised org library | Design system teams become bottlenecks; product teams fork components | Governed federation: product teams extend without breaking the core; Figma's "component extensions" pattern is a partial answer |
| Prototyping fidelity vs. production code | High-fidelity static prototypes | Gap between prototype and production still exists; Framer and v0 bridge it with code output | Code Connect to Storybook is the current partial answer; production-ready output requires a bigger product bet |
| Dev Mode as free viewer vs. paid seat | Moving to paid | Risks engineering community backlash; Dev Mode was long used for free inspection | Tiered Dev Mode (basic inspection free; Code Connect, annotations, token access paid) reduces backlash while monetising power usage |

---

## 10) Metrics framework

### North Star

**Weekly active editors in files with at least one shared library component instance**

Rationale: Captures both individual design activity (editing) and design system adoption (library instance). Teams in this cohort have crossed the lock-in threshold - they are building on top of a shared system, not just using Figma as a drawing tool. This cohort shows near-zero churn.

### Input metrics (leading indicators)

| Metric | Target | Why it matters |
|---|---|---|
| % of new Professional teams publishing a shared library within 30 days | >40% | Design system activation rate; highest predictor of retention |
| % of design files opened by at least one non-designer (engineer or PM) within 7 days of creation | >50% | Cross-functional reach; validates collaboration loop is working |
| Dev Mode sessions per active engineering team per week | >3 sessions/team/week | Handoff loop depth; indicates engineers are using Figma as source of truth |
| `ai_make_designs_generated` sessions where `uses_org_library_components = true` | Baseline + 20% QoQ | Design-system-aware AI adoption; key quality metric for AI strategy |
| Comments left per file per week per reviewer | 2-6 comments | Review loop health; <2 suggests reviewers are not engaging; >10 suggests friction |
| Library update acceptance rate | >80% | Design system health; decline signals designers are forking rather than accepting updates |

### Guardrail metrics

| Metric | Threshold | Why it's a guardrail |
|---|---|---|
| File open P95 latency for files with <500 nodes | Must stay <3s | Performance regression would break the "open any file instantly" promise |
| Multiplayer sync error rate per 1,000 sessions | Must not increase MoM | Sync errors destroy trust in real-time collaboration - the core product promise |
| Free -> Professional 30-day conversion rate | Must not decline with any pricing or AI changes | Monetisation health; a decline means PLG funnel is leaking |
| Dev Mode adoption by engineering teams | Must grow QoQ | If it plateaus, the viewer -> paid-seat strategy is stalled |

---

## 11) Risks and failure modes

1. **Browser performance ceiling:** Large files in the browser are noticeably slower than native apps. This is not a fixable bug - it is an architectural constraint. As design systems scale, performance will degrade. Enterprise teams at 500+ designers will hit this wall and consider native alternatives.

2. **AI that ignores the design system:** Make Designs generates generic UI, not system-compliant UI. In any org with a mature design system, this makes AI generation harmful (creates inconsistencies) rather than helpful. Figma must solve design-system-aware generation before AI becomes a net positive for enterprise.

3. **v0 / AI-to-code routing around the design layer:** If AI code generation tools mature to the point where PMs can describe a feature and receive production-ready, brand-correct frontend code, the design file step may be skipped for new feature development. Figma's design-to-code strategy (code connect, variables, Dev Mode) is the hedge, but it is not yet good enough to claim ownership of this workflow.

4. **Viewer monetisation ceiling:** The free viewer model drives viral growth but caps revenue per enterprise account. A company with 500 engineers viewing Figma files and 30 designer editors pays for 30 seats, not 530. As Figma moves into Dev Mode tiers (paid engineer seats), it risks backlash from the engineering community that has long treated Figma as free.

5. **Design system governance at scale:** As org libraries grow, the design system team becomes a bottleneck. Product teams fork components to move faster; the library diverges; the "single source of truth" promise breaks. Notion has the same entropy problem (page sprawl) but Figma's version of it affects engineering output directly - inconsistent UI ships to production.

6. **FigJam is losing to Miro:** FigJam captured early adoption from teams already in Figma, but Miro has a more powerful facilitation feature set, broader diagramming templates, and a head start in enterprise. If FigJam doesn't differentiate beyond "Miro but in Figma," it risks becoming a feature, not a product.

7. **Enterprise sales dependency:** Figma's growth path above $50M ARR per enterprise customer requires a real outbound sales motion for SSO, SCIM, and Organisation tier. Pure PLG does not close enterprise security reviews. As Figma targets larger orgs, the sales/CS investment must grow faster than product-led growth, compressing margins.

---

## 12) Experiment backlog

| Experiment | Hypothesis | Primary metric | Guardrail | Priority |
|---|---|---|---|---|
| Design-system-aware Make Designs | AI uses org library components when generating frames; reduces post-generation editing to <20% of nodes | `uses_org_library_components` rate in `ai_make_designs_generated`; time-to-handoff for AI-generated frames | Manual edit distance after AI generation (must decrease) | P0 - existential for AI strategy |
| Dev Mode freemium tier | Basic inspection (measurements, colors) stays free; Code Connect, token annotations, and design specs move to paid | Dev Mode paid seat conversion rate | Engineering community NPS; Figma file sharing rate (must not decline) | P1 - monetisation lever |
| Cross-functional invite nudge | After a designer's first frame is complete, show an inline "share with your engineer" prompt | `file_opened_by_non_editor` within 7 days rate (target: +15% relative) | Time-to-first-frame (must not increase - nudge must not interrupt flow) | P1 - activation lever |
| Library setup wizard for new teams | Guided onboarding for Professional teams walks through creating and publishing a shared library | % of Professional teams publishing a library within 30 days (target: +10% relative) | Time-to-first-library (must not exceed 20 minutes for guided path) | P1 - retention lever |
| Branching discoverability | In-canvas tooltip when a designer creates a new page for a major redesign: "Use branching to explore this safely" | Branch creation rate per Professional+ file | Page creation rate (must not decrease - must not confuse experienced users) | P2 |
| FigJam AI meeting summary -> action items | Post-meeting FigJam board auto-generates action items as sticky notes linked to a Figma project | FigJam sessions per team per week; action item stickies created | Manual sticky note creation rate (must not decline - must augment, not replace) | P2 |
| Figma AI context: workspace-grounded generation | AI reads the user's recent files before generating copy or structure suggestions | AI response acceptance rate (target: +25% relative vs. generic generation) | AI generation latency P95 (must stay <8s) | P1 |

---

## 13) What I'd do as PM - 30-60-90 day plan

### 30 days - Diagnose
- Audit the activation funnel for the cross-functional cliff: what % of new design files are opened by at least one non-designer in the first 7 days? What triggers that first non-designer open?
- Instrument `devmode_session_depth` across engineering teams: are engineers doing meaningful inspection (copying code, reading tokens) or just opening the file and leaving?
- Run 10 user interviews with enterprise design systems leads: what is their biggest pain point with library governance at scale? What do they work around Figma to solve?
- Review `ai_make_designs_generated` usage and abandonment: what % of generated frames are used in final designs vs. immediately deleted? What is the edit distance between generated and shipped?
- Map the v0/Cursor overlap: are there product teams in the user base who have reduced Figma usage because they are generating frontend code directly?

### 60 days - Experiment
- Launch cross-functional invite nudge A/B test (see experiment backlog).
- Prototype design-system-aware Make Designs: connect AI generation to the org's published library. Run internal dogfood with 3 enterprise design systems teams.
- Ship Dev Mode freemium tier scoping: define which capabilities stay free vs. paid; model the revenue impact and community sentiment risk.

### 90 days - Evaluate and scale
- Review D7 and D30 retention lift from cross-functional invite nudge. If >10% relative lift, roll out to 100% of new design files.
- Evaluate design-system-aware Make Designs acceptance rate vs. control (generic generation). If `uses_org_library_components` rate is >50% for org-library-enabled teams, accelerate to GA.
- If Dev Mode freemium tier analysis shows >15% paid conversion of active Dev Mode users without NPS decline, move to implementation.

---

## 14) Open questions

1. What is Figma's actual D30 retention rate for teams where at least one non-designer opened a file in the first 7 days vs. teams where no non-designer opened a file?
2. Does `library_first_consumed_by_second_team` predict 12-month retention better than any other single event? What is the actual churn rate for teams past this threshold?
3. What % of `ai_make_designs_generated` outputs are used in files that eventually get shared with an engineer? What is the defect rate (design system violations) in these files?
4. Is the engineering community backlash against Dev Mode paid tiers severe enough to measurably reduce `file_opened_by_non_editor` rates in orgs where Dev Mode is gated?
5. What is the actual revenue per enterprise customer from Dev Mode seats vs. Organisation tier admins? Which is growing faster?
6. Does Figma have internal data showing a correlation between library update acceptance rate and team-level churn? Is there a "acceptance rate cliff" below which teams start to fork?
7. How does Figma's WebGL/WASM rendering roadmap address the large-file performance ceiling? Is there an internal 2-year plan to move the layout engine off the main browser thread?

---

*All metrics are directional estimates based on public information and observable UX patterns, not internal Figma data.*
