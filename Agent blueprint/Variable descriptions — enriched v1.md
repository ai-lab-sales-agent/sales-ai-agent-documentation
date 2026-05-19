# Variable Descriptions — Enriched Draft

> Created: May 6, 2026

> Purpose: Updated Botpress variable descriptions to test whether richer descriptions allow slimmer node prompts. These descriptions appear in the auto-generated `tools.d.ts` that every Autonomous Node sees.
>
> Test with Discovery node first. If it works, roll out to other workflows.

---

## Conversation Variables

### Data Variables (capture verbatim from visitor)

**use_case**
REQUIRED — at least one of use_case or pain_points must be set before discovery can complete. What the visitor wants the AI agent to do for their business. Used to understand the visitor's goal and evaluate whether there is a real sales challenge. Capture at Discovery step 2.3 (Challenges) or whenever the visitor describes their goal. Feeds ch_challenges signal. Example values: "Qualify inbound leads from our website before they reach a sales rep", "Automate FAQ responses on our pricing page".

**pain_points**
REQUIRED — at least one of use_case or pain_points must be set before discovery can complete. The specific business problem the visitor is trying to solve. Used alongside use_case to evaluate the strength of the visitor's need. Capture at Discovery step 2.3 alongside use_case, or whenever the visitor describes a pain point. Feeds ch_challenges signal. Example values: "Our SDRs spend 60% of their time on leads that never convert", "We can't respond fast enough — leads go cold within hours".

**timeline**
REQUIRED. When the visitor wants the AI agent live. Used to assess project urgency and readiness. Capture at Discovery step 2.4 (Timeline) or whenever the visitor mentions their timeframe. Feeds p_prioritization signal. Example values: "1-2 months", "no deadline", "June", "till the end of Q3", "no rush", "this month".

**trigger_event**
External event driving urgency behind the timeline. Provides context for why the visitor needs the agent by a certain date. Capture at Discovery step 2.4 or whenever the visitor mentions an event. Feeds p_prioritization signal. Example values: "conference in May", "product launch", "Q2 target", "funding round".

**budget_indication**
REQUIRED. Budget range the visitor has in mind for the project. Used to assess financial fit and detect DQ scenarios. Accept any currency; evaluate against approximate EUR equivalent. Below EUR 5,000 triggers DQ. Capture at Discovery step 2.5 (Budget) or whenever the visitor mentions budget. Feeds m_money signal. Example values: "Around EUR 10,000-15,000", "We haven't allocated a specific budget yet", "No more than 5000 euros".

**decision_authority**
REQUIRED. The visitor's level of decision authority. Captures whether they can approve the purchase or need someone else's sign-off. Capture at Discovery step 2.6 (Authority) or whenever the visitor describes their decision-making role. Feeds a_authority signal. Example values: "Decision maker", "Needs manager's approval", "It's a joint decision between me and our CTO", "I have no idea".

**other_stakeholders**
Other people involved in the buying decision beyond the visitor. Provides context on the decision-making structure. Capture at Discovery step 2.6 alongside decision_authority, or whenever the visitor mentions other decision-makers. Example values: "Our CTO and VP of Sales", "Just me", "My manager needs to sign off".

**leads_per_month**
Current number of inbound leads the visitor's company receives per month (numeric or approximate range). Helps scope the AI agent's expected workload. Optional — capture at Discovery step 2.7 (Technical Context) or whenever the visitor mentions their lead volume.

**expected_volume**
Expected number of conversations per month after the AI agent is launched (numeric or approximate range). Used to plan capacity and set expectations. Optional — capture at Discovery step 2.7 or whenever the visitor mentions expected volume.

**current_crm**
CRM system currently used by the visitor's company. Relevant for integration planning. Optional — capture at Discovery step 2.7 or whenever the visitor mentions their CRM.

**current_chat_tools**
Existing chat tools on the visitor's website. Relevant for understanding the current setup being replaced or augmented. Optional — capture at Discovery step 2.7 or whenever the visitor mentions their chat tools. Example values: "Intercom", "Drift", "LiveChat", "none".

**website_platform**
Website platform the visitor's company uses (e.g., WordPress, Webflow, custom). Relevant for webchat widget deployment. Optional — capture at Discovery step 2.7 or whenever the visitor mentions their platform.

