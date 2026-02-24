# Handoff Rules

> Post-scoring routing logic for the Sales AI Agent. Covers every path a visitor can take after CHAMP evaluation — from Calendly booking to polite close.

---

## Overview

This document is the single reference for what happens **after** the agent completes CHAMP scoring (Step 10). It consolidates routing logic that was previously spread across [Qualification framework v2](./Qualification%20framework%20v2.md), [Decision tree v2](./option_b_decision_tree_v2.md), and [Data points schema v2](./Data%20points%20schema%20(JSON%20formatted)%20v2.md).

**Audience:** Botpress developer building the post-scoring conversation nodes.

**Relationship to other documents:**
- **Qualification framework v2** — Defines CHAMP scoring logic, discovery steps 1–9, nurture flow, and knowledge gap escalation. This document picks up where scoring ends.
- **Decision tree v2** — Visual flow diagrams for the full conversation. This document details the routing branches shown after Step 10.
- **Data points schema v2** — Variable definitions and Botpress Table schema. This document references those variables and introduces new ones where needed.
- **PRD Sales AI Agent** — User stories and integration architecture. This document implements US-5 (meeting booking), US-6 (returning visitors), and US-8/US-9 (lead delivery + pre-call context).

---

## Handoff Summary

### By Lead Category

| Category    | Trigger Criteria                                        | Primary Path                   | Fallback                                | Data Destination                     |
|-------------|---------------------------------------------------------|--------------------------------|-----------------------------------------|--------------------------------------|
| **Hot**     | All 4 CHAMP signals positive                            | Calendly (tag: Hot)            | Contact form; availability fallback     | Botpress Table → Calendly → HubSpot |
| **Warm**    | CH positive, 1–2 of A/M/P missing (≥1 confirmed)       | Calendly (tag: Warm)           | Contact form; availability fallback     | Botpress Table → Calendly → HubSpot |
| **Nurture** | CH weak/vague, or M/P not established                   | Resources → re-qualify → nudge | Warm close (N5)                         | Botpress Table                       |
| **DQ**      | ICP exclusion, no need, wrong scope, spam, budget <€5k  | Polite close                   | —                                       | Botpress Table (tag: DQ)             |

> Agent language for each category is provided in the dedicated section below.

### By Trigger Scenario

| Scenario                                     | When It Fires           | Routing                     |
|----------------------------------------------|-------------------------|-----------------------------|
| CHAMP = 4/4 positive                         | Step 10 scoring         | → Hot lead handoff          |
| CHAMP = CH + 1–2 of A/M/P                   | Step 10 scoring         | → Warm lead handoff         |
| CHAMP = CH weak/vague                        | Step 10 scoring         | → Nurture flow              |
| Budget < €5,000                              | Step 9 or Step 10       | → DQ path                   |
| ICP exclusion detected                       | Step 3                  | → DQ (immediate)            |
| No need / wrong scope / no sales team / spam | Step 10 scoring         | → DQ path                   |
| Visitor declines Calendly (Hot/Warm)         | After Calendly offer    | → Contact form fallback     |
| Calendly has no available slots              | After Calendly offer    | → Availability fallback     |
| Knowledge gap (any category, any point)      | Mid-conversation        | → Knowledge gap handoff     |
| Nurture signals improve after N3             | N3 re-qualification     | → Upgrade to Warm or Hot    |
| Visitor returns to chat                      | Session start           | → Returning visitor routing |

---

## Hot Lead Handoff

**Trigger:** All 4 CHAMP signals evaluated as `positive` — Challenges clear, Authority confirmed, Money ≥ €5,000, Prioritization realistic.

**Sequence:**

1. **Qualification summary** — Agent frames the result naturally, never announcing the score.
   > "This sounds like a great fit. I'd love to get you on a call with our team — here's a link to book a time."

2. **Calendly link** — Present the Calendly booking link. Tag the booking as `Hot` in Botpress Table.

