# UC4: Returning Visitor — Sub-Workflow

> Botpress Studio: Create workflow "Returning Visitor"
> Mixed nodes (Standard + Autonomous).

> Created: March 3, 2026 | Updated: March 4, 2026

---

## Context

This flow is entered when `is_returning_visitor` = true. The agent looks up the visitor's previous data from the Users Table and routes based on `previous_lead_score`.

Data loaded from Users Table lookup:
- `visitor_name`, `visitor_company`
- `previous_lead_score` (Hot / Warm / Nurture / DQ / unscored)
- `conversation_stage`
- `qualification_complete` (boolean)
- `nurture_stage` (N1-N5 or null)
- `resources_shared` (array)
- `conversion_action` (meeting_booked / form_submitted / resources_sent / none)
- `lead_score_reason` (for DQ visitors)
- All CHAMP signals: `ch_challenges`, `a_authority`, `m_money`, `p_prioritization`

---

## Steps

### R0: Lookup (Standard Node)

1. Check Users Table for match by visitor_id
2. If no match: redirect to UC1 Step 1 (treat as new visitor)
3. If match: load previous data, set `is_returning_visitor` to `true`, go to R1

### R1: Greet by Name (Standard Node)

1. "Welcome back{, [visitor_name]}! Great to see you again."
2. Go to R2

### R2: Score Router (Standard Node)

Route based on `previous_lead_score`:
- "Hot" -> R3a
- "Warm" -> R4
- "Nurture" -> R5
- "DQ" -> R6
- "unscored" -> R7

---

## R3: Hot Returning

### R3a: Check Conversion (Standard Node)

Route based on `conversion_action`:
- "meeting_booked" -> R3b
- "form_submitted" -> R3c
- "none" -> R3c

### R3b: Meeting Follow-Up (Autonomous Node)

1. "Great to have you back. How can I help?"
2. No re-qualification. Answer questions via Knowledge Agent (UC2 if gap).
3. Warm close when done.

### R3c: Re-offer Booking (Autonomous Node)

1. Re-offer Calendly booking.
2. If visitor accepts: redirect to Handoff workflow.
3. If visitor declines AND `conversion_action` = "form_submitted": "No worries - your details are already with our team, they'll follow up. Anything else I can help with?" Answer questions via Knowledge Agent. Warm close when done.
4. If visitor declines AND `conversion_action` = "none": offer contact form. If accepted: agent captures data into Contact Form table. If declined: provide salesai@halo-lab.team. Warm close.

---

## R4: Warm Returning (Autonomous Node)

Check which of `a_authority`/`m_money`/`p_prioritization` are not "positive". Probe each missing signal specifically.

If the signal was "unclear" (not clearly stated/defined last time), ask openly:
- `m_money` unclear: "We didn't get to nail down budget last time. Do you have a sense of what you'd be looking to invest?"
- `a_authority` unclear: "We didn't get to discuss the decision process last time. Who else would be involved?"
- `p_prioritization` unclear: "Has your timeline become clearer since we last spoke?"

If the signal was "negative" (explicitly unfavorable last time), reference what they said:
- `m_money` negative: "Last time, budget was a concern. Has anything shifted?"
- `a_authority` negative: "You mentioned someone else makes the call on this. Any updates?"
- `p_prioritization` negative: "Last time, timing wasn't right. Has that changed?"

Update CHAMP signals based on responses. If any signal worsens (becomes negative), update it honestly but do not change routing. The sales team will see the flag in the pre-call brief.

**If all 4 CHAMP signals now positive:**
1. Set `lead_score` to `"Hot"`
2. Go to R3a (check previous conversion action)

**All other cases (still Warm, no change, or signals worsened):**
1. Keep `lead_score` as `"Warm"`
2. Go to R3a (check previous conversion action)

---

## R5: Nurture Returning (Standard Node)

Check `nurture_stage`:

**If `nurture_stage` = "N4_upgraded":**
1. Check `qualification_complete`:
   - If true: go to R3a (check previous conversion action)
   - If false: redirect to Deep Discovery (Step 6)

**If `nurture_stage` = "N1_resources_shared" or "N4_nudged" or "N5_warm_closed":**
1. Resources already shared. Redirect to Nurture Flow.
2. Nurture Flow checks `nurture_stage` at its first node: if already set, skip N1 and start from N2.

**If `nurture_stage` = "N2_checked_in" or "N3_requalified":**
1. Redirect to Nurture Flow.
2. Nurture Flow checks `nurture_stage` at its first node: skip N1, start from N2.

**If `nurture_stage` is null:**
1. Redirect to Nurture Flow (starts from N1 as normal).

Note: Nurture Flow needs a condition at the start of N1: if `nurture_stage` is already set (not null), skip to N2. This is how Botpress handles mid-workflow entry.

---

## R6: DQ Returning (Autonomous Node)

Check `lead_score_reason`:

