# JSON Schemas — Sales AI Agent

> These schemas define the data model for both Options.
> Use them to configure: (1) Botpress Conversation Variables, (2) Botpress Table columns, (3) batch testing CSV structure.

---

## Option A — Soft Flow (No Scoring)

### Conversation Variables

Option A collects the same discovery data but does NOT evaluate CHAMP signals automatically. `lead_score` is set by the sales team after the call, not by the agent.

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "SalesAgentConversation_OptionA",
  "description": "Conversation variables for Option A (Soft Flow). Agent collects CHAMP data but does not score leads automatically. All leads → Calendly. Sales team decides fit.",
  "type": "object",
  "properties": {

    "visitor_id": {
      "type": "string",
      "description": "Auto-generated from Botpress built-in userId. Persisted via browser cookie. Used to identify returning visitors."
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
      "description": "Company or organization name. Collected at Step 3 (DISCOVERY_COMPANY).",
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
      "description": "Industry/vertical. Inferred from company description at Step 3. Optional — not asked separately.",
      "default": null
    },

    "visitor_location": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Company location/country. Inferred from conversation. Used for ICP exclusion check (Russia = hard DQ).",
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
      "description": "What the visitor wants the AI agent to do: qualify leads, book meetings, handle FAQs, handle objections. Step 4 (DISCOVERY_USE_CASE).",
      "default": null
    },

    "pain_points": {
      "type": ["string", "null"],
      "description": "Primary challenge: slow lead response, team overwhelmed, low conversion, no 24/7 coverage, lost deals. Step 5 (DISCOVERY_PAIN_POINTS).",
      "default": null
    },

    "leads_per_month": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Current inbound leads/month. Step 6 (DISCOVERY_VOLUME). Used for Botpress plan scoping.",
      "default": null
    },

    "expected_volume": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Expected conversation volume after launch. Step 6 (DISCOVERY_VOLUME).",
      "default": null
    },

    "current_crm": {
      "type": ["string", "null"],
      "description": "Current CRM: HubSpot, Salesforce, Pipedrive, none, other. Step 7 (DISCOVERY_CURRENT_STACK).",
      "default": null
    },

    "website_platform": {
      "type": ["string", "null"],
      "description": "Website platform: WordPress, Webflow, custom, other. Step 7.",
      "default": null
    },

    "current_chat_tools": {
      "type": ["string", "null"],
      "description": "Existing chat tools: Intercom, Drift, LiveChat, none, other. Step 7.",
      "default": null
    },

    "integrations_needed": {
      "type": ["string", "null"],
      "description": "Required integrations: Calendly, Slack, email, other. Step 7.",
      "default": null
    },

    "timeline": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "When they want the agent live. Free text: 'ASAP', '6 weeks', 'Q2', 'exploring'. Step 8 (DISCOVERY_TIMELINE).",
      "default": null
    },

    "trigger_event": {
      "type": ["string", "null"],
      "description": "Trigger event driving urgency: product launch, funding round, seasonal traffic, none. Step 8.",
      "default": null
    },

    "budget_indication": {
      "type": ["string", "null"],
      "maxLength": 100,
      "description": "Budget range or 'not discussed'. Reference floor: €5,000. Step 9 (DISCOVERY_BUDGET).",
      "default": null
    },

    "decision_authority": {
      "type": ["string", "null"],
      "description": "Role in decision-making: 'decision_maker', 'influencer', 'researcher', 'unknown'. Step 9.",
      "default": null
    },

    "other_stakeholders": {
      "type": ["string", "null"],
      "description": "Who else is involved in the decision. Step 9.",
      "default": null
    },

    "icp_exclusion_flag": {
      "type": "boolean",
      "description": "True if ICP hard exclusion detected at Step 3 (Adult/18+ industry or Russia-based company). Triggers immediate polite close.",
      "default": false
    },

    "lead_score": {
      "type": "string",
      "enum": ["unscored", "Hot", "Warm", "Nurture", "DQ"],
      "description": "Option A: defaults to 'unscored'. Sales team assigns the score after reviewing the lead data / Calendly call. Agent does NOT set this automatically.",
      "default": "unscored"
    },

    "conversion_action": {
      "type": "string",
      "enum": ["meeting_booked", "form_submitted", "resources_sent", "none"],
      "description": "What conversion action occurred: Calendly booked, contact form submitted, resources shared, or none.",
      "default": "none"
    },

    "contact_form_question": {
      "type": ["string", "null"],
      "description": "The specific question captured via knowledge-gap contact form. Null if form was not triggered.",
      "default": null
    },

    "resources_shared": {
      "type": "array",
      "items": { "type": "string" },
      "description": "List of case study / resource links shared with the visitor during nurture flow.",
      "default": []
    },

    "conversation_summary": {
      "type": ["string", "null"],
      "description": "AI-generated summary of the conversation. Auto-populated at end of conversation.",
      "default": null
    },

    "qualification_complete": {
      "type": "boolean",
      "description": "Whether minimum qualification data was collected (company + use case at minimum).",
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
        "qualification_summary",
        "handoff",
        "nurture",
        "knowledge_gap",
        "completed",
        "dq_closed"
      ],
      "description": "Current stage in the conversation flow. Tracks where the visitor is in the discovery sequence.",
      "default": "greeting"
    },

    "is_returning_visitor": {
      "type": "boolean",
      "description": "True if visitor has been recognized from a previous session (via email or cookie).",
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

  "required": ["visitor_id", "lead_score", "conversion_action", "qualification_complete", "conversation_stage"]
}
```

---

## Option B — Scored Flow (Hot / Warm / Nurture / DQ)

### Conversation Variables

Option B extends Option A with CHAMP signal tracking. The agent evaluates signals and assigns `lead_score` automatically.

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "SalesAgentConversation_OptionB",
  "description": "Conversation variables for Option B (Scored Flow). Agent evaluates CHAMP signals and routes leads automatically: Hot/Warm → Calendly (tagged), Nurture → resources + re-qualify, DQ → polite close.",
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
      "description": "Budget range or 'not discussed'. Step 9.",
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
          "description": "M — Money. POSITIVE: budget ≥ €5,000 (starting price, subject to change). NEGATIVE: below €5,000 or undefined. STRONG weight — differentiates Hot from Warm.",
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
      "description": "Count of CHAMP signals evaluated as 'positive'. Computed after discovery. Used for scoring: 4 = Hot, CH + 1-2 others = Warm, CH weak = Nurture, no need = DQ.",
      "default": 0
    },

    "lead_score": {
      "type": "string",
      "enum": ["unscored", "Hot", "Warm", "Nurture", "DQ"],
      "description": "Option B: set AUTOMATICALLY by agent based on CHAMP evaluation. Starts as 'unscored' until CHAMP evaluation is complete. Hot = all 4 positive. Warm = CH positive + 1-2 of A/M/P positive. Nurture = CH weak/vague. DQ = no need, wrong scope, spam, or ICP exclusion.",
      "default": "unscored"
    },

    "lead_score_reason": {
      "type": ["string", "null"],
      "description": "Brief explanation of why the lead was scored this way. Used by sales team to understand agent's reasoning. E.g., 'Challenges clear, budget confirmed, timeline realistic, decision-maker. All 4 CHAMP signals positive.'",
      "default": null
    },

    "nurture_stage": {
      "type": ["string", "null"],
      "enum": ["N1_resources_shared", "N2_checked_in", "N3_requalified", "N4_upgraded", "N4_nudged", "N5_warm_closed", null],
      "description": "Current stage in the Nurture flow (Option B only). Tracks progress through N1-N5. Null if not in nurture.",
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
      "description": "Question captured via knowledge-gap contact form.",
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
      "description": "Current stage in the conversation flow. Option B adds: champ_evaluation, handoff_hot, handoff_warm stages.",
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

## Botpress Table Schema (Shared — Leads Table)

This is the persistent table where lead data is stored across sessions. Same structure for both options — the difference is whether `lead_score` is populated by the agent (Option B) or left as "unscored" for sales team (Option A).

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "LeadsTable",
  "description": "Botpress Table schema for persisting lead data. One row per qualified conversation. Used by sales team to review leads and by returning visitor logic.",
  "type": "object",
  "properties": {

    "id":                   { "type": "string",  "description": "Auto-generated row ID" },
    "visitor_id":           { "type": "string",  "description": "Botpress userId (cookie-based)" },
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
    "budget_indication":    { "type": ["string", "null"], "maxLength": 100 },
    "decision_authority":   { "type": ["string", "null"] },
    "other_stakeholders":   { "type": ["string", "null"], "description": "Other decision-makers involved. Step 9." },

    "ch_challenges":        { "type": ["string", "null"], "enum": ["positive", "negative", "unclear", null], "description": "Option B only. Null for Option A." },
    "a_authority":          { "type": ["string", "null"], "enum": ["positive", "negative", "unclear", null], "description": "Option B only." },
    "m_money":              { "type": ["string", "null"], "enum": ["positive", "negative", "unclear", null], "description": "Option B only." },
    "p_prioritization":     { "type": ["string", "null"], "enum": ["positive", "negative", "unclear", null], "description": "Option B only." },

    "champ_positive_count": { "type": ["integer", "null"], "minimum": 0, "maximum": 4, "description": "Option B only. Count of positive CHAMP signals (0-4). Null for Option A.", "default": null },

    "lead_score":           { "type": "string",  "enum": ["unscored", "Hot", "Warm", "Nurture", "DQ"] },
    "lead_score_reason":    { "type": ["string", "null"], "description": "Option B only." },

    "nurture_stage":        { "type": ["string", "null"], "enum": ["N1_resources_shared", "N2_checked_in", "N3_requalified", "N4_upgraded", "N4_nudged", "N5_warm_closed", null], "description": "Option B only. Current Nurture flow stage. Null if not in nurture or Option A." },
    "nurture_upgraded_to":  { "type": ["string", "null"], "enum": ["Warm", "Hot", null], "description": "Option B only. If lead upgraded from Nurture after re-qualification. Null if no upgrade." },

    "conversion_action":    { "type": "string",  "enum": ["meeting_booked", "form_submitted", "resources_sent", "none"] },
    "contact_form_question":{ "type": ["string", "null"] },
    "conversation_summary": { "type": ["string", "null"] },

    "icp_exclusion_flag":   { "type": "boolean", "default": false },
    "is_returning_visitor":  { "type": "boolean", "default": false },
    "previous_lead_score":  { "type": "string", "enum": ["unscored", "Hot", "Warm", "Nurture", "DQ"], "description": "Lead score from previous conversation. Defaults to 'unscored' for new visitors or leads that were never scored.", "default": "unscored" },

    "created_at":           { "type": "string",  "format": "date-time" },
    "updated_at":           { "type": "string",  "format": "date-time" }
  },

  "required": ["id", "visitor_id", "lead_score", "conversion_action", "created_at"]
}
```

