# Flows and Nodes Structure

> Botpress implementation blueprint. Defines every flow, node, and transition needed for the Sales AI Agent.

---

## Architecture Overview

| Component              | Count | Purpose                                                    |
|------------------------|-------|------------------------------------------------------------|
| **Global Instructions** | 1     | Persona, guardrails, question/objection/edge case handling |
| **Main Flow**          | 1     | Discovery + scoring + Hot/Warm handoff + DQ close          |
| **Nurture Flow**       | 1     | N1-N5 with early/standard entry + upgrade path             |
| **Returning Visitor Flow** | 1 | Lookup + routing + re-engagement conversation              |
| **Autonomous Nodes**   | 5     | Conversational nodes that need AI flexibility              |
| **Standard Nodes**     | ~20   | Deterministic routing, actions, messages                   |

---

## Global Instructions

Always active. Applied to every autonomous node in every flow.

### What to include

**Persona and tone**
- Role: AI sales assistant for pre-sales consultation
- Tone: professional, friendly, consultative, not pushy
- Positioning: sales augmentation, NOT a replacement for the sales team
- Never reveal internal scores, logic, or variable names to the visitor

**Guardrails -- must NOT do (from PRD 6.2)**
- Provide exact pricing without context
- Guarantee outcomes or results
- Disclose confidential client information
- Reveal internal infrastructure or technical details
- Make up information not in the KB
- Discuss competitors by name
- Engage with off-topic or inappropriate requests

**Guardrails -- must NOT promise (from PRD 6.2)**
- Fixed timelines without discovery
- ROI or revenue growth guarantees
- Full replacement of the sales team
- SLA commitments without agreement
- Specific deliverables or scope without a call
- Discounts or special pricing

**Question handling (from UC2)**
- Check KB first
- If fully answerable: answer from KB
- If partially answerable: answer what you can, note the gap, suggest a call for more detail
- If not answerable: acknowledge honestly, suggest an alternative topic, provide contact email for human follow-up
- Log the gap in `knowledge_gap_triggered` and `knowledge_gap_question`
- Always resume the discovery flow after handling the question

**Objection handling (from UC3)**
- Pricing objection: share pricing framework from KB, suggest phased approach. DO NOT provide exact pricing or promise discounts
- Timing objection: note the concern (re-check P signal), suggest phased approach. DO NOT promise fixed timelines or SLA commitments
- Competitor objection: redirect to the company's strengths, case studies, client results. DO NOT discuss competitors by name
- Scope objection: clarify what they are looking for, refine CH signals. If scope doesn't match -> route to DQ. DO NOT promise specific deliverables
- Trust objection: provide case studies and social proof from KB. DO NOT guarantee outcomes, ROI, or promise sales team replacement
- Unidentified objection: log the objection text, provide contact email for follow-up

**Edge case handling (from UC5 A+B+C)**
- Spam/profanity/abuse: redirect politely once. If behavior doesn't change -> polite close, set `lead_score = "DQ"`, `lead_score_reason = "spam"`
- Off-topic/gibberish: redirect with empathy. Two attempts. If no change -> polite close, provide contact email
- Skip to human / request for a human: if `qualification_complete = true` -> present Calendly. If false but name + email + company captured -> present contact form. If false and no contact data -> ask for name, email, company, then present contact form. This rule applies at any point in any autonomous node.

---

## Flow 1: Main Flow

Entry point for all conversations. Checks for returning visitors first, then handles discovery (Steps 1-9), CHAMP scoring (Step 10), Hot/Warm handoff, and DQ close.

### Node Map

```
[M0] Returning Visitor Check
  |         |
  |       Returning
  |         |
  |      -> Returning Visitor Flow
  |
[M1] Greeting
  |
[M2] Introduction
  |
[M3] Rapport + Challenges (autonomous)
  |         |
  |       ICP fail -> [M11] DQ Close
  |
[M4] CH Signal Check
  |         |
  |       CH = negative/unclear
  |         |
  |      -> Nurture Flow (entry: "early")
  |
[M5] Details + Budget (autonomous)
  |         |
  |       Budget < 5k -> [M11] DQ Close
  |
[M6] CHAMP Scoring Engine + Router
  |      |      |      |
  Hot   Warm  Nurture  DQ
  |      |      |      |
  |      |      |   [M11] DQ Close
  |      |      |
  |      |   -> Nurture Flow (entry: "standard")
  |      |
[M7] Calendly Offer
  |
[M8] Calendly Response Router
  |         |           |
 Booked   Declined   No slots
  |         |           |
[M12]    [M9]       [M9]
  |
[M9] Contact Form
  |
[M13] Completed
  |
[M12] Post-Booking Confirmation
  |
[M10] Pre-Call Brief
  |
[M13] Completed
```