**integrations_needed**
Specific integrations the visitor needs the AI agent to connect with (e.g., CRM, calendar, Slack). Helps scope technical requirements. Optional — capture at Discovery step 2.7 or whenever the visitor mentions integration needs.

**visitor_team_size**
Team size or company size. Provides context on the visitor's organization scale. Optional — capture at Discovery step 2.2 (Company Profile) or whenever the visitor mentions their team or company size. Example values: "3 people", "sales team of 5", "about 50 employees", "small team".

### CHAMP Signal Variables

> Evaluate based only on what the visitor explicitly stated. Never infer from role, tone, or context. Once set, signals can change as the visitor updates their answers, but never back to "none".

**ch_challenges**
CH — Challenges. Derived from use_case and pain_points. Mandatory to evaluate to complete qualification. Allowed values: "none" (default, not yet evaluated), "positive" (clear use case or specific pain), "negative" (no specific use case or pain point), "unclear" (vague curiosity, no clear problem).

**p_prioritization**
P — Prioritization. Derived from timeline and trigger_event. Mandatory to evaluate to complete qualification. Allowed values: "none" (default, not yet evaluated), "positive" (realistic deadline, a few weeks or more), "negative" (unrealistically short, less than a few weeks), "unclear" (flexible, "just exploring", no date).

**m_money**
M — Money. Derived from budget_indication. Mandatory to evaluate to complete qualification. Allowed values: "none" (default, not yet evaluated), "positive" (budget >= EUR 5,000), "negative" (explicitly below EUR 5,000 — triggers DQ), "unclear" (not mentioned, "not sure", flexible).

**a_authority**
A — Authority. Derived from decision_authority and other_stakeholders. Mandatory to evaluate to complete qualification. Allowed values: "none" (default, not yet evaluated), "positive" (visitor can approve the purchase themselves), "negative" (needs manager approval, no authority), "unclear" (vague answer, unclear role).

### Scoring & Routing Variables (set by Scoring Engine, not by Discovery)

**champ_positive_count**
Count of CHAMP signals evaluated as "positive" (0-4). Computed by the Scoring Engine after discovery is complete. Used as the primary input for lead scoring logic.

**lead_score**
Final lead classification based on CHAMP evaluation. Set by the Scoring Engine. Determines which flow the visitor enters next (Handoff, Nurture, or DQ close). Allowed values: "unscored" (default), "Hot", "Warm", "Nurture", "DQ".

**lead_score_reason**
Brief explanation of why the lead received its score. Set by the Scoring Engine. Stored in the Leads Table for the sales team to review. Example values: "Budget below EUR 5,000 — insufficient for project scope", "Challenges clear, budget confirmed, timeline realistic, decision-maker. All 4 CHAMP signals positive".

**conversion_action**
What conversion action occurred during the conversation. Set during Handoff (booking link shared), Edge Cases (skip-to-human or asking to book a call), or Nurture (N3 nudge close). Allowed values: "none" (default), "booking_link_shared", "form_submitted".

### Nurture Variables (set by Nurture flow)

**nurture_stage**
Current stage in the Nurture flow. Tracks the visitor's progress through nurture steps. Allowed values: "none" (default), "N1_topics_offered", "N2_requalified", "N3_Upgraded" (set by Scoring Engine after re-qualification), "N3_Nudged" (set by Scoring Engine), "N3_closed".

**nurture_upgraded_to**
New lead score if the visitor was upgraded from Nurture after re-qualification at N2. Set when the Scoring Engine re-evaluates and the result is higher than Nurture. Allowed values: "Warm", "Hot", null (default).

### Other Conversation Variables

**contact_form_question**
Optional message the visitor wants to pass to the team, captured during form_submitted scenarios (e.g., Handoff booking fallback, Edge Cases skip-to-human, Nurture close). Null if no form was submitted or no message was provided.

**leads_record_created**
Boolean flag set by the Execute Code card after a Leads Table row is created. Handled programmatically — not set by any Autonomous Node.

---

## User Variables

**visitor_name**
Visitor's name. Used for personalization throughout the conversation. Save whenever shared during any conversation turn.

**visitor_company**
REQUIRED. Company or organization name. Capture at Discovery step 2.2 (Company Profile) or whenever the visitor mentions their company.