---

## Contact Form Schema (Shared — Both Options)

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "ContactForm_KnowledgeGap",
  "description": "Contact form schema. Triggered when agent cannot answer a question (knowledge gap) or visitor declines Calendly. NOT tied to lead quality.",
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

## Differences Summary

### Scoring behaviour

**`lead_score` default**
- Option A: `"unscored"` — stays unscored until sales team reviews
- Option B: `"unscored"` — stays unscored until CHAMP evaluation completes, then set automatically

**`lead_score` set by**
- Option A: Sales team (manual)
- Option B: Agent (automatic, after CHAMP evaluation)

### Option B-only variables

These variables exist only in Option B. Option A does not use them.

**`champ_signals`** — object with 4 required signals (`ch_challenges`, `a_authority`, `m_money`, `p_prioritization`). Each signal is `"positive"`, `"negative"`, or `"unclear"`.

**`champ_positive_count`** — integer (0–4). Count of CHAMP signals evaluated as positive. Drives the scoring logic.

**`lead_score_reason`** — free-text explanation of why the agent scored the lead this way. Helps sales team understand the agent's reasoning.

**`nurture_stage`** — tracks progress through the Nurture flow: `N1_resources_shared` → `N2_checked_in` → `N3_requalified` → `N4_upgraded` / `N4_nudged` → `N5_warm_closed`.

**`nurture_upgraded_to`** — `"Warm"` or `"Hot"` if the lead was upgraded after re-qualification (N4 upgrade path). Null if no upgrade occurred.

### Conversation stage differences

**Option A** — 15 stages. Uses a single `handoff` stage for all qualified leads.

**Option B** — 17 stages. Adds `champ_evaluation`, `handoff_hot`, and `handoff_warm` to differentiate routing after scoring.

### ICP exclusion handling

**`icp_exclusion_flag`**
- Option A: Flags the lead for human review — agent still offers Calendly.
- Option B: Triggers AUTO DQ — immediate polite close, no Calendly, no form.

### Botpress Table CHAMP columns

**`ch_challenges`, `a_authority`, `m_money`, `p_prioritization`**
- Option A: Null (not populated by agent)
- Option B: Populated with `"positive"`, `"negative"`, or `"unclear"` after CHAMP evaluation