### Node Details

---

#### M0 -- Returning Visitor Check

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard (Execute Code) |
| **Purpose**    | First node in the entire agent. Checks if this visitor has been here before. |
| **Logic**      | Query Leads Table by `visitor_id`. If match found: set `is_returning_visitor = true`, load `previous_lead_score` -> redirect to Returning Visitor Flow. If no match: set `is_returning_visitor = false` -> M1 |
| **Transition** | Returning visitor -> Returning Visitor Flow R3. New visitor -> M1 |

---

#### M1 -- Greeting

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Welcome visitor. Set context. |
| **Message**    | "Hi -- I'm here to help you figure out if an inbound Sales AI Agent could work for your business. Mind if I ask a few questions?" |
| **Variables**  | Set `conversation_stage = "greeting"` |
| **Transition** | -> M2 |

---

#### M2 -- Introduction

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Brief framing of the Sales AI Agent service. |
| **Message**    | What an inbound Sales AI Agent is, what it does (qualify leads, book meetings, handle FAQs), typical outcomes. Sets expectations. |
| **Variables**  | Set `conversation_stage = "introduction"` |
| **Transition** | -> M3 |

---

#### M3 -- Rapport + Challenges (Steps 3-5)

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Autonomous |
| **Purpose**    | Understand who the visitor is, what they need, and what problem is driving the need. |
| **Node instructions** | Ask about the visitor's company, their role, industry, and team size. Then explore what they want the Sales AI Agent to do and what pain points they're experiencing. Keep it conversational -- don't interrogate. If the visitor provides multiple answers at once, acknowledge and move on. **ICP check:** If you detect that the company is in the Adult/18+ content industry or is russia-based, set `icp_exclusion_flag = true` and transition to DQ Close immediately -- do not continue discovery. |
| **Variables read** | None |
| **Variables set** | `visitor_company`, `visitor_role`, `visitor_industry`, `visitor_location`, `visitor_team_size`, `use_case`, `pain_points`, `icp_exclusion_flag`, `champ_signals.ch_challenges`, `conversation_stage` (update through `"discovery_company"` -> `"discovery_use_case"` -> `"discovery_pain_points"`) |
| **Exit conditions** | 1. `icp_exclusion_flag = true` -> M11 (DQ Close). 2. Company info + use case + pain points collected -> M4 |
| **Timeout** | 2 min inactivity -> "Still there? No rush -- let me know when you're ready to continue." -> 2 more min -> soft close with contact email |

---

#### M4 -- CH Signal Check

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Early Nurture checkpoint. If challenges are weak/vague, don't continue to Steps 6-9 -- route to Nurture. |
| **Logic**      | If `champ_signals.ch_challenges = "negative"` or `"unclear"` -> Nurture Flow (entry: "early"). If `"positive"` -> M5 |
| **Variables**  | Read `champ_signals.ch_challenges` |
| **Transition** | Negative/unclear -> Nurture Flow. Positive -> M5 |

---

#### M5 -- Details + Budget (Steps 6-9)

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Autonomous |
| **Purpose**    | Collect volume, tech stack, timeline, budget, and decision authority. |
| **Node instructions** | Ask about their current lead volume and expected conversation volume after launch. Then ask about their sales tools (CRM, website platform, chat tools, integrations needed). Then explore timeline -- when do they want the agent live, any trigger events? Finally, ask about budget range (reference floor: EUR 5,000) and who else is involved in the decision. Start with the technical questions and ease into budget last. **Budget DQ check:** If the visitor explicitly states their budget is below EUR 5,000, set `champ_signals.m_money = "negative"` and transition to DQ Close immediately -- do not continue discovery. Note: "not sure" or declining to answer is NOT a DQ -- set `m_money = "unclear"` and continue. |
| **Variables read** | `visitor_company`, `use_case` (for context) |
| **Variables set** | `leads_per_month`, `expected_volume`, `current_crm`, `website_platform`, `current_chat_tools`, `integrations_needed`, `timeline`, `trigger_event`, `budget_indication`, `decision_authority`, `other_stakeholders`, `champ_signals.p_prioritization`, `champ_signals.m_money`, `champ_signals.a_authority`, `conversation_stage` (update through `"discovery_volume"` -> `"discovery_current_stack"` -> `"discovery_timeline"` -> `"discovery_budget"`) |
| **Exit conditions** | 1. Budget explicitly < EUR 5,000 -> M11 (DQ Close). 2. All data collected (or visitor declines to answer remaining questions) -> M6 |
| **Timeout** | Same as M3 |

