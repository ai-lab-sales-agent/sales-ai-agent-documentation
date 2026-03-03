# Hot/Warm Handoff - Sub-Workflow

> Botpress Studio: Create workflow "Handoff"
> Mixed nodes (Autonomous + Standard).

> Created: March 3, 2026 | Updated: March 3, 2026

---

## Context

This workflow is entered when a visitor qualifies as Hot or Warm. Check `lead_score` to determine messaging tone.

- Hot: `ch_challenges` = "positive", `a_authority` = "positive", `m_money` = "positive", `p_prioritization` = "positive". Confident framing.
- Warm: `ch_challenges` = "positive" + at least 1 of `a_authority`/`m_money`/`p_prioritization` = "positive". Exploratory framing.

---

## Steps

### Step 0: Entry Guard (Standard Node)

1. Verify `lead_score` is "Hot" or "Warm"
2. If not, redirect to appropriate flow (Nurture or DQ Close)

### Step 1: Qualification Summary (Autonomous Node)

Frame the result naturally. Never announce the score or mention CHAMP. These are tone examples - adapt naturally but keep the tone confident (Hot) or exploratory (Warm).

If `lead_score` = "Hot":
Example: "This sounds like a great fit. Let me get you booked with our team so you can explore this further."

If `lead_score` = "Warm":
Example: "There is definitely something to explore here. Let me get you connected with our team."

Set `conversation_stage` to `"handoff_hot"` or `"handoff_warm"` based on `lead_score`.

### Step 2: Present Calendly Slots (Standard Node)

1. Present available Calendly slots inline
2. Collect `visitor_email` if not already captured (required for Calendly booking)

### Step 3: Check Result (Standard Node)

Four possible outcomes:

**A. Visitor selects a slot:**
1. Agent books the meeting using collected data (visitor_name, visitor_email, visitor_company)
2. Set `conversion_action` to `"meeting_booked"`
3. Go to Outcome A (Completed)

**B. Visitor declines booking:**
1. Offer to capture their details: "No problem - I can have someone reach out instead. Can I take your details?"
2. If visitor agrees: agent captures name, email, company, and optional message into Contact Form table. Set `conversion_action` to `"form_submitted"`. Go to Outcome A (Completed).
3. If visitor declines or responds ambiguously: set `conversion_action` to `"none"`. Provide salesai@halo-lab.team. Go to Outcome B (Not Converted).

**C. No available Calendly slots (within next 5 business days):**
1. Acknowledge: "It looks like the next few days are fully booked. Let me take your details so our team can reach out directly and find a time that works."
2. Set `calendly_fallback_used` to `true`
3. If visitor agrees: agent captures data into Contact Form table. Set `conversion_action` to `"form_submitted"`. Go to Outcome A (Completed).
4. If visitor declines or responds ambiguously: set `conversion_action` to `"none"`. Provide salesai@halo-lab.team. Go to Outcome B (Not Converted).

**D. Calendly integration fails (API error, timeout):**
1. Acknowledge: "Let me take your details and our team will reach out to schedule directly."
2. If visitor agrees: agent captures data into Contact Form table. Set `conversion_action` to `"form_submitted"`. Go to Outcome A (Completed).
3. If visitor declines or responds ambiguously: set `conversion_action` to `"none"`. Provide salesai@halo-lab.team. Go to Outcome B (Not Converted).

---

## Outcomes

### Outcome A: Completed (Standard Node)

1. If `conversion_action` = "meeting_booked": confirm the booking: "You are all set - our team will be in touch before your call." Trigger pre-call brief delivery. Set `pre_call_brief_sent` to `true`.
2. If `conversion_action` = "form_submitted": confirm: "Thanks - our team will reach out shortly."
3. Set `conversation_stage` to `"completed"`
4. End conversation

### Outcome B: Not Converted (Standard Node)

1. Keep `conversation_stage` as `"handoff_hot"` or `"handoff_warm"`
2. End conversation

---

## Pre-Call Brief

