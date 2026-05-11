# Feedback Table — Filtered for New Bot

> Source: `table_01KJW3EEDRNBDZF9S9V0ATWVCG` (original bot, March–May 2026)
> Filtered: only rules NOT already covered in current system prompts (Personality Agent v4, Policy Agent v3, Discovery v3, Nurture v4, Nurture Close v2, Handoff v3, Handle Objections v4, Edge Cases v3, Tool Instructions v1, DQ Close v1)
> Created: May 11, 2026

---

## How to use

- **"Where to apply"** = which node instruction or prompt should encode this rule
- **"Row #"** = original table row for reference
- Rules marked **(variable)** are about variable handling logic — may belong in Execute Code cards or AN instructions

---

## Message Style

| # | Rule | Where to apply | Row |
|---|------|----------------|-----|
| 1 | **No "I" for product capabilities.** The bot can say "I'm here to help" but must NOT say "I qualify your leads" or "I book meetings." Use "it" or "the agent" when describing what the Sales AI Agent product does. The bot is a sales assistant, not the product itself. | Personality Agent or all AN instructions | 44 |
| 2 | **Don't use role/company as a name substitute.** If the visitor hasn't shared their name, skip personalization entirely — don't say "Thanks, Product Manager at Ferbie." Just move to the next question. | Personality Agent or Discovery | 9, 35, 72 |
| 3 | **No recap mid-discovery.** Don't summarize collected data in intermediate messages. Reserve recaps for the final qualification summary only (when discovery_complete = true). | Discovery | 38 |
| 4 | **Don't re-comment on topics already handled.** If budget was confirmed in Handle Objections, don't say "Now that we've got budget sorted" when returning to Discovery. Just move to the next question. | Discovery | 37 |
| 5 | **No closing language early.** Don't say "before we wrap up" or "one last thing" when discovery is still ongoing. Use natural transitions like "by the way" or go straight to the question. | Discovery | 78 |
| 6 | **No next steps or framing when sharing booking link.** Don't say "There's definitely something to explore here" or "Our team will review what we discussed." Just present the link and ask if there's anything else. | Handoff | 13, 19, 77 |

## Discovery Flow

| # | Rule | Where to apply | Row |
|---|------|----------------|-----|
| 7 | **No numbers or ranges in budget questions.** Don't anchor the visitor with "Are we talking a few thousand euros?" Just ask "What's your budget for this project?" | Discovery (section 2.5 example) | 29 |
| 8 | **Scope mismatch → clarify before continuing.** If the visitor's stated use case exceeds the agent's scope (e.g., "automate the entire sales process including closing"), clarify what the agent actually does and confirm alignment before proceeding with CHAMP. | Discovery (section 2.3) | 64 |
| 9 | **Researcher / intermediary = valid Nurture path.** If the visitor is researching on behalf of a client, don't gatekeep by demanding direct client engagement. Complete discovery with available data (A/M/P will be "unclear"), transition to Scoring Engine normally. | Discovery (Guardrails) | 61 |

## CHAMP & Scoring

| # | Rule | Where to apply | Row |
|---|------|----------------|-----|
| 10 | **(variable) Always save budget_indication.** When the visitor states any budget amount, save it to budget_indication — even when m_money is "negative" and it's a DQ. The exact amount matters for requalification later. | Discovery (Guardrails, Step 2) | 80 |
| 11 | **(variable) Budget DQ ≠ ICP exclusion.** When budget is below EUR 5,000, set m_money to "negative" only. Do NOT set icp_exclusion_flag to true. ICP exclusion is reserved for Russia-based companies and adult/18+ content. | Discovery (Guardrails, DQ triggers) | 81 |
| 12 | **Only Russia triggers ICP exclusion.** China, India, or any other country does NOT trigger icp_exclusion_flag. Only Russia-based companies are excluded. Continue discovery normally for all other locations. | Discovery (Guardrails, DQ triggers) | 53 |
| 13 | **Russia DQ: no "reach out in future" language.** For Russia-based ICP exclusions, close with a brief, polite message. Don't say "reach out if circumstances change" or "happy to reconnect." | DQ Close or Discovery (DQ triggers) | 10 |

## Nurture Flow

| # | Rule | Where to apply | Row |
|---|------|----------------|-----|
| 14 | **(variable) Email shared without buying signals → form_submitted + N5.** When a Nurture visitor shares their email but shows no improved CHAMP signals, set conversion_action to "form_submitted", nurture_stage to "N5_warm_closed", and close gracefully. Don't push implementation content. | Nurture Close | 47 |
| 15 | **Don't skip nurture stages.** Progress sequentially: N1 → N2 → N3 → N4 → N5. At N3, probe for barriers ("What would need to change for you to move forward?"). At N4, offer to capture details. Only then move to N5 warm close. | Nurture Close | 50, 54, 55 |
| 16 | **(variable) nurture_requalified = true → route to Requalification exit.** When setting the requalification flag, return the appropriate transition action — don't keep listening. | Nurture (workflow routing) | 56 |

## Handoff

| # | Rule | Where to apply | Row |
|---|------|----------------|-----|
| 17 | **(variable) Verify email is captured before setting form_submitted.** Don't set conversion_action to "form_submitted" and close the handoff until the visitor has actually provided their email. Ask for it first. | Handoff | 67 |
| 18 | **(variable) Distinguish role from name.** Don't assign "Operations Manager" to visitor_name. If you only have role and company, ask for name and email explicitly. | All ANs (variable handling) | 72 |

## Handle Objections

| # | Rule | Where to apply | Row |
|---|------|----------------|-----|
| 19 | **Objection confirmed → set flag + transition only.** When the visitor says the objection is cleared, set objection_addressed to true and return the transition action. Don't recap, don't provide next steps — those belong in the calling workflow. | Handle Objections (Close step) | 66 |
| 20 | **Don't fabricate process timelines.** Don't say "Week 1: discovery, Week 2-3: build." Describe steps (Discovery → Build & Integration → Launch) without attaching time estimates unless the KB explicitly states them. | Handle Objections (Process type) | 65 |

## Edge Cases

| # | Rule | Where to apply | Row |
|---|------|----------------|-----|
| 21 | **Partnership / non-sales inquiries = contact form, not out-of-scope.** If the visitor wants to discuss partnerships or services outside Sales AI Agent, capture it as a contact form question, provide salesai@halo-lab.team, and close. Don't search KB or treat as out-of-scope DQ. | Edge Cases | 70 |
| 22 | **Spam: close on 2nd occurrence.** First spam/profanity → redirect politely. Second occurrence → set lead_score to "DQ", conversion_action to "spam_abuse", and close. | Edge Cases | 75 |

---

**Total: 22 rules** extracted from 42 original feedback entries. The remaining 20 entries are already encoded in current system prompts.