---

#### M6 -- CHAMP Scoring Engine + Router

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard (Execute Code) |
| **Purpose**    | Evaluate all 4 CHAMP signals, assign lead_score, and route to the correct handoff path. |
| **Logic**      | Count positive signals in `champ_signals` -> set `champ_positive_count`. Scoring rules: All 4 positive -> `lead_score = "Hot"`. CH positive + 1-2 of A/M/P positive (at least 1) -> `lead_score = "Warm"`. CH negative/unclear, OR CH positive but 0 of A/M/P positive -> `lead_score = "Nurture"`. No relevant need / wrong scope / no sales team / spam -> `lead_score = "DQ"`. Then route: Hot -> M7. Warm -> M7. Nurture -> Nurture Flow (entry: "standard"). DQ -> M11 |
| **Variables set** | `champ_positive_count`, `lead_score`, `lead_score_reason`, `qualification_complete = true`, `conversation_stage = "champ_evaluation"` |
| **Transition** | Hot/Warm -> M7. Nurture -> Nurture Flow. DQ -> M11 |

---

#### M7 -- Calendly Offer

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Present Calendly link with category-appropriate language. |
| **Message (Hot)**  | "This sounds like a great fit. I'd love to get you on a call with our team -- here's a link to book a time." + Calendly link |
| **Message (Warm)** | "There's definitely something to explore here. Let me get you connected with our team -- here's a link to book a call." + Calendly link |
| **Variables**  | Set `conversation_stage = "handoff_hot"` or `"handoff_warm"` based on `lead_score` |
| **Transition** | -> M8 |

---

#### M8 -- Calendly Response Router

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Route based on visitor's response to Calendly offer. |
| **Logic**      | Visitor books -> M12. Visitor declines -> M9. No available slots within 5 business days -> set `calendly_fallback_used = true` -> M9 |
| **Transition** | Booked -> M12. Declined -> M9. No slots -> M9 |

---

#### M9 -- Contact Form

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Capture lead data when Calendly is declined or unavailable. |
| **Message**    | "No problem -- I can have someone reach out instead. Can I take your details?" |
| **Form fields**| Name (required), Email (required), Company (required, pre-filled if known), Message (optional) |
| **Variables**  | Set `conversion_action = "form_submitted"`. Pre-fill from `visitor_name`, `visitor_company` if available |
| **Transition** | Form submitted -> M13. Visitor declines form -> offer contact email -> M13 |

---

#### M10 -- Pre-Call Brief

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard (Execute Code) |
| **Purpose**    | Send structured pre-call brief to sales team. |
| **Logic**      | Only fires for Hot and Warm leads (including upgraded Nurture). Not sent for Nurture N4 soft-nudge bookings. Build brief from template (see Handoff rules v2 for full template). Send via Slack (email fallback). |
| **Variables**  | Set `pre_call_brief_sent = true` |
| **Transition** | -> M13 |

---

#### M11 -- DQ Close

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Politely close the conversation for disqualified visitors. |
| **Message (ICP exclusion)** | "Thanks for reaching out -- this isn't something we're able to help with." |
| **Message (all other DQ)** | "Thanks for reaching out -- based on what you've described, this service may not be the right fit right now." |
| **Logic**      | Pick message based on `icp_exclusion_flag`. If `true` -> ICP message. Else -> standard DQ message |
| **Variables**  | Set `lead_score = "DQ"`, `lead_score_reason` (specific reason), `conversation_stage = "dq_closed"`, `conversion_action = "none"` |
| **Transition** | -> End |

---

