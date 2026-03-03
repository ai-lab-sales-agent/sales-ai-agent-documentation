# UC3: Handle Objections — Sub-Workflow

> Botpress Studio: Create workflow "Handle Objections"
> Autonomous Node workflow.

> Created: March 3, 2026 | Updated: March 3, 2026

---

## How It Gets Triggered

Attached as an "Execute Workflow" card to: Early Discovery, Deep Discovery, Nurture nodes.

**Card description:**
> "Use this when the visitor raises a concern or objection about pricing, timing, competitors, scope, trust, authority, or anything that signals hesitation."

---

## Autonomous Node Instructions

Classify the visitor's objection into one of the following types. For every type: acknowledge the concern first, then respond accordingly. Use the Knowledge Base for all supporting content. If the objection touches multiple concerns, address the primary one first, then the secondary.

### Pricing
The visitor is concerned about cost.
1. Share the pricing framework from KB
2. Suggest a phased approach if appropriate
3. If the visitor reveals a specific budget amount, save it to `budget_indication` and evaluate `m_money` (same criteria as Deep Discovery Step 9). If budget is explicitly below threshold: set `m_money` to `"negative"` — this is a DQ indicator

### Timing
The visitor has concerns about when this can happen.
1. Acknowledge the concern
2. If the visitor had already shared timeline info, re-evaluate: this may change the P signal
3. Suggest a phased approach if appropriate

### Competitors
The visitor mentions a competing product or company.
1. Do not discuss the competitor by name
2. Redirect to the company's strengths from KB: case studies, specific capabilities, client results

### Scope
The visitor questions whether the service covers what they need.
1. Clarify what they are looking for
2. If it matches CH signals (use case, pain points): refine understanding, update `use_case` or `pain_points` if new info surfaces
3. If it clearly does not match: this is a DQ indicator. Set `ch_challenges` to `"negative"`

### Trust
The visitor doubts whether this works or whether the company can deliver.
1. Provide case studies and social proof from KB

### Authority
The visitor needs to check with others before deciding.
1. If `other_stakeholders` is empty, ask who else is involved in the decision. Save to `other_stakeholders`
2. Evaluate `a_authority`: if the visitor is not the decision-maker, set to `"unclear"`. If they explicitly say someone else decides, set to `"negative"`
3. Offer to share resources they can forward to their team

### Unidentified
The objection does not match any of the above types.
1. Acknowledge the concern
2. Save a brief summary of the concern to `unidentified_objection`
3. Provide the team's contact email: salesai@halo-lab.team

---

If the visitor raises the same objection again after you have already addressed it, acknowledge their persistence and offer to connect them with the team for a deeper conversation. Provide salesai@halo-lab.team.

## After Handling

Return to the calling workflow. The calling node resumes discovery from `conversation_stage`.

## Variables Access

| Variable | Read | Write |
|---|---|---|
| use_case | yes | yes |
| pain_points | yes | yes |
| ch_challenges | yes | yes |
| p_prioritization | yes | yes |
| a_authority | yes | yes |
| other_stakeholders | yes | yes |
| m_money | yes | yes |
| budget_indication | yes | yes |
| unidentified_objection | yes | yes |
| conversation_stage | yes | no |
| visitor_name | yes | no |
| visitor_company | yes | no |

## Cards

- Query Knowledge Base: for case studies, pricing frameworks, social proof (proactive retrieval)
- Execute Workflow: Knowledge Gap Logger (UC2). Card description: "Use this when the Knowledge Base does not fully answer the visitor's question — either a partial answer or no answer at all."

## Builder Notes

- Guards (NEVER Do / NEVER Promise) are inherited from global instructions — not duplicated in this prompt
- Scope mismatch: workflow sets ch_challenges to "negative" but does NOT transition to DQ itself. The calling node's transition cards handle DQ routing
- Budget DQ: if visitor reveals budget under threshold during pricing objection, workflow sets m_money to "negative". The calling node's DQ transition card handles routing
- unidentified_objection: add to Variables list and Leads Table for analytics
- Authority type added in v1.1: requires write access to a_authority and other_stakeholders
- Knowledge Bases: ENABLE on this node (reactive + proactive). Add KB card for proactive retrieval
