# Qualification framework

## ICP definition

**Company type**
B2B companies with an existing website and inbound lead flow

**Company stage**
Growth-stage startups, scale-ups, SMBs with established sales processes. Not pre-revenue or idea-stage.

**Industries**
Industry-agnostic. Any B2B company with a sales team. Higher fit: SaaS, Professional Services, Agencies, Fintech, Edtech. **Hard exclusion: Adult/18+ content.**

**Inbound volume**
50+ website leads/month (enough to justify automation)

**Sales team**
Has at least 1 dedicated sales person / SDR. Agent augments, doesn't replace.

**Current pain**
Slow lead response, sales team overwhelmed, no 24/7 coverage, low website-to-call conversion

**Tech stack**
Has or is willing to adopt a CRM. Has a website where a chat widget can be embedded.

**Budget**
€5,000+ (starting price, subject to change)

**Timeline**
X+ weeks to go-live (realistic for MVP delivery)

**Decision authority**
Founder, Head of Sales, VP Marketing, or COO — someone with budget authority over sales tooling

**Geography**
Global, English-speaking markets. **Hard exclusion: russia-based companies.**

**Engagement model**
Project-based (MVP build) with optional retainer for iteration/support

## Suggested Lead Qualification Framework

[General qualification flow](https://www.notion.so/General-qualification-flow-30d31a911e9c80d59efdc2861941a436?pvs=21)

### How This Differs from the General Flow

The general qualification flow handles all Halo Lab services from the main website. The Sales AI Agent landing page serves a more specific, higher-intent audience. The differences below apply identically to both BANT and CHAMP frameworks.

**1. Greeting + Introduction**
Sales AI Agent-specific — agent introduces itself as a specialist for inbound lead qualification, not a generalist Halo Lab assistant.

**2. DISCOVERY_PROJECT → DISCOVERY_USE_CASE**
Replaced entirely. Instead of asking about a project, the agent asks: what should the Sales AI Agent do — qualify leads, book meetings, handle FAQs, handle objections?

**3. DISCOVERY_TECHNICAL → DISCOVERY_CURRENT_STACK**
Reframed to focus on existing sales tooling: CRM (HubSpot/Salesforce/Pipedrive), website platform, chat tools, integrations needed (Calendly, Slack, email).

**4. DISCOVERY_VOLUME (new step)**
No equivalent in general flow. Collects leads/month and estimated conversation volume — critical for Botpress plan scoping and pricing.

**5. DISCOVERY_OPTIONAL removed**
Inspiration/preferences questions removed — not relevant for a B2B AI development service.

**6. Contact form purpose**
The contact form is a knowledge-gap escalation tool only — not tied to lead quality. It can appear at any point, for any lead category, in both options.

**7. Warm lead conversion path**
Warm leads (Option B) go to Calendly tagged as Warm.

**8. Nurture outcome**
Nurture leads receive resources → re-qualification → soft Calendly nudge. Not a dead end as in the current PRD.

### CHAMP Framework

CHAMP is a modern qualification framework that leads with the prospect's Challenges rather than budget. This produces more natural, conversational discovery — especially for a chat widget where the visitor can disengage at any moment. Budget (Money) is explored after the agent has established relevance and trust.

#### Detailed Characteristics

**Best for**
Consultative B2B services, custom solutions, complex buying processes.

**Risk of DQ'ing good leads**
Lower — challenge-first approach keeps more prospects in the funnel.

**AI Agent fit**
Aligns with conversational AI's strength: natural discovery through dialogue.

**Ease of implementation**
Slightly more nuanced. Requires open-ended discovery. Still 4 elements.

**Time VS Priority**
Asks "how important?" — urgency + ranking among their priorities.

**Budget handling**
Budget discussed later. No budget now ≠ disqualified. Budgets can flex.

**Conversation tone**
Consultative, empathetic. Feels like a discovery call.

**Philosophy**
Buyer-centric: leads with understanding their pain.

**Primary focus**
Challenges — what problem do they have?

#### CHAMP Element Mapping

**CH — Challenges**
- **Maps to:** DISCOVERY_USE_CASE (Step 4) + DISCOVERY_PAIN_POINTS (Step 6)
- **What the agent collects:** Pain points and current problems: too slow to respond, sales team overwhelmed, low conversion, no 24/7 coverage. Motivation for seeking an inbound Sales AI Agent. Challenges surface first — before budget.

**A — Authority**
- **Maps to:** DISCOVERY_COMPANY role (Step 3) + DISCOVERY_BUDGET (Step 8)
- **What the agent collects:** Visitor's role, who else is involved in the decision, whether they have budget access. Authority is confirmed alongside budget, not separately.

**M — Money**
- **Maps to:** DISCOVERY_BUDGET (Step 8)
- **What the agent collects:** Budget range for the project. Floor: €5,000. Explored after challenges are established — visitor with a clear problem but uncertain budget is still valuable.

**P — Prioritization**
- **Maps to:** DISCOVERY_TIMELINE (Step 7)
- **What the agent collects:** Timeline urgency, deadlines, trigger events (product launch, funding round, seasonal traffic). Minimum realistic MVP: X weeks.

---

## Shared Discovery Sequence — Steps 1–9

Steps 1–9 are identical across Option A and Option B. Divergence happens at Step 10.

**Step 1 — GREETING**
- **Goal:** Welcome visitor. Set context immediately.
- **Key questions / content:** "Hi — I'm here to help you figure out if an inbound Sales AI Agent could work for your business. Mind if I ask a few questions?"
- **vs. General flow:** No broad Halo Lab pitch. Visitor already chose this page.

**Step 2 — INTRODUCTION**
- **Goal:** Brief, specific framing of the inbound Sales AI Agent development service.
- **Key questions / content:** What an inbound Sales AI Agent is, what it does (qualify leads, book meetings, handle FAQs), typical outcomes. Sets expectations.
- **vs. General flow:** Replaces generic Halo Lab overview. Visitor has landing page context — agent builds on it.

**Step 3 — DISCOVERY_COMPANY**
- **Goal:** Understand who the visitor is.
- **Key questions / content:** Company name, visitor's role (Founder / Sales Manager / Marketing Lead / Enterprise buyer), industry, team size.
- **vs. General flow:** Same intent as general flow. Role shapes how agent frames value throughout.

**Step 4 — DISCOVERY_USE_CASE** *(CHAMP: Challenges — depth)*
- **Goal:** Understand what they want the agent to do — and why.
- **Key questions / content:** Qualify leads? Book meetings automatically? Answer FAQs 24/7? Handle sales objections? What problem is driving this need?
- **vs. General flow:** Replaces DISCOVERY_PROJECT. Challenges surface here — what is broken or painful in their current sales process?

**Step 5 — DISCOVERY_PAIN_POINTS** *(CHAMP: Challenges — depth)*
- **Goal:** Uncover the real problem and motivation.
- **Key questions / content:** Too slow to respond to leads? Sales team overwhelmed? Low website conversion? No 24/7 coverage? Lost deals to faster competitors?
- **vs. General flow:** Same intent as general flow. Deepens the Challenges signal from Step 4.

**Step 6 — DISCOVERY_VOLUME**
- **Goal:** Understand scale requirements.
- **Key questions / content:** How many leads/month does the website currently receive? Expected conversation volume after launch?
- **vs. General flow:** NEW. No equivalent in general flow. Critical for Botpress plan scoping and pricing.

**Step 7 — DISCOVERY_CURRENT_STACK**
- **Goal:** Map existing sales tech.
- **Key questions / content:** CRM (HubSpot, Salesforce, Pipedrive, none)? Website platform? Current chat tools? Integrations needed (Calendly, Slack, email)?
- **vs. General flow:** Replaces generic DISCOVERY_TECHNICAL. Focused on sales tooling, not dev stack.

**Step 8 — DISCOVERY_TIMELINE** *(CHAMP: Prioritization)*
- **Goal:** Understand urgency and deadlines.
- **Key questions / content:** When do they want the agent live? Is there a trigger event (product launch, funding round, seasonal traffic)?
- **vs. General flow:** Same as general flow. CHAMP Prioritization signal.

**Step 9 — DISCOVERY_BUDGET** *(CHAMP: Money + Authority)*
- **Goal:** Explore budget and decision-making.
- **Key questions / content:** Budget range for the project. Reference floor: €5,000 (starting price: €5,000, subject to change). Who else is involved in the decision?
- **vs. General flow:** Same as general flow. Budget floor is €5,000 — confirmed. Authority question collected here alongside budget.

---

## Contact Form — Knowledge Gap Escalation

In both options, the contact form has one specific purpose: escalating questions the agent cannot answer. It is NOT tied to lead quality and can appear at any point in the conversation, for any lead category.

**Trigger**
Visitor asks a question outside the agent's knowledge base — specific pricing, custom integrations, contract terms, technical edge cases.

**Agent behaviour**
Acknowledges the gap honestly. Offers to capture the question and have the sales team follow up. Does NOT invent an answer.

**Example language**
"That's a great question — I don't have enough detail to give you an accurate answer on that. Let me make sure our team gets back to you on it. Can I take your email and note down your question?"

**After form submission**
Agent continues the conversation. Qualification does not stop. The form is a parallel action, not a handoff.

**Data captured**
Name, email, company (pre-filled if collected), the specific question flagged.

**Where it goes**
The contact in the Botpress Users Table created. Question noted in contact record for sales team follow-up.

*Design principle: the agent stays in the conversation after offering the form. The visitor gets continuity; the sales team gets the unanswered question. This prevents both dead-end conversations and hallucinated answers.*

---

## Option A — Soft, Conversational Flow

### Overview

*No automatic scoring · All qualified leads → Calendly · Sales team decides fit*

Mirrors the design philosophy of the existing general qualification flow. The agent collects structured CHAMP data through natural dialogue but does not score or categorise leads. When enough discovery data is collected, the agent presents a Calendly link. All qualification and disqualification judgements are made by the human sales team (DQ is handled manually by the sales team after reviewing lead data — the agent does not automatically disqualify).

### Step 10 — QUALIFICATION_SUMMARY

Agent recaps collected information conversationally and confirms accuracy. No score is announced. CHAMP signals shape the language — if signals are weak, concerns are flagged softly and Calendly is still offered. Phased/MVP approaches are suggested as alternatives when needed.

> **Example:** "Based on what you've shared, it sounds like you're looking to build an inbound lead qualification agent integrated with HubSpot, targeting a go-live in about 6 weeks. Let me connect you with our team to walk through the approach — here's a link to book a time."

### Step 11 — Handoff strategy

Handoff triggers when the agent has collected: company details + visitor role, desired use case, current sales stack, timeline indication, and budget range (even approximate).

**Sufficient data collected**
- **Situation:** All required discovery steps complete. CHAMP signals are positive or mixed.
- **Agent action:** Present Calendly link. Recap key CHAMP points before link.
- **Conversion path:** Calendly link

**Weak signals (budget/timeline concern)**
- **Situation:** Budget below €5,000 or timeline unrealistic.
- **Agent action:** Flag concern softly. Still offer Calendly + phased approach as alternative.
- **Conversion path:** Calendly — sales team assesses fit on the call

**Nurture situation**
- **Situation:** Early-stage, not ready to commit yet.
- **Agent action:** Share resources → check in → ask what needs to change → soft Calendly nudge. See Nurture flow below.
- **Conversion path:** Resources → re-qualify → soft Calendly nudge

**Knowledge gap at any point**
- **Situation:** Visitor asks something outside agent's KB.
- **Agent action:** Offer contact form. Continue qualifying after submission.
- **Conversion path:** Form → Botpress table. Calendly still offered at end if qualified.

**Visitor declines Calendly**
- **Situation:** Lead prefers not to book immediately.
- **Agent action:** Acknowledge. Offer contact form as fallback. Close warmly.
- **Conversion path:** Contact form → Botpress table.

### CHAMP Qualification Signals — Soft, Not Hard Gates

The agent looks for CHAMP signals throughout the conversation. None of these are hard gates — weak signals result in a soft flag, not a rejection.

**CH — Challenges**
- **What the agent looks for:** Clear, specific pain: slow lead response, team overwhelmed, low conversion, no 24/7 coverage.
- **Response to weak signal:** Agent asks follow-up questions to surface the challenge. Still proceeds to Calendly.

**A — Authority**
- **What the agent looks for:** Visitor is the decision-maker, or confirms they have budget access.
- **Response to weak signal:** If not the decision-maker, agent asks who else is involved and whether they should join the call. Still proceeds to Calendly.

**M — Money**
- **What the agent looks for:** Budget at or above €5,000 (starting price: €5,000, subject to change).
- **Response to weak signal:** "Our inbound Sales AI Agent projects typically start at €5,000 — let's have a quick call to see what's possible within your budget." Still proceeds to Calendly.

**P — Prioritization**
- **What the agent looks for:** Timeline of X+ weeks, or a realistic deadline with a clear trigger event.
- **Response to weak signal:** "Timelines under X weeks are tight for this type of build — we can discuss a phased approach on a call." Still proceeds to Calendly.

### Nurture Flow — Option A

When a visitor shows early-stage interest but isn't ready to commit, the agent follows this sequence:

**N1 — Share relevant resources**
- **Example:** "Let me share a couple of examples of inbound Sales AI Agents we've built — these might give you a better feel for what's possible." [share case studies]
- **Outcome:** Visitor sees concrete examples.

**N2 — Check in**
- **Example:** "Did any of those feel relevant to what you're working on?"
- **Outcome:** Keeps conversation open. Agent learns more from response.

**N3 — Re-qualification** *(CHAMP: Challenges revisit)*
- **Example:** "What would need to change on your side for this to become something you'd want to explore more concretely?"
- **Outcome:** Surfaces blockers (budget, timing, internal approval). May upgrade lead.

**N4 — Soft Calendly nudge**
- **Example:** "Even if it's just exploratory, a 20-minute call with our team might help clarify things — no commitment needed. Want me to share the link?"
- **Outcome:** If accepted → Calendly. If declined → warm close.

**N5 — Warm close** *(if Calendly declined)*
- **Example:** "Totally understood — feel free to come back when the timing is better. I'll make a note of your situation so our team has context if you reach out."
- **Outcome:** Conversation ends positively. No hard close.

### Pros & Cons — Option A (CHAMP)

**Pros:**
- CHAMP leads with Challenges — more natural conversation, higher engagement than budget-first approaches
- Lower risk of incorrectly rejecting a good lead due to incomplete data
- Simpler to build in Botpress — no scoring variables or category-conditional branches
- Sales team retains full control over qualification and disqualification decisions

**Cons:**
- Sales team receives all leads including poor fits — more manual filtering required
- No automatic prioritisation — a strong lead and a weak lead both go to Calendly identically
- Calendly slots may be used by leads who aren't a good fit, blocking sales team time

---

## Option B — CHAMP · Scored Flow (Hot / Warm / Nurture / DQ)

### Overview

*Agent evaluates CHAMP signals · Hot + Warm → Calendly (tagged) · Nurture → resources + re-qualify*

Introduces automated lead evaluation based on CHAMP signals. After collecting discovery data, the agent assesses all four CHAMP elements and routes the visitor to the appropriate path. Both Hot and Warm leads are presented with Calendly — but tagged differently so the sales team knows how to prepare. Nurture leads receive resources, re-qualification, and a soft nudge. DQ leads are closed politely.

### Lead Scoring — Four Categories

**Hot**
- **Criteria:** Challenges clear + Money ≥ €5,000 + Prioritization ≥ X weeks + Authority confirmed. Strong ICP fit.
- **Agent action:** Present Calendly link. Tag as Hot.
- **Conversion path:** Calendly

**Warm**
- **Criteria:** Challenges confirmed. Missing 1–2 of: Money / Prioritization / Authority. Potential fit.
- **Agent action:** Present Calendly link. Tag as Warm so sales team prepares differently.
- **Conversion path:** Calendly

**Nurture**
- **Criteria:** Challenges vague or early-stage. Money/timeline not established. Exploring options.
- **Agent action:** Share resources. Re-qualify. Soft Calendly nudge. See Nurture flow below.
- **Conversion path:** Resources → re-qualify → soft Calendly nudge

**DQ**
- **Criteria:** No relevant need, wrong scope, no sales team, spam, or meeting ICP exclusion criteria.
- **Agent action:** Politely disengage. No conversion action.
- **Conversion path:** Thank & close

**Hot vs Warm distinction:** Both go to Calendly — the difference is Botpress Table tagging. Hot = all 4 CHAMP signals confirmed. Warm = Challenges confirmed + fewer than 2 of Money / Prioritization / Authority. Sales team uses the tag to decide prep and follow-up depth.

### CHAMP Signal Weights

**CH — Challenges**
- **Positive:** Use case clearly defined, specific pain articulated
- **Negative:** Vague curiosity, no clear problem
- **Weight:** Required — without this, maximum outcome is Nurture

**A — Authority**
- **Positive:** Decision-maker or confirmed budget access
- **Negative:** Needs manager approval, unclear role
- **Weight:** Strong — differentiates Hot from Warm

**M — Money**
- **Positive:** At or above €5,000 (starting price: €5,000, subject to change)
- **Negative:** Below €5,000 or undefined
- **Weight:** Strong — differentiates Hot from Warm

**P — Prioritization**
- **Positive:** X+ weeks, realistic deadline
- **Negative:** Under X weeks or "someday"
- **Weight:** Moderate — differentiates Hot from Warm

### Step 10 — QUALIFICATION_SUMMARY with CHAMP Scoring

After discovery, the agent internally evaluates the lead against all four CHAMP signals. The summary message is adapted to the category — the score is never announced explicitly, but the language and next step naturally reflect it.

**Hot**
- **Situation:** All 4 CHAMP signals confirmed.
- **Agent action:** "This sounds like a great fit. I'd love to get you on a call with our team — here's a link to book a time."
- **Conversion path:** Calendly → Botpress Table (tag: Hot)

**Warm**
- **Situation:** Challenges confirmed. Missing 1–2 signals.
- **Agent action:** "There's definitely something to explore here. Let me get you connected with our team — here's a link to book a call."
- **Conversion path:** Calendly → Botpress Table (tag: Warm)

**Nurture**
- **Situation:** Challenges vague or Money/Prioritization not established.
- **Agent action:** Resources → check in → re-qualification → soft Calendly nudge. See Nurture flow below.
- **Conversion path:** Resources → re-qualify → soft Calendly nudge

**DQ**
- **Situation:** No relevant need, wrong scope, no sales team, spam, or meeting ICP exclusion criteria.
- **Agent action:** "Thanks for reaching out — based on what you've described, this service may not be the right fit right now."
- **Conversion path:** Polite close. No conversion action.

**Knowledge gap (any category)**
- **Situation:** Visitor asks something outside agent's KB — at any point.
- **Agent action:** Form offered. Agent continues. Calendly still presented at end if qualified.
- **Conversion path:** Form → Botpress Table. Calendly offered separately.

**Visitor declines Calendly**
- **Situation:** Hot or Warm lead prefers not to book immediately.
- **Agent action:** "No problem — I can have someone reach out instead. Can I take your details?"
- **Conversion path:** Contact form as fallback → Botpress Table.

### Nurture Flow — Option B

Same nurture sequence as Option A. The difference is that the agent already knows this is a Nurture lead from its CHAMP scoring — so it frames the check-in and re-qualification with more intent to upgrade the lead.

**N1 — Share relevant resources**
- **Example:** "Let me share a couple of examples of what we've built — these should give you a clearer sense of what's possible." [share case studies]
- **Outcome:** Visitor sees concrete examples. Agent notes their reaction.

**N2 — Check in**
- **Example:** "Did any of those feel close to what you're thinking about?"
- **Outcome:** Keeps conversation going. Agent uses response to refine understanding of Challenges.

**N3 — Re-qualification** *(CHAMP revisit)*
- **Example:** "What would need to change on your side for this to become something you'd want to move forward on?"
- **Outcome:** Surfaces blockers. If CHAMP signals clarified positively, agent can upgrade to Warm or Hot.

**N4 (upgrade path)** — If CHAMP signals improve after N3
- **Agent action:** Re-evaluates CHAMP. If Challenges + any 1 signal confirmed → Warm. If all 4 confirmed → Hot.
- **Outcome:** Upgraded to Warm or Hot. Calendly presented.

**N4 (no upgrade)** — Soft Calendly nudge if no upgrade
- **Example:** "Even if it's just exploratory, a 20-minute call might help clarify what's realistic for you — no commitment needed. Want the link?"
- **Outcome:** If accepted → Calendly (tagged Nurture). If declined → warm close.

**N5 — Warm close** *(if Calendly declined)*
- **Example:** "Totally understood — come back when the timing is better. I'll make a note so our team has context if you do reach out."
- **Outcome:** Conversation ends positively.

### Pros & Cons — Option B (CHAMP)

**Pros:**
- CHAMP leads with Challenges — higher engagement and more honest qualification than budget-first approaches
- Calendly slots protected — only Hot and Warm leads can book, no time wasted on poor fits
- Hot and Warm differentiated — sales team prepares appropriately for each call
- Nurture is a live upgrade path, not a dead end — good leads aren't lost due to incomplete early signals
- DQ handled automatically — no sales time spent on clearly wrong-fit visitors

**Cons:**
- More complex to build in Botpress — scoring variables, conditional transitions, category-aware prompts required
- Risk of mis-scoring — agent may Nurture a good lead who gave incomplete early answers. Requires batch testing and calibration.
- DQ criteria must be explicitly defined before building — out-of-scope industries/profiles need to be documented.