#### M12 -- Post-Booking Confirmation

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Confirm booking, set expectations, transition to completed. |
| **Message**    | "You're all set -- [sales team member] will be in touch before your call. In the meantime, I'll share a quick summary of what we discussed so they can hit the ground running." |
| **Variables**  | Set `conversion_action = "meeting_booked"` |
| **Transition** | -> M10 |

---

#### M13 -- Completed

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | End the conversation. Write data to Botpress Table. |
| **Variables**  | Set `conversation_stage = "completed"`. Write all conversation variables to Leads Table |
| **Transition** | -> End |

---

## Flow 2: Nurture Flow

Separate flow called from Main Flow. Handles resource sharing, re-qualification, and upgrade path.

### Entry

Called with `nurture_entry` flag:
- `"early"` -- from M4 (CH weak/vague after Steps 4-5, no Steps 6-9 data)
- `"standard"` -- from M6 (after full discovery, CHAMP scored as Nurture)

### Node Map

```
[N1] Share Resources (autonomous)
  |
[N2] Check-in + Re-qualification (autonomous)
  |
[N3] Upgrade Check
  |         |
 Upgraded  Not upgraded
  |         |
  |      [N4] Soft Nudge
  |         |         |
  |       Accepted  Declined
  |         |         |
  |      [N5] Nurture  [N6] Warm Close
  |       Booking
  |
(route depends on nurture_entry + upgrade level)
  |
  --> Main Flow M5 (early, only CH improved)
  --> Main Flow M7 (upgraded to Warm/Hot)
```

### Node Details

---

#### N1 -- Share Resources

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Autonomous |
| **Purpose**    | Select and share relevant case studies and resources based on what the agent knows about the visitor. |
| **Node instructions** | Based on what you've learned about the visitor's industry, use case, and pain points, select the most relevant case studies and resources from the KB. Present them conversationally: "Let me share a couple of examples of what we've built -- these should give you a clearer sense of what's possible." If you have limited info about the visitor (early Nurture entry), pick the most broadly relevant examples. Note which resources you shared. |
| **Variables read** | `visitor_industry`, `use_case`, `pain_points`, `nurture_entry` (for context on how much is known) |
| **Variables set** | `nurture_stage = "N1_resources_shared"`, `conversion_action = "resources_sent"`, `resources_shared = [list of links shared]` |
| **Transition** | -> N2 |

---

#### N2 -- Check-in + Re-qualification (N2 + N3)

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Autonomous |
| **Purpose**    | Check if resources resonated, then probe for changed signals. |
| **Node instructions** | First, ask if any of the shared examples felt close to what the visitor is thinking about. Use their response to refine your understanding of their challenges. Then ask: "What would need to change on your side for this to become something you'd want to move forward on?" Listen for improved CHAMP signals -- clearer challenges, budget mentions, timeline urgency, decision-maker confirmation. Update the CHAMP signal values based on what you hear. |
| **Variables read** | `nurture_entry`, `resources_shared`, `champ_signals` (current values) |
| **Variables set** | `nurture_stage = "N3_requalified"`, updated `champ_signals` values |
| **Exit condition** | Re-qualification conversation complete -> N3 |
| **Timeout** | 2 min -> soft check -> 2 min -> warm close (N6) |

---

#### N3 -- Upgrade Check

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Determine if CHAMP signals improved enough to upgrade. |
| **Logic**      | Re-count positive signals. Then route based on `nurture_entry` flag: |

**If `nurture_entry = "early"`:**

| Signals after N2                          | Route                                                   |
|-------------------------------------------|---------------------------------------------------------|
| Only CH improved (CH positive, 0 of A/M/P)| -> Main Flow M5 (resume discovery at Steps 6-9)        |
| CH + 1-2 of A/M/P positive               | Set `nurture_upgraded_to = "Warm"` -> Main Flow M7     |
| All 4 positive                            | Set `nurture_upgraded_to = "Hot"` -> Main Flow M7      |
| No improvement                            | -> N4                                                   |

**If `nurture_entry = "standard"`:**

| Signals after N2                          | Route                                                   |
|-------------------------------------------|---------------------------------------------------------|
| CH + 1-2 of A/M/P positive (was 0 before) | Set `nurture_upgraded_to = "Warm"` -> Main Flow M7    |
| All 4 positive                            | Set `nurture_upgraded_to = "Hot"` -> Main Flow M7      |
| No improvement                            | -> N4                                                   |