**If `lead_score_reason` = "icp_exclusion" or "spam":**
1. No re-qualification. Welcome back politely.
2. Answer questions via Knowledge Agent (UC2 if gap).
3. Warm close: "Let me know if there is anything else I can help with."

**If `lead_score_reason` = "insufficient_budget" or "wrong_scope" or "no_sales_team":**
1. Ask if anything has changed, using context based on the reason:
   - insufficient_budget: "Last time, the budget wasn't quite aligned with what this service typically requires. Has anything changed?"
   - wrong_scope: "Last time, what you were looking for wasn't a direct fit for a Sales AI Agent. Has your focus shifted?"
   - no_sales_team: "Last time, it sounded like there wasn't a sales function in place to support. Has that changed?"
2. If visitor indicates changes: update relevant CHAMP signals.
   - If signals improved AND `qualification_complete` = true: redirect to the node that triggers CHAMP Scoring for re-scoring.
   - If signals improved AND `qualification_complete` = false: check `conversation_stage` and resume Discovery from where they left off.
3. If nothing changed: no re-qualification. Answer questions via Knowledge Agent. Warm close.

---

## R7: Unscored Returning (Standard Node)

1. "I remember we started chatting before. Want to pick up where we left off?"
2. If yes:
   - If `conversation_stage` = "discovery_company" or further: resume Discovery from the NEXT step after `conversation_stage` (the completed step does not need repeating).
   - If `conversation_stage` = "greeting" or "introduction" or empty: redirect to UC1 Step 1 (full discovery).
3. If no: answer questions via Knowledge Agent. Warm close when done.

---

## Variables Access

| Variable | Read | Write |
|---|---|---|
| is_returning_visitor | yes | yes |
| previous_lead_score | yes | no |
| visitor_name | yes | no |
| visitor_company | yes | no |
| conversation_stage | yes | yes |
| qualification_complete | yes | no |
| nurture_stage | yes | no |
| resources_shared | yes | no |
| conversion_action | yes | yes |
| lead_score | yes | yes |
| lead_score_reason | yes | no |
| ch_challenges | yes | yes |
| a_authority | yes | yes |
| m_money | yes | yes |
| p_prioritization | yes | yes |
| budget_indication | yes | yes |
| decision_authority | yes | yes |
| other_stakeholders | yes | yes |
| timeline | yes | yes |

## Node Types

| Node | Type | Why |
|---|---|---|
| R0: Lookup | Standard | Table query |
| R1: Greet by Name | Standard | Scripted message |
| R2: Score Router | Standard | Conditional routing |
| R3a: Check Conversion | Standard | Route by conversion_action |
| R3b: Meeting Follow-Up | Autonomous | Conversational Q&A |
| R3c: Re-offer Booking | Autonomous | Conversational re-offer + fallbacks |
| R4: Warm Returning | Autonomous | LLM probes missing signals |
| R5: Nurture Returning | Standard | Route by nurture_stage |
| R6: DQ Returning | Autonomous | LLM asks if situation changed |
| R7: Unscored Returning | Standard | Route by conversation_stage |

## Cards (R3b, R3c, R4, R6 Autonomous Nodes)

- Execute Workflow: Knowledge Gap Logger (UC2). Card description: "Use this when the Knowledge Base does not fully answer the visitor's question - either a partial answer or no answer at all."
- Execute Workflow: Handle Objections (UC3). Card description: "Use this when the visitor raises a concern or objection about pricing, timing, competitors, scope, trust, authority, or anything that signals hesitation."

## Builder Notes

- R0 lookup uses visitor_id (browser cookie). If cookie cleared, visitor appears new
- R3 split into 3 nodes: R3a (Standard routing), R3b (Autonomous for meeting follow-up), R3c (Autonomous for re-offer conversation)
- R4: if signals worsen during probing, update the variable honestly but do NOT route to DQ. Sales team sees the flag in the pre-call brief and handles it on the call
- R4 and R6 are Autonomous Nodes. Knowledge Bases: ENABLE on R3b, R3c, R4, R6
- R5 Nurture entry: Nurture Flow needs a condition at the start of N1 — if nurture_stage is already set (not null), skip to N2. This is how returning visitors bypass N1
- R4 and R5 route to R3a (not Handoff) because these visitors were already offered Calendly/form during their previous visit. R3 checks what conversion action already happened
- R6 ICP/spam DQ visitors: never re-qualify. They can ask questions (Knowledge Agent handles) but the agent does not probe or discover
- R6 CHAMP DQ: redirect to "the node that triggers CHAMP Scoring" means the Standard Node in the main flow that runs the CHAMP Scoring JS Execute Code card
- R3b meeting_booked: the agent does NOT know actual meeting date/time from Calendly. Opening is generic ("How can I help?") to handle both upcoming and past meetings
- resources_shared is checked to avoid sending duplicate case studies if the visitor returns to Nurture
- QA: test all 5 routing paths end-to-end. Test cookie-cleared returning visitors (should appear new)