Triggered at Outcome A when `conversion_action` = "meeting_booked". Delivered via Slack (email fallback). Summary Agent generates the conversation summary automatically.

Trigger condition: `conversion_action` = "meeting_booked" AND `lead_score` = "Hot" or "Warm"

Content template:

```
New {lead_score} Lead - Pre-Call Brief

Visitor: {visitor_name} | {visitor_company} | {visitor_role} | {visitor_industry}

CHAMP Signals:
  CH: {ch_challenges} - {use_case}; {pain_points}
  A:  {a_authority} - {decision_authority}; stakeholders: {other_stakeholders}
  M:  {m_money} - {budget_indication}
  P:  {p_prioritization} - {timeline}; trigger: {trigger_event}

Tech Stack: CRM: {current_crm} | Website: {website_platform} | Chat: {current_chat_tools} | Integrations: {integrations_needed}
Volume: {leads_per_month} current, {expected_volume} expected

Missing Signals (Warm leads only):
  {List any CHAMP signal that is "negative" or "unclear"}

Knowledge Gap (if any):
  {knowledge_gap_question}

Conversation Summary:
  {conversation.SummaryAgent.summary}
```

---

## Variables Access

| Variable | Read | Write |
|---|---|---|
| lead_score | yes | no |
| visitor_name | yes | no |
| visitor_company | yes | no |
| visitor_role | yes | no |
| visitor_email | yes | yes |
| visitor_industry | yes | no |
| ch_challenges | yes | no |
| a_authority | yes | no |
| m_money | yes | no |
| p_prioritization | yes | no |
| use_case | yes | no |
| pain_points | yes | no |
| budget_indication | yes | no |
| decision_authority | yes | no |
| other_stakeholders | yes | no |
| timeline | yes | no |
| trigger_event | yes | no |
| leads_per_month | yes | no |
| expected_volume | yes | no |
| current_crm | yes | no |
| website_platform | yes | no |
| current_chat_tools | yes | no |
| integrations_needed | yes | no |
| knowledge_gap_question | yes | no |
| conversation_stage | yes | yes |
| conversion_action | yes | yes |
| calendly_fallback_used | yes | yes |
| pre_call_brief_sent | yes | yes |
| contact_form_question | yes | yes |

## Node Types

| Node | Type | Why |
|---|---|---|
| Step 0: Entry Guard | Standard | Deterministic check |
| Step 1: Qualification Summary | Autonomous | LLM generates natural framing |
| Step 2: Present Calendly Slots | Standard | Calendly integration card |
| Step 3: Check Result | Standard | Conditional routing |
| Outcome A: Completed | Standard | Set variables, confirm, trigger brief if booked |
| Outcome B: Not Converted | Standard | Set variables, end |

## Cards (Step 1 Autonomous Node only)

- Execute Workflow: Knowledge Gap Logger (UC2). Card description: "Use this when the Knowledge Base does not fully answer the visitor's question - either a partial answer or no answer at all."
- Execute Workflow: Handle Objections (UC3). Card description: "Use this when the visitor raises a concern or objection about pricing, timing, competitors, scope, trust, authority, or anything that signals hesitation."

## Builder Notes

- Step 0 guard prevents data errors from routing wrong leads to this workflow
- Step 1 is the only Autonomous Node. All other nodes are Standard
- Knowledge Bases: ENABLE on Step 1 only
- Calendly integration: use Botpress Calendly card or API for inline booking. The agent must know if booking succeeded to trigger the pre-call brief. Research Botpress Calendly integration options during implementation
- The "5 business days" availability threshold needs to be configurable
- Contact Form is a separate Botpress Table. Fields: name, email, company, optional message (contact_form_question). This table is also used by Nurture N4 contact form
- Pre-call brief: Summary Agent generates conversation summary automatically (conversation.SummaryAgent.summary). Deliver via Botpress webhook or Execute Code to Slack. Email fallback if Slack fails. If delivery fails, pre_call_brief_sent stays false - monitor in analytics
- QA: test Calendly integration reliability, form validation, pre-call brief delivery, Calendly error handling (Step 3D)