| **Variables** | Set `nurture_stage = "N4_upgraded"` on upgrade. Update `lead_score` to match upgrade |

---

#### N4 -- Soft Calendly Nudge

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Low-pressure Calendly offer for non-upgraded Nurture leads. |
| **Message**    | "Even if it's just exploratory, a 20-minute call might help clarify what's realistic for you -- no commitment needed. Want the link?" |
| **Variables**  | Set `nurture_stage = "N4_nudged"` |
| **Transition** | Accepted -> N5. Declined -> N6 |

---

#### N5 -- Nurture Booking

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Handle Calendly booking for Nurture lead (exploratory call). |
| **Logic**      | Present Calendly link. Tag as Nurture. No pre-call brief (booking is exploratory). Post-booking confirmation message is still sent. |
| **Variables**  | Set `conversion_action = "meeting_booked"`, `conversation_stage = "completed"` |
| **Transition** | -> Main Flow M13 (Completed) |

---

#### N6 -- Warm Close

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | End conversation positively for Nurture leads who decline the nudge. |
| **Message**    | "Totally understood -- come back when the timing is better. I'll make a note so our team has context if you do reach out." |
| **Variables**  | Set `nurture_stage = "N5_warm_closed"`, `conversation_stage = "completed"` |
| **Transition** | -> Main Flow M13 (Completed) |

---

## Flow 3: Returning Visitor Flow

Entry point when M0 detects a returning visitor. Handles greeting, routing by previous score, and re-engagement.

### Node Map

```
[R1] Greet by Name
  |
[R2] Score Router
  |        |       |       |        |
 Hot     Warm   Nurture   DQ    Unscored
  |        |       |       |        |
[R3]     [R4]   [R5]    [R6]     [R7]
```

### Node Details

---

#### R1 -- Greet by Name

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Welcome returning visitor with recognition. |
| **Message**    | "Welcome back{, [visitor_name]}! Great to see you again." (Name included if known) |
| **Transition** | -> R2 |

---

#### R2 -- Score Router

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Route to the appropriate re-engagement path based on previous lead score. |
| **Logic**      | Switch on `previous_lead_score`: `"Hot"` -> R3. `"Warm"` -> R4. `"Nurture"` -> R5. `"DQ"` -> R6. `"unscored"` -> R7 |
| **Transition** | By score value |

---

#### R3 -- Hot Returning

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Handle returning Hot lead based on their previous conversion action. |
| **Logic**      | Check `conversion_action`: `"meeting_booked"` -> follow-up message: "Your call is coming up -- anything you'd like to add or ask before then?" No re-qualification. `"form_submitted"` -> re-offer Calendly. `"none"` -> re-offer Calendly |
| **Transition** | Follow-up -> End (or visitor asks questions -> handled by global instructions). Re-offer accepted -> Main Flow M12. Re-offer declined -> "No worries -- your details are already with our team, they'll follow up." -> End |

---

#### R4 -- Warm Returning

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Autonomous |
| **Purpose**    | Probe the specific missing CHAMP signals from the previous conversation. |
| **Node instructions** | Check which of A/M/P signals were missing or unclear in the previous conversation. Probe each one specifically: If M missing: "Last time, budget was still being figured out. Has anything changed?" If A missing: "You mentioned needing to check with your team. Any updates?" If P missing: "Has your timeline firmed up since we last spoke?" Update the CHAMP signal values based on responses. |
| **Variables read** | `champ_signals` (previous values), `visitor_company`, `use_case` |
| **Variables set** | Updated `champ_signals`, `lead_score` (if upgraded) |
| **Exit condition** | Probing complete -> Standard check: if all 4 positive -> update to Hot -> Main Flow M7 (Calendly as Hot). If still Warm -> Main Flow M7 (Calendly as Warm) |

---

#### R5 -- Nurture Returning

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Route to Nurture Flow based on previous nurture_stage. |
| **Logic**      | Check `nurture_stage`: `null` -> Nurture Flow N1 (start from beginning). `"N1_resources_shared"` or `"N4_nudged"` or `"N5_warm_closed"` -> Nurture Flow N2 (skip N1, resources already shared). `"N2_checked_in"` or `"N3_requalified"` -> Nurture Flow N2 (re-check). `"N4_upgraded"` -> check `qualification_complete`. If true -> Main Flow M7. If false -> Main Flow M5 |
| **Transition** | By nurture_stage value |

