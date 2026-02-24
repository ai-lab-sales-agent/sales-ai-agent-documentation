# JSON Schemas v2 — Sales AI Agent

> These schemas define the data model for the CHAMP Scored Flow.
> Use them to configure: (1) Botpress Conversation Variables, (2) Botpress Table columns, (3) batch testing CSV structure.

---

## Conversation Variables

The agent evaluates CHAMP signals and assigns `lead_score` automatically. Insufficient budget (< €5,000) triggers automatic DQ.

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "SalesAgentConversation_v2",
  "description": "Conversation variables for CHAMP Scored Flow. Agent evaluates CHAMP signals and routes leads automatically: Hot/Warm → Calendly (tagged), Nurture → resources + re-qualify, DQ → polite close. Budget < €5,000 = DQ.",
  "type": "object",
  "properties": {

    "visitor_id": {
      "type": "string",
      "description": "Auto-generated from Botpress built-in userId. Persisted via browser cookie."
    },

    "visitor_name": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Visitor's name. Collected via conversation or contact form.",
      "default": null
    },

    "visitor_email": {
      "type": ["string", "null"],
      "format": "email",
      "description": "Email address. Collected via contact form or Calendly booking.",
      "default": null
    },

    "visitor_company": {
      "type": ["string", "null"],
      "maxLength": 150,
      "description": "Company or organization name. Collected at Step 3.",
      "default": null
    },

    "visitor_role": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Visitor's role: Founder, Sales Manager, Marketing Lead, Enterprise buyer, other. Collected at Step 3.",
      "default": null
    },

    "visitor_industry": {
      "type": ["string", "null"],
      "maxLength": 150,
      "description": "Industry/vertical. Inferred from company description at Step 3. Optional.",
      "default": null
    },

    "visitor_location": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Company location/country. Used for ICP exclusion check.",
      "default": null
    },

    "visitor_team_size": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Team size or company size. Inferred or stated at Step 3.",
      "default": null
    },

    "use_case": {
      "type": ["string", "null"],
      "description": "What the visitor wants the AI agent to do. Step 4.",
      "default": null
    },

    "pain_points": {
      "type": ["string", "null"],
      "description": "Primary challenge described by visitor. Step 5.",
      "default": null
    },

    "leads_per_month": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Current inbound leads/month. Step 6.",
      "default": null
    },

    "expected_volume": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Expected conversation volume after launch. Step 6.",
      "default": null
    },

    "current_crm": {
      "type": ["string", "null"],
      "description": "Current CRM. Step 7.",
      "default": null
    },

    "website_platform": {
      "type": ["string", "null"],
      "description": "Website platform. Step 7.",
      "default": null
    },

    "current_chat_tools": {
      "type": ["string", "null"],
      "description": "Existing chat tools. Step 7.",
      "default": null
    },

    "integrations_needed": {
      "type": ["string", "null"],
      "description": "Required integrations. Step 7.",
      "default": null
    },

    "timeline": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "When they want the agent live. Step 8.",
      "default": null
    },

    "trigger_event": {
      "type": ["string", "null"],
      "description": "Trigger event driving urgency. Step 8.",
      "default": null
    },

    "budget_indication": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Budget range or 'not discussed'. Step 9. Below €5,000 triggers DQ.",
      "default": null
    },

    "decision_authority": {
      "type": ["string", "null"],
      "description": "Role in decision-making. Step 9.",
      "default": null
    },

    "other_stakeholders": {
      "type": ["string", "null"],
      "description": "Who else is involved in the decision. Step 9.",
      "default": null
    },

    "icp_exclusion_flag": {
      "type": "boolean",
      "description": "True if ICP hard exclusion detected at Step 3. Triggers AUTO DQ — immediate polite close, no Calendly, no form.",
      "default": false
    },

    "champ_signals": {
      "type": "object",
      "description": "CHAMP signal evaluation. Agent populates these after discovery. Used to compute lead_score automatically.",
      "properties": {

        "ch_challenges": {
          "type": "string",
          "enum": ["positive", "negative", "unclear"],
          "description": "CH — Challenges. POSITIVE: clear use case + specific pain. NEGATIVE: vague curiosity, no clear problem. REQUIRED — without positive, max outcome = Nurture.",
          "default": "unclear"
        },

        "a_authority": {
          "type": "string",
          "enum": ["positive", "negative", "unclear"],
          "description": "A — Authority. POSITIVE: decision-maker or confirmed budget access. NEGATIVE: needs manager approval, unclear role. STRONG weight — differentiates Hot from Warm.",
          "default": "unclear"
        },

        "m_money": {
          "type": "string",
          "enum": ["positive", "negative", "unclear"],
          "description": "M — Money. POSITIVE: budget ≥ €5,000. NEGATIVE: below €5,000 (triggers DQ) or undefined. STRONG weight — differentiates Hot from Warm.",
          "default": "unclear"
        },

        "p_prioritization": {
          "type": "string",
          "enum": ["positive", "negative", "unclear"],
          "description": "P — Prioritization. POSITIVE: _+ weeks, realistic deadline or trigger event. NEGATIVE: under _ weeks or 'someday'. MODERATE weight — differentiates Hot from Warm.",
          "default": "unclear"
        }
      },
      "required": ["ch_challenges", "a_authority", "m_money", "p_prioritization"]
    },

    "champ_positive_count": {
      "type": "integer",
      "minimum": 0,
      "maximum": 4,
      "description": "Count of CHAMP signals evaluated as 'positive'. Computed after discovery. Used for scoring: 4 = Hot, CH + 1-2 others = Warm, CH weak = Nurture, no need / budget < €5,000 = DQ.",
      "default": 0
    },

    "lead_score": {
      "type": "string",
      "enum": ["unscored", "Hot", "Warm", "Nurture", "DQ"],
      "description": "Set AUTOMATICALLY by agent based on CHAMP evaluation. Starts as 'unscored' until CHAMP evaluation is complete. Hot = all 4 positive. Warm = CH positive + 1-2 of A/M/P positive. Nurture = CH weak/vague. DQ = no need, wrong scope, spam, ICP exclusion, or budget < €5,000.",
      "default": "unscored"
    },

    "lead_score_reason": {
      "type": ["string", "null"],
      "description": "Brief explanation of why the lead was scored this way. Used by sales team to understand agent's reasoning. E.g., 'Budget below €5,000 — insufficient for project scope.' or 'Challenges clear, budget confirmed, timeline realistic, decision-maker. All 4 CHAMP signals positive.'",
      "default": null
    },

    "nurture_stage": {
      "type": ["string", "null"],
      "enum": ["N1_resources_shared", "N2_checked_in", "N3_requalified", "N4_upgraded", "N4_nudged", "N5_warm_closed", null],
      "description": "Current stage in the Nurture flow. Tracks progress through N1-N5. Null if not in nurture.",
      "default": null
    },

    "nurture_upgraded_to": {
      "type": ["string", "null"],
      "enum": ["Warm", "Hot", null],
      "description": "If lead was upgraded from Nurture after re-qualification (N4 upgrade path). Null if no upgrade.",
      "default": null
    },

    "conversion_action": {
      "type": "string",
      "enum": ["meeting_booked", "form_submitted", "resources_sent", "none"],
      "description": "What conversion action occurred.",
      "default": "none"
    },

    "contact_form_question": {
      "type": ["string", "null"],
      "description": "Question captured via knowledge-gap escalation. Null if no knowledge gap occurred.",
      "default": null
    },

    "knowledge_gap_triggered": {
      "type": "boolean",
      "description": "True if the visitor asked a question outside the agent's knowledge base. Agent offers an alternative + contact email, then continues discovery.",
      "default": false
    },

    "knowledge_gap_question": {
      "type": ["string", "null"],
      "description": "The specific question the visitor asked that the agent could not answer. Captured for sales team follow-up. Null if no knowledge gap occurred.",
      "default": null
    },

    "resources_shared": {
      "type": "array",
      "items": { "type": "string" },
      "description": "List of case study / resource links shared with the visitor.",
      "default": []
    },

    "conversation_summary": {
      "type": ["string", "null"],
      "description": "AI-generated summary of the conversation.",
      "default": null
    },

    "qualification_complete": {
      "type": "boolean",
      "description": "Whether minimum qualification data was collected.",
      "default": false
    },

    "conversation_stage": {
      "type": "string",
      "enum": [
        "greeting",
        "introduction",
        "discovery_company",
        "discovery_use_case",
        "discovery_pain_points",
        "discovery_volume",
        "discovery_current_stack",
        "discovery_timeline",
        "discovery_budget",
        "champ_evaluation",
        "qualification_summary",
        "handoff_hot",
        "handoff_warm",
        "nurture",
        "knowledge_gap",
        "dq_closed",
        "completed"
      ],
      "description": "Current stage in the conversation flow. Includes champ_evaluation, handoff_hot, handoff_warm stages for scored routing.",
      "default": "greeting"
    },

    "is_returning_visitor": {
      "type": "boolean",
      "description": "True if visitor recognized from previous session.",
      "default": false
    },

    "previous_lead_score": {
      "type": "string",
      "enum": ["unscored", "Hot", "Warm", "Nurture", "DQ"],
      "description": "Lead score from previous conversation (if returning visitor). Defaults to 'unscored' for new visitors or leads that were never scored.",
      "default": "unscored"
    },

    "created_at": {
      "type": "string",
      "format": "date-time",
      "description": "Timestamp when conversation started."
    },

    "updated_at": {
      "type": "string",
      "format": "date-time",
      "description": "Timestamp of last update to conversation data."
    }
  },

  "required": ["visitor_id", "champ_signals", "lead_score", "conversion_action", "qualification_complete", "conversation_stage"]
}
```

---

## Botpress Table Schema (Leads Table)

Persistent table where lead data is stored across sessions. `lead_score` is populated automatically by the agent after CHAMP evaluation.

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "LeadsTable_v2",
  "description": "Botpress Table schema for persisting lead data. One row per qualified conversation. Used by sales team to review leads and by returning visitor logic. Budget < €5,000 = DQ.",
  "type": "object",
  "properties": {

    "visitor_id":           { "type": "string",  "description": "Auto-generated by Botpress from built-in userId. Persisted via browser cookie. Used as row identifier and to match returning visitors." },
    "visitor_name":         { "type": ["string", "null"], "maxLength": 100 },
    "visitor_email":        { "type": ["string", "null"], "format": "email" },
    "visitor_company":      { "type": ["string", "null"], "maxLength": 150 },
    "visitor_role":         { "type": ["string", "null"], "maxLength": 100 },
    "visitor_industry":     { "type": ["string", "null"], "maxLength": 150 },
    "visitor_location":     { "type": ["string", "null"], "maxLength": 100 },

    "visitor_team_size":    { "type": ["string", "null"], "maxLength": 100, "description": "Team/company size. Collected at Step 3." },

    "use_case":             { "type": ["string", "null"] },
    "pain_points":          { "type": ["string", "null"] },
    "leads_per_month":      { "type": ["string", "null"], "maxLength": 100 },
    "expected_volume":      { "type": ["string", "null"], "maxLength": 100, "description": "Expected conversation volume after launch. Step 6." },
    "current_crm":          { "type": ["string", "null"] },
    "website_platform":     { "type": ["string", "null"], "description": "Website platform: WordPress, Webflow, custom, other. Step 7." },
    "current_chat_tools":   { "type": ["string", "null"], "description": "Existing chat tools: Intercom, Drift, LiveChat, none, other. Step 7." },
    "integrations_needed":  { "type": ["string", "null"] },

    "timeline":             { "type": ["string", "null"], "maxLength": 100 },
    "trigger_event":        { "type": ["string", "null"], "description": "Trigger event driving urgency: product launch, funding round, seasonal traffic, none. Step 8." },
    "budget_indication":    { "type": ["string", "null"], "maxLength": 100, "description": "Budget range. Below €5,000 triggers DQ." },
    "decision_authority":   { "type": ["string", "null"] },
    "other_stakeholders":   { "type": ["string", "null"], "description": "Other decision-makers involved. Step 9." },

    "ch_challenges":        { "type": "string", "enum": ["positive", "negative", "unclear"], "description": "CHAMP: Challenges signal." },
    "a_authority":          { "type": "string", "enum": ["positive", "negative", "unclear"], "description": "CHAMP: Authority signal." },
    "m_money":              { "type": "string", "enum": ["positive", "negative", "unclear"], "description": "CHAMP: Money signal. Negative + below €5,000 = DQ." },
    "p_prioritization":     { "type": "string", "enum": ["positive", "negative", "unclear"], "description": "CHAMP: Prioritization signal." },

    "champ_positive_count": { "type": "integer", "minimum": 0, "maximum": 4, "description": "Count of positive CHAMP signals (0-4).", "default": 0 },

    "lead_score":           { "type": "string",  "enum": ["unscored", "Hot", "Warm", "Nurture", "DQ"], "description": "Set automatically by agent after CHAMP evaluation." },
    "lead_score_reason":    { "type": ["string", "null"], "description": "Agent's reasoning for the score." },

    "nurture_stage":        { "type": ["string", "null"], "enum": ["N1_resources_shared", "N2_checked_in", "N3_requalified", "N4_upgraded", "N4_nudged", "N5_warm_closed", null], "description": "Current Nurture flow stage. Null if not in nurture." },
    "nurture_upgraded_to":  { "type": ["string", "null"], "enum": ["Warm", "Hot", null], "description": "If lead upgraded from Nurture after re-qualification. Null if no upgrade." },

    "conversion_action":    { "type": "string",  "enum": ["meeting_booked", "form_submitted", "resources_sent", "none"] },
    "contact_form_question":{ "type": ["string", "null"] },
    "knowledge_gap_triggered":  { "type": "boolean", "default": false, "description": "True if visitor asked a question outside agent's KB." },
    "knowledge_gap_question":   { "type": ["string", "null"], "description": "The question the agent could not answer. Null if no knowledge gap." },
    "conversation_summary": { "type": ["string", "null"] },

    "icp_exclusion_flag":   { "type": "boolean", "default": false },
    "is_returning_visitor":  { "type": "boolean", "default": false },
    "previous_lead_score":  { "type": "string", "enum": ["unscored", "Hot", "Warm", "Nurture", "DQ"], "description": "Lead score from previous conversation. Defaults to 'unscored' for new visitors.", "default": "unscored" },

    "created_at":           { "type": "string",  "format": "date-time" },
    "updated_at":           { "type": "string",  "format": "date-time" }
  },

  "required": ["visitor_id", "lead_score", "conversion_action", "created_at"]
}
```

