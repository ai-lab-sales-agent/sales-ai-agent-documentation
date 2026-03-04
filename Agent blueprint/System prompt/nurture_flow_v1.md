# Nurture Flow — Sub-Workflow

> Botpress Studio: Create workflow "Nurture Flow"
> Autonomous Node workflow.

> Created: March 3, 2026 | Updated: March 4, 2026

---

## Context

Check `qualification_complete` at N4 to determine the upgrade path.

You have read access to: `visitor_name`, `visitor_company`, `use_case`, `pain_points`. Use these to personalize.

## Entry Condition

If `nurture_stage` is already set (not null), skip N1 and start from N2. This handles returning visitors who already received resources, as well as re-entry after a failed upgrade.

---

## Steps

### N1: Share Resources
1. Share 1-2 case studies from KB most relevant to the visitor's industry or use case
2. If KB has no closely matching examples, share the most related ones and be transparent: "These aren't an exact match for your industry, but they show the kind of results our agents deliver."
3. Save `resources_shared` (JSON array, e.g. `["Fintech case study", "ROI calculator"]`)
4. Set `nurture_stage` to `"N1_resources_shared"`

### N2: Check In
1. Ask if the resources resonated. Example: "Did any of those feel close to what you're thinking about?"
2. Set `nurture_stage` to `"N2_checked_in"`

### N3: Re-qualification (CHAMP Revisit)
1. Ask what would need to change for them to move forward. Example: "What would need to change on your side for this to become something you'd want to move forward on?"
2. Update CHAMP signals that become clearer:
   - Use case or pain points become specific: update `use_case`, `pain_points`, set `ch_challenges` to `"positive"`
   - Timeline info surfaces: update `timeline`, evaluate `p_prioritization`
   - Budget info surfaces: update `budget_indication`, evaluate `m_money`
   - Authority info surfaces: update `decision_authority`, evaluate `a_authority`
3. Set `nurture_stage` to `"N3_requalified"`

### N4: Evaluate Upgrade

**If signals improved AND `qualification_complete` = true:**
1. CH + at least 1 of A/M/P confirmed: set `nurture_upgraded_to` to `"Warm"`, set `lead_score` to `"Warm"`
2. All 4 positive: set `nurture_upgraded_to` to `"Hot"`, set `lead_score` to `"Hot"`
3. Set `nurture_stage` to `"N4_upgraded"`
4. Redirect to Hot/Warm Handoff

**If CH became positive AND `qualification_complete` = false:**
1. Set `nurture_stage` to `"N4_upgraded"`
2. Set `conversation_stage` to `"discovery_volume"`
3. Redirect to Deep Discovery

**If signals did NOT improve:**
1. Offer to collect their details: "Would it help if I took your details so our team can reach out when the timing is better?"
2. If accepted: collect name, email, company via contact form. Set `nurture_stage` to `"N4_nudged"`. Set `conversion_action` to `"form_submitted"`
3. If declined or ambiguous ("maybe later", "not now"): move to N5

### N5: Warm Close
1. Close positively: "Totally understood — come back when the timing is better. I'll make a note so our team has context if you do reach out."
2. Set `nurture_stage` to `"N5_warm_closed"`
3. Set `conversion_action` to `"resources_sent"`

---

## Variables Access

| Variable | Read | Write |
|---|---|---|
| visitor_name | yes | no |
| visitor_company | yes | no |
| use_case | yes | yes |
| pain_points | yes | yes |
| ch_challenges | yes | yes |
| a_authority | yes | yes |
| m_money | yes | yes |
| p_prioritization | yes | yes |
| timeline | yes | yes |
| budget_indication | yes | yes |
| decision_authority | yes | yes |
| qualification_complete | yes | no |
| nurture_stage | yes | yes |
| nurture_upgraded_to | yes | yes |
| lead_score | yes | yes |
| resources_shared | yes | yes |
| conversion_action | yes | yes |
| lead_score_reason | yes | yes |
| conversation_stage | yes | yes |

## Cards

- Query Knowledge Base: for proactively pulling case studies at N1. Knowledge Agent handles reactive visitor questions separately.
- Execute Workflow: Knowledge Gap Logger (UC2). Card description: "Use this when the Knowledge Base does not fully answer the visitor's question — either a partial answer or no answer at all."
- Execute Workflow: Handle Objections (UC3). Card description: "Use this when the visitor raises a concern or objection about pricing, timing, competitors, scope, trust, or anything that signals hesitation."
- Transition: DQ Close workflow (condition: m_money = "negative"). Set `lead_score_reason` to `"insufficient_budget"` before transition. Handles budget disqualification during re-qualification at N3.
- Transition: Deep Discovery node (condition: nurture_stage = "N4_upgraded" AND qualification_complete = false). Routes Early Nurture upgrades back to Step 6.
- Transition: Hot/Warm Handoff workflow (condition: nurture_stage = "N4_upgraded" AND qualification_complete = true). Routes Standard Nurture upgrades to Calendly.

## Builder Notes

- qualification_complete is the key differentiator at N4 — test both paths thoroughly
- At N3, the LLM re-evaluates CHAMP signals from conversation context, not the CHAMP Scoring JS. For Standard Nurture upgrades, the LLM sets nurture_upgraded_to AND lead_score directly. For Early Nurture, the visitor goes through Deep Discovery then CHAMP Scoring as normal
- lead_score has WRITE access — needed for Standard Nurture upgrades
- resources_shared format: JSON array of strings. UC4 checks this to avoid sending duplicate content
- Knowledge Bases: ENABLE on this node (reactive + proactive). Add a KB card for proactive case study retrieval at N1
- QA: test whether Botpress timeout flow fires inside sub-workflows