**visitor_role**
REQUIRED. Visitor's role at their company. Provides context on seniority and decision-making capacity. Capture at Discovery step 2.2 or whenever the visitor mentions their role. Example values: "Founder", "Sales Manager", "Marketing Lead", "Enterprise buyer".

**visitor_industry**
Industry or vertical of the visitor's company. Provides context for tailoring the conversation and checking ICP fit. Infer from the company description at Discovery step 2.2, or whenever identifiable. Optional — only set if clearly identifiable. Example values: "EdTech", "SaaS", "E-commerce", "Real estate", "Healthcare".

**visitor_location**
Company location or country. Used for ICP exclusion check (Russia-based = automatic DQ). Capture at Discovery step 2.2 or whenever the visitor mentions their location.

**visitor_email**
Visitor's email address. Collected when the visitor shares it directly, or requested by the bot for form_submitted scenarios (Handoff booking fallback, Edge Cases skip-to-human, Nurture close). Before saving, verify the email includes an @ symbol and a domain with a TLD (e.g., .com, .org). If it looks incomplete (e.g., "tes@gmail"), ask the visitor to double-check before saving.

**stage**
Current stage in the conversation flow. USER scope variable — must be written to user scope only. Updated at each discovery step to track progress and enable resumption after sub-workflows. Allowed values for Discovery: "introduction", "discovery_company", "discovery_use_case", "discovery_timeline", "discovery_budget", "discovery_authority", "discovery_current_stack". Values used by other workflows: "handoff_hot", "handoff_warm", "nurture", "dq_closed".

**icp_exclusion_flag**
True if an ICP hard exclusion is detected (Adult/18+ industry or Russia-based company). Triggers immediate DQ — polite close, no booking link, no form. Default: false.

**qualification_complete**
Whether the full qualification flow has finished for this visitor. Set to true after the Scoring Engine completes scoring and the visitor has been routed to the appropriate outcome (Handoff, Nurture, or DQ close). Used to prevent re-running qualification on returning visitors. Default: false.

---

## Workflow Variables

### Discovery

**discovery_complete**
Gate variable for the Discovery-to-Scoring-Engine transition. Set to true at Discovery step 2.8 after all required data is captured and all four CHAMP signals are evaluated (each set to "positive", "negative", or "unclear" — none left as "none"). Triggers the AI Transition to the Scoring Engine. Default: false.

### Handoff

**handoff_authorized**
Gate variable set by the Execute Code card at workflow entry. Filters out non-Hot/Warm leads before the Autonomous Node runs. Default: false.

**handoff_completed**
Gate variable for the Handoff workflow transition. Set to true when the handoff interaction is complete — visitor booked, submitted contact form, declined, or conversation closed. Triggers exit from the Handoff workflow. Default: false.

### Handle Objections

**objection_addressed**
Gate variable for the Handle Objections workflow transition. Set to true when the objection is fully addressed (visitor confirms, signals readiness to move forward, or conversation is closed). Triggers exit back to the calling workflow. Default: false.

### Nurture

**nurture_requalified**
Gate variable for the Nurture workflow transition from the N1/N2 node. Set to true when the visitor has been through the requalification step — whether signals improved or not. Triggers transition to the Scoring Engine for re-evaluation. Default: false.

**n3_closed**
Gate variable for the Nurture Close (N3) node transition. Set to true when the visitor has either submitted contact details or declined. Triggers exit from the Nurture Close node. Default: false.

### Edge Cases

**edge_case_handled**
Gate variable for the Edge Cases workflow transition. Set when the edge case is fully resolved and the workflow should exit. The Execute Code card uses this value to set edge_case_soft_close and DQ variables. Allowed values: "spam_abuse", "off_topic", "skip_human", "prior_convers", "sensitive_data", "out_scope", "money_dq", "icp_dq", "already_dq", "resolved" (first-occurrence spam/off-topic where visitor cooperated). Default: null.

**edge_case_soft_close**
Signals that the conversation should end after the edge case — the visitor should not return to the caller workflow. Set by the Execute Code card based on edge_case_handled value. True for: spam_abuse, off_topic, skip_human, out_scope, money_dq, icp_dq, already_dq. False for: prior_convers, sensitive_data, resolved. Default: false.