---

## Contact Form Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "ContactForm_KnowledgeGap",
  "description": "Contact form schema. Triggered when visitor declines Calendly. For knowledge gaps, the agent offers an alternative and provides a contact email instead of a form — see Knowledge Gap Escalation in Qualification framework v2.",
  "type": "object",
  "properties": {
    "visitor_name":           { "type": "string",          "description": "Required. Free text. Maps to visitor_name in conversation schema." },
    "visitor_email":          { "type": "string",          "format": "email", "description": "Required. Validated format. Maps to visitor_email in conversation schema." },
    "visitor_company":        { "type": "string",          "description": "Required. Pre-filled if collected during conversation. Maps to visitor_company in conversation schema." },
    "contact_form_question":  { "type": ["string", "null"],"description": "Optional. Pre-filled with conversation summary or question from the user if available. Maps to contact_form_question in conversation schema." }
  },
  "required": ["visitor_name", "visitor_email", "visitor_company"]
}
```

---

## DQ Criteria Summary

The following conditions trigger automatic disqualification:

1. **ICP exclusion** — Adult/18+ content industry or Russia-based company (detected at Step 3)
2. **Insufficient budget** — Budget explicitly stated as less than €5,000 (detected at Step 9)
3. **No relevant need** — Visitor has no sales challenge (detected at Step 10 CHAMP evaluation)
4. **Wrong scope** — Visitor is looking for something unrelated (detected at Step 10)
5. **No sales team** — No one to augment (detected at Step 10)
6. **Spam / irrelevant** — Detected at any point

---

## Scoring Logic Summary

| Category    | CH (Challenges)  | A (Authority)  | M (Money)      | P (Prioritization) | Action                              |
|-------------|------------------|----------------|----------------|---------------------|-------------------------------------|
| **Hot**     | ✓ confirmed      | ✓ confirmed    | ✓ confirmed    | ✓ confirmed         | Calendly (tag: Hot)                 |
| **Warm**    | ✓ confirmed      | ✓ or missing   | ✓ or missing   | ✓ or missing        | Calendly (tag: Warm)                |
| **Nurture** | weak / vague     | unclear        | not established| not established     | Resources → re-qualify → soft nudge |
| **DQ**      | not relevant     | —              | < €5,000       | —                   | Polite close, no conversion         |

**Warm detail:** Challenges must be confirmed. 1–2 of A/M/P may be missing, but at least 1 of the 3 must be confirmed.

**DQ triggers:** No relevant need, wrong scope, no sales team, spam, ICP exclusion (Adult/18+, Russia-based), or budget explicitly below €5,000.

**Key rule:** Without Challenges confirmed → max outcome is Nurture.

**Budget rule:** Budget explicitly below €5,000 → DQ regardless of other signals.