---

#### R6 -- DQ Returning

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Autonomous |
| **Purpose**    | Welcome back a previously DQ'd visitor. Handle carefully based on DQ reason. |
| **Node instructions** | Check the `lead_score_reason`. If ICP exclusion (Adult/18+ or russia-based): do NOT re-qualify. Welcome back politely. Answer questions if asked (handled by global instructions). Otherwise warm close. If CHAMP-based DQ (budget, no need, wrong scope): ask if anything has changed since last time. If visitor indicates changes, update CHAMP signals. |
| **Variables read** | `lead_score_reason`, `icp_exclusion_flag` |
| **Variables set** | Updated `champ_signals` if visitor indicates changes |
| **Exit condition** | ICP DQ -> End (or answer questions). CHAMP DQ + signals improved -> check `qualification_complete`. If true -> Main Flow M6 (re-score). If false -> check `conversation_stage` and resume Main Flow from where they left off. CHAMP DQ + no change -> warm close -> End |

---

#### R7 -- Unscored Returning

| Attribute      | Detail |
|----------------|--------|
| **Type**       | Standard |
| **Purpose**    | Handle returning visitor who was never scored (dropped off mid-conversation). |
| **Logic**      | Check `conversation_stage`. If `"discovery_company"` or further -> resume Main Flow from the appropriate node based on stage. If `"greeting"` or `"introduction"` -> reference previous conversation: "I remember we started chatting before. Want to pick up where we left off?" If yes -> Main Flow M1. If no -> answer questions (global instructions) or warm close |
| **Transition** | By conversation_stage value |

---

## Timeout Transitions (applied to all autonomous nodes)

| Trigger             | Action                                                                      |
|---------------------|-----------------------------------------------------------------------------|
| 2 min no response   | Send: "Still there? No rush -- let me know when you're ready to continue." |
| 2 more min          | Send: "I'll be here whenever you're ready. Feel free to come back anytime." -> End (soft close) |

Configure as a timeout transition on each autonomous node (M3, M5, N1, N2, R4, R6), pointing to a shared "inactivity check" standard node.

---

## Summary Table

| ID   | Name                        | Type       | Flow               |
|------|-----------------------------|------------|---------------------|
| M0   | Returning Visitor Check     | Standard   | Main                |
| M1   | Greeting                    | Standard   | Main                |
| M2   | Introduction                | Standard   | Main                |
| M3   | Rapport + Challenges        | Autonomous | Main                |
| M4   | CH Signal Check             | Standard   | Main                |
| M5   | Details + Budget            | Autonomous | Main                |
| M6   | CHAMP Scoring + Router      | Standard   | Main                |
| M7   | Calendly Offer              | Standard   | Main                |
| M8   | Calendly Response Router    | Standard   | Main                |
| M9   | Contact Form                | Standard   | Main                |
| M10  | Pre-Call Brief              | Standard   | Main                |
| M11  | DQ Close                    | Standard   | Main                |
| M12  | Post-Booking Confirmation   | Standard   | Main                |
| M13  | Completed                   | Standard   | Main                |
| N1   | Share Resources             | Autonomous | Nurture             |
| N2   | Check-in + Re-qualification | Autonomous | Nurture             |
| N3   | Upgrade Check               | Standard   | Nurture             |
| N4   | Soft Calendly Nudge         | Standard   | Nurture             |
| N5   | Nurture Booking             | Standard   | Nurture             |
| N6   | Warm Close                  | Standard   | Nurture             |
| R1   | Greet by Name               | Standard   | Returning Visitor   |
| R2   | Score Router                | Standard   | Returning Visitor   |
| R3   | Hot Returning               | Standard   | Returning Visitor   |
| R4   | Warm Returning              | Autonomous | Returning Visitor   |
| R5   | Nurture Returning           | Standard   | Returning Visitor   |
| R6   | DQ Returning                | Autonomous | Returning Visitor   |
| R7   | Unscored Returning          | Standard   | Returning Visitor   |

**Totals: 3 flows, 27 nodes (5 autonomous + 22 standard), 1 global instruction set.**