3. **Calendly availability fallback** — If Calendly returns no available slots within the next 5 business days, the agent acknowledges and offers the contact form instead:
   > "It looks like the next few days are fully booked. Let me take your details so our team can reach out directly and find a time that works."

   Set `calendly_fallback_used = true`. Route to contact form.

4. **Post-booking confirmation** — After the visitor books, the agent confirms and sets expectations:
   > "You're all set — [sales team member] will be in touch before your call. In the meantime, I'll share a quick summary of what we discussed so they can hit the ground running."

   Set `conversion_action = "meeting_booked"`. Trigger pre-call brief delivery. Set `conversation_stage = "completed"`.

5. **Pre-call context brief** — Delivered to the sales team (see [Pre-call context brief](#pre-call-context-brief) for full spec). The brief includes all discovery data, CHAMP signals, and conversation highlights.

**If visitor declines Calendly:**
> "No problem — I can have someone reach out instead. Can I take your details?"

Present contact form. Set `conversion_action = "form_submitted"`.

**Data written:**

| Variable                  | Value                                    |
|---------------------------|------------------------------------------|
| `lead_score`              | `"Hot"`                                  |
| `conversation_stage`      | `"handoff_hot"` → `"completed"`          |
| `conversion_action`       | `"meeting_booked"` or `"form_submitted"` |
| `pre_call_brief_sent`     | `true` (after brief delivery)            |
| `calendly_fallback_used`  | `true` if no slots available             |

---

## Warm Lead Handoff

**Trigger:** CH = positive + 1–2 of A/M/P missing, but at least 1 of the 3 confirmed.

The Warm handoff follows the same structure as Hot with these key differences:

1. **Qualification summary** — Language reflects potential rather than certainty:
   > "There's definitely something to explore here. Let me get you connected with our team — here's a link to book a call."

2. **Calendly link** — Tag as `Warm` in Botpress Table. Sales team uses this tag to prepare for missing signals (e.g., budget objection handling, authority clarification).

3. **Calendly availability fallback** — Same as Hot. If no slots, offer contact form. Set `calendly_fallback_used = true`.

4. **Post-booking confirmation** — Same message and flow as Hot.

5. **Pre-call context brief** — Same delivery mechanism as Hot. The brief explicitly flags which CHAMP signals are missing so the sales team knows what to probe on the call.

**If visitor declines Calendly:**
> "No problem — I can have someone reach out instead. Can I take your details?"

Present contact form. Set `conversion_action = "form_submitted"`.

**Data written:**

| Variable                  | Value                                    |
|---------------------------|------------------------------------------|
| `lead_score`              | `"Warm"`                                 |
| `conversation_stage`      | `"handoff_warm"` → `"completed"`         |
| `conversion_action`       | `"meeting_booked"` or `"form_submitted"` |
| `pre_call_brief_sent`     | `true` (after brief delivery)            |
| `calendly_fallback_used`  | `true` if no slots available             |

---

## Nurture Lead Handoff

**Trigger:** Challenges weak/vague, or Money/Prioritization not established. The visitor is exploring but doesn't have a clear, actionable need yet.

The Nurture flow is a multi-step sequence designed to upgrade the lead rather than close the conversation.

### Flow

**N1 — Share resources**
> "Let me share a couple of examples of what we've built — these should give you a clearer sense of what's possible." [share case studies]

Agent notes visitor's reaction. Set `nurture_stage = "N1_resources_shared"`. Set `conversion_action = "resources_sent"`. Populate `resources_shared` with the list of links sent.

**N2 — Check in**
> "Did any of those feel close to what you're thinking about?"

Agent refines understanding of Challenges based on response. Set `nurture_stage = "N2_checked_in"`.

**N3 — Re-qualification (CHAMP revisit)**
> "What would need to change on your side for this to become something you'd want to move forward on?"

Surfaces blockers. Agent re-evaluates CHAMP signals. Set `nurture_stage = "N3_requalified"`.

**N4 (upgrade path)** — If CHAMP signals improve after N3:
- CH + 1 signal confirmed → upgrade to `Warm`. Set `nurture_upgraded_to = "Warm"`. Set `nurture_stage = "N4_upgraded"`.
- All 4 confirmed → upgrade to `Hot`. Set `nurture_upgraded_to = "Hot"`. Set `nurture_stage = "N4_upgraded"`.
- The lead then follows the full **Warm or Hot lead handoff flow** (Calendly → post-booking confirmation → pre-call brief).

**N4 (no upgrade)** — Soft Calendly nudge:
> "Even if it's just exploratory, a 20-minute call might help clarify what's realistic for you — no commitment needed. Want the link?"

If accepted → Calendly (tag: Nurture). Set `nurture_stage = "N4_nudged"`. The booking is exploratory — no pre-call brief is sent, but the post-booking confirmation message is still delivered.

**N5 — Warm close** (if Calendly declined):
> "Totally understood — come back when the timing is better. I'll make a note so our team has context if you do reach out."

Set `nurture_stage = "N5_warm_closed"`. Conversation ends positively.

### Returning Nurture Visitor Re-entry

When a previously Nurture-scored visitor returns, the agent does **not** restart from scratch. Instead:

1. Greet by name (if known). Reference the previous conversation.
2. Skip N1 (resources already shared). Start from N2 — check in on whether anything has changed.
3. Re-evaluate CHAMP signals with fresh information.
4. If signals improve → upgrade to Warm or Hot and present Calendly.
5. If signals haven't changed → offer the soft Calendly nudge (N4) or warm close (N5).

See [Returning visitor routing](#returning-visitor-routing) for the full routing table.

**Data written:**

| Variable              | Value                                                             |
|-----------------------|-------------------------------------------------------------------|
| `lead_score`          | `"Nurture"` (may change to `"Warm"` or `"Hot"` on upgrade)       |
| `conversation_stage`  | `"nurture"` → `"completed"`                                      |
| `nurture_stage`       | Tracks N1–N5 progression                                         |
| `nurture_upgraded_to` | `"Warm"` or `"Hot"` if upgraded, `null` otherwise                |
| `resources_shared`    | Array of case study / resource links sent at N1                   |
| `conversion_action`   | `"resources_sent"`, `"meeting_booked"` (if upgraded), or `"none"` |

---

## Disqualification Path

**Trigger:** Any of the following:
- **ICP exclusion** — Adult/18+ content industry or russia-based company (detected at Step 3 — immediate close, no further discovery)
- **Insufficient budget** — Budget explicitly stated as less than €5,000 (detected at Step 9)
- **No relevant need** — Visitor has no sales challenge (Step 10)
- **Wrong scope** — Visitor is looking for something unrelated to an inbound Sales AI Agent (Step 10)
- **No sales team** — No one to augment; the company has no sales function (Step 10)
- **Spam / irrelevant** — Detected at any point

**Agent language:**
> "Thanks for reaching out — based on what you've described, this service may not be the right fit right now."

For ICP exclusion (detected at Step 3):
> "Thanks for reaching out — this isn't something we're able to help with."

**Action:** Polite close. No Calendly. No contact form. No resources. (Exception: returning DQ visitors may receive updated resources — see [Returning visitor routing](#returning-visitor-routing).)

**Data written:**

| Variable             | Value                                                                                    |
|----------------------|------------------------------------------------------------------------------------------|
| `lead_score`         | `"DQ"`                                                                                   |
| `lead_score_reason`  | Specific reason: "ICP exclusion: Adult/18+", "Budget below €5,000", "No sales challenge" |
| `conversation_stage` | `"dq_closed"`                                                                            |
| `conversion_action`  | `"none"`                                                                                 |
| `icp_exclusion_flag` | `true` if ICP exclusion                                                                  |

---

## Knowledge Gap Handoff

**Trigger:** The visitor asks a question the agent cannot answer — at any point in the conversation, regardless of lead category.

**Sequence:**

1. **Acknowledge the gap honestly.** The agent does NOT invent an answer.
2. **Offer an alternative.** Suggest a related topic the agent can help with, or reframe the question.
3. **Provide a contact email** (not a form). The agent gives the visitor a direct email address for human follow-up.
4. **Continue the discovery flow.** Qualification does not stop.

**Agent language:**
> "That's a great question — unfortunately, I don't have the details to answer that one. I can help you with [related alternative], or if you'd prefer a direct answer, you can reach our team at [email address]. In the meantime, let's keep going — I'd love to learn more about your project."

**Why email, not a form:** The contact form collects structured lead data (name, email, company) and is designed for Calendly-decline scenarios. Knowledge gap escalation is about routing a specific question to a human — an email address is faster, lower-friction, and doesn't interrupt the conversation flow with a form. The visitor already has the agent's attention; the email is a parallel channel, not a replacement.

**Data written:**

| Variable                   | Value                                                |
|----------------------------|------------------------------------------------------|
| `knowledge_gap_triggered`  | `true`                                               |
| `knowledge_gap_question`   | The specific question the agent could not answer      |
| `contact_form_question`    | Same question (for Botpress Table / sales follow-up)  |

**Important:** Knowledge gap does not change the lead score. After the gap is addressed, qualification continues. Calendly is still presented at the end if the visitor qualifies.

---

## Fallback Mechanisms

A consolidated view of every fallback in the system, when it fires, and why that specific mechanism was chosen.

| Scenario                          | Primary Path       | Fallback + Mechanism                                     | Why                                                   |
|-----------------------------------|--------------------|----------------------------------------------------------|-------------------------------------------------------|
| Hot/Warm declines Calendly        | Calendly booking   | Contact form (name, email, company, message)             | Captures lead data for manual outreach + HubSpot sync |
| Hot/Warm — no Calendly slots      | Calendly booking   | Contact form + `calendly_fallback_used = true`           | Prevents dead-end on scheduling availability          |
| Nurture declines soft nudge       | Soft Calendly (N4) | Warm close (N5), context saved                           | Low-pressure exit preserves re-engagement             |
| Knowledge gap                     | Agent continues    | Contact email for human follow-up                        | Low-friction, doesn't interrupt conversation          |
| Visitor unresponsive (2+ min)     | Continue chat      | Offer contact email                                      | Graceful timeout, doesn't lose the lead               |

---

## Pre-call Context Brief

The pre-call brief gives the sales team all discovery context before their call with a Hot or Warm lead. This implements US-9 from the PRD: "As a sales manager, I want to see what the prospect discussed with the agent before my call."

### Specification

| Attribute            | Detail                                                                                                            |
|----------------------|-------------------------------------------------------------------------------------------------------------------|
| **Trigger**          | `conversion_action = "meeting_booked"` for Hot/Warm (incl. upgraded Nurture). Not sent for N4 soft-nudge bookings |
| **Delivery channel** | Slack notification to sales channel (email fallback)                                                              |
| **Timing**           | Immediately after Calendly booking confirmed                                                                      |
| **Format**           | Structured message with variable mappings (see template below)                                                    |

### Content Template

```
New {lead_score} Lead — Pre-Call Brief

Visitor
   Name:    {visitor_name}
   Company: {visitor_company}
   Role:    {visitor_role}
   Industry: {visitor_industry}

CHAMP Signals
   Challenges:     {ch_challenges} — {use_case}; {pain_points}
   Authority:      {a_authority} — {decision_authority}; stakeholders: {other_stakeholders}
   Money:          {m_money} — {budget_indication}
   Prioritization: {p_prioritization} — {timeline}; trigger: {trigger_event}

Additional Context
   Leads/month:     {leads_per_month}
   Expected volume: {expected_volume}
   Current CRM:     {current_crm}
   Website:         {website_platform}
   Chat tools:      {current_chat_tools}
   Integrations:    {integrations_needed}

Missing Signals (Warm leads only)
   {List any CHAMP signal that is "negative" or "unclear" — these are topics for the call}

Conversation Summary
   {conversation_summary}

Knowledge Gap (if any)
   Question: {knowledge_gap_question}
   (Visitor was given [email] for follow-up)

Meeting: {Calendly booking date/time}
```

### Variable Mappings

All variables in the template map to fields defined in [Data points schema v2](./Data%20points%20schema%20(JSON%20formatted)%20v2.md). Most map directly by name. The CHAMP signal variables (`ch_challenges`, `a_authority`, `m_money`, `p_prioritization`) are nested under the `champ_signals` object in the Conversation Variables schema but stored as flat columns in the Leads Table schema — use the Leads Table as the source for the brief, or access them as `champ_signals.ch_challenges` etc. from Conversation Variables.

No new variables are needed for the brief content — only the `pre_call_brief_sent` flag is new (see [Data schema impact](#data-schema-impact)).

---

## Returning Visitor Routing

When `is_returning_visitor = true`, the agent checks `previous_lead_score` and routes accordingly. The agent greets the visitor by name (if known) and references the previous conversation — it does not re-ask questions already answered.

| Previous Score | Routing                                                                                | Agent Approach                                   |
|----------------|----------------------------------------------------------------------------------------|--------------------------------------------------|
| **Hot**        | Check if meeting booked. Yes → "Call coming up — anything to add?" No → re-offer.     | Maintain momentum. Don't re-qualify.             |
| **Warm**       | Check missing signals. Improved → upgrade to Hot. Same → re-offer Calendly as Warm.   | "Last time, [signal] was open. Changed?"         |
| **Nurture**    | Skip N1. Start from N2 check-in. Re-evaluate CHAMP. Upgrade if improved.              | "Welcome back! Did those examples resonate?"     |
| **DQ**         | Welcome back. No re-qualification. May share updated resources if situation changed.   | "Welcome back — let me know if I can help."      |
| **Unscored**   | Treat as new visitor. Full discovery from Step 1.                                      | Standard greeting.                               |

**Data written on return:**

| Variable               | Value                            |
|------------------------|----------------------------------|
| `is_returning_visitor` | `true`                           |
| `previous_lead_score`  | Value from Botpress Table lookup |

The agent updates `lead_score` if the visitor's category changes during the return conversation.

---

## Post-booking Confirmation

**Trigger:** Visitor successfully books via Calendly (`conversion_action = "meeting_booked"`). Fires for all booking scenarios: Hot, Warm, upgraded Nurture, and non-upgraded Nurture (N4 soft nudge).

**Message:**
> "You're all set — [sales team member] will be in touch before your call. In the meantime, I'll share a quick summary of what we discussed so they can hit the ground running."

**Purpose:** Reduces no-show risk by confirming the booking, sets expectations for prep, and transitions the conversation to a completed state.

**Stage transition:** `conversation_stage` moves to `"completed"` from whichever current stage applies (`"handoff_hot"`, `"handoff_warm"`, or `"nurture"`).

**What happens next:**
1. For Hot and Warm leads (including upgraded Nurture): pre-call context brief is generated and sent to the sales team. `pre_call_brief_sent` is set to `true`.
2. For non-upgraded Nurture leads (N4 soft nudge booking): no pre-call brief — the booking is exploratory. `pre_call_brief_sent` remains `false`.
3. Conversation is marked as completed in Botpress Table.

---

## Data Schema Impact

The following new variables are needed to support the handoff improvements in this document. These extend the schemas defined in [Data points schema v2](./Data%20points%20schema%20(JSON%20formatted)%20v2.md). No existing enums or variable types are modified.

### New Conversation Variables

```json
{
  "calendly_fallback_used": {
    "type": "boolean",
    "description": "NEW — True if the Calendly link had no available slots and the agent fell back to the contact form. Tracks scheduling availability issues.",
    "default": false
  },

  "pre_call_brief_sent": {
    "type": "boolean",
    "description": "NEW — True after the pre-call context brief has been delivered to the sales team via Slack (or email fallback). Confirms the sales rep has discovery context.",
    "default": false
  }
}
```

### Botpress Table Schema Updates

The same two changes apply to the Leads Table:

| Column                  | Type    | Change  | Notes                                         |
|-------------------------|---------|---------|-----------------------------------------------|
| `calendly_fallback_used`| boolean | **New** | Default: `false`. Tracks availability issues. |
| `pre_call_brief_sent`   | boolean | **New** | Default: `false`. Confirms brief delivery.    |

### Impact on Existing Variables

- `lead_score` enum: no changes needed — existing values (`unscored`, `Hot`, `Warm`, `Nurture`, `DQ`) cover all paths.
- `conversation_stage` enum: no changes needed — existing stages (`handoff_hot`, `handoff_warm`, `dq_closed`, etc.) cover all new paths.

---

## Implementation Checklist

Ordered by dependency. Complete each item before moving to the next group.

### Group 1 — Schema & Data Layer

- [ ] Add `calendly_fallback_used` (boolean, default `false`) to Conversation Variables and Leads Table
- [ ] Add `pre_call_brief_sent` (boolean, default `false`) to Conversation Variables and Leads Table

### Group 2 — Scoring & Routing Logic

- [ ] Implement Hot lead handoff node: Calendly (tag Hot) → post-booking confirmation → pre-call brief trigger
- [ ] Implement Warm lead handoff node: Calendly (tag Warm) → post-booking confirmation → pre-call brief trigger
- [ ] Implement DQ handoff node: polite close, no conversion actions

### Group 3 — Fallback Mechanisms

- [ ] Implement Calendly availability check — if no slots in next 5 business days, trigger contact form fallback and set `calendly_fallback_used = true`
- [ ] Implement Calendly-decline fallback — contact form presented when Hot/Warm visitor declines booking
- [ ] Verify contact form captures: name, email, company, optional message (per ContactForm_KnowledgeGap schema)

### Group 4 — Knowledge Gap & Nurture

- [ ] Implement knowledge gap handoff: offer alternative → provide contact email → continue flow
- [ ] Verify knowledge gap variables are written: `knowledge_gap_triggered`, `knowledge_gap_question`, `contact_form_question`
- [ ] Implement full Nurture flow: N1 → N2 → N3 → N4 (upgrade or nudge) → N5 (warm close)
- [ ] Implement Nurture upgrade logic: if CHAMP signals improve at N3, re-score and route to Warm or Hot

### Group 5 — Advanced Features

- [ ] Implement post-booking confirmation message (fires after Calendly booking for Hot and Warm)
- [ ] Build pre-call context brief template with variable mappings (see [Pre-call context brief](#pre-call-context-brief))
- [ ] Set up Slack integration for pre-call brief delivery (with email fallback)
- [ ] Set `pre_call_brief_sent = true` after successful delivery
### Group 6 — Returning Visitors

- [ ] Implement returning visitor detection (`is_returning_visitor`, `previous_lead_score` lookup)
- [ ] Build routing logic for each `previous_lead_score` value (Hot, Warm, Nurture, DQ, unscored)
- [ ] Implement Nurture re-entry: skip N1, start from N2 check-in

### Group 7 — Testing & Verification

- [ ] Test all 4 lead categories end-to-end: Hot, Warm, Nurture, DQ
- [ ] Test Calendly availability fallback (simulate no-slots scenario)
- [ ] Test Calendly decline fallback (visitor says no to booking)
- [ ] Test knowledge gap escalation mid-conversation (verify qualification continues)
- [ ] Test Nurture upgrade path (N3 → signals improve → Warm/Hot)
- [ ] Test returning visitor routing for all `previous_lead_score` values
- [ ] Test post-booking confirmation and pre-call brief delivery
- [ ] Verify all new variables are written correctly to Botpress Table
- [ ] Cross-check Leads Table data against HubSpot sync (Calendly → HubSpot)
