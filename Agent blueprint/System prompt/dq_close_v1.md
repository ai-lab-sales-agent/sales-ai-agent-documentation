# DQ Close — Sub-Workflow

> Botpress Studio: Create workflow "DQ Close"
> Standard Nodes only.

> Created: March 3, 2026 | Updated: March 3, 2026

---

## Context

This workflow is entered when `lead_score` = "DQ". Check `lead_score_reason` to determine the close message.

DQ triggers:
- ICP exclusion: adult/18+ content industry or russia-based company (detected at Step 3)
- Insufficient budget: budget explicitly stated as less than 5,000 EUR (detected at Step 9 or during UC3 pricing objection)
- Wrong scope: visitor looking for something unrelated to a Sales AI Agent (detected at Steps 4-5 or Step 10)
- No sales team: no one to augment (detected at Steps 4-5 or Step 10)
- Spam / irrelevant: detected at any point

---

## Steps

### Step 1: Check Reason (Standard Node)

1. If `lead_score_reason` is not set, default to generic close (Step 2B)
2. If `lead_score_reason` = "icp_exclusion" or "spam": go to Step 2A
3. All other reasons: go to Step 2B

### Step 2A: ICP / Spam Close (Standard Node)

1. "Thanks for reaching out - this isn't something we're able to help with."
2. Set `lead_score` to `"DQ"`
3. Set `conversation_stage` to `"dq_closed"`
4. Set `conversion_action` to `"none"`
5. End conversation

### Step 2B: CHAMP / Scope / Budget Close (Standard Node)

1. "Thanks for reaching out - based on what you've described, this service may not be the right fit right now."
2. If `lead_score_reason` = "wrong_scope": add "If you're interested in other services, you can reach our team at inquiry@halo-lab.com."
3. Set `lead_score` to `"DQ"`
4. Set `conversation_stage` to `"dq_closed"`
5. Set `conversion_action` to `"none"`
6. End conversation

---

## What NOT to do

- No Calendly
- No contact form
- No resources or case studies
- No further discovery questions

---

## Variables Access

| Variable | Read | Write |
|---|---|---|
| lead_score | yes | yes |
| lead_score_reason | yes | no |
| conversation_stage | yes | yes |
| conversion_action | yes | yes |

## Builder Notes

- All Standard Nodes - no LLM, no Autonomous Node, no KB
- Step 1 routes to 2A or 2B based on lead_score_reason. If lead_score_reason is missing, defaults to 2B (generic close)
- lead_score_reason must be set BEFORE entering this workflow. Calling nodes: Early Discovery (icp_exclusion, wrong_scope, no_sales_team), Deep Discovery (insufficient_budget), CHAMP Scoring JS (wrong_scope, no_sales_team), UC3 (insufficient_budget during pricing objection), Nurture N3 (insufficient_budget during re-qualification), Global edge cases (spam). QA: verify all entry points set this correctly
- lead_score_reason values: "icp_exclusion", "insufficient_budget", "wrong_scope", "no_sales_team", "spam"
- ICP exclusion has a shorter message because no further context is appropriate
- Wrong scope gets the inquiry@halo-lab.com redirect for other services
- Returning DQ visitors are handled by UC4, not this workflow. UC4 may offer resources to CHAMP-based DQ visitors but never to ICP/spam DQ visitors
- lead_score_reason is a new variable - add to Variables list and Leads Table
- If multiple DQ reasons could apply, the first one detected takes priority (ICP at Step 3, budget at Step 9, CHAMP at Step 10)
