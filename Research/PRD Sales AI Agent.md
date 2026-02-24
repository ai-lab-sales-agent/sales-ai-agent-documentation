# PRD: Sales AI Agent

## AI Sales Agent (MVP) — Botpress Implementation

| Field            | Details                                                                        |
| :--------------- | :----------------------------------------------------------------------------- |
| **Document Type** | Product Requirements Document (PRD)                                           |
| **Product**       | AI Sales Agent — Website Chat                                                 |
| **Platform**      | Botpress (Cloud)                                                              |
| **Version**       | 1.0 — MVP                                                                     |
| **Date**          | February 19, 2026                                                             |
| **Status**        | Draft                                                                         |
| **Based On**      | Discovery Document (Feb 2026)                                                 |
| **Related Docs**  | MVP Roadmap, Comparison Table, Scoring Matrix, Botpress Overview              |

---

## 1. Overview

### 1.1 Problem Statement

Inbound leads are not responded to quickly enough. Sales managers spend time on unqualified calls. CRM updates are inconsistent. The pipeline leaks due to delays and manual inefficiencies.

### 1.2 Product Vision

Deploy an AI-powered sales agent on the company website that automates initial inbound interactions, qualifies leads before they reach a sales manager, explains services, surfaces relevant case studies, and prepares prospects for a meaningful sales conversation. The agent augments the sales team (SDR + pre-sales support) — it does not replace it.

### 1.3 MVP Scope Summary

The MVP delivers a reactive website chatbot (Botpress) that:

1. Qualifies inbound leads through conversational discovery
2. Explains services and recommends relevant case studies from the knowledge base
3. Answers typical sales-related questions (FAQ, pricing frameworks, process)
4. Converts qualified prospects via contact form or meeting booking (Calendly)
5. Provides pre-sale consultation to prepare the prospect for a discovery call

### 1.4 Success Criteria (First 3 Months)

| KPI                                      | Target           | Why It Matters                                                     |
| :--------------------------------------- | :--------------- | :----------------------------------------------------------------- |
| Lead response time                       | < 3 seconds      | Currently leads wait minutes/hours. Agent responds instantly.      |
| Conversations / day                      | 10+              | Agent is engaging real visitors at scale.                          |
| Botpress leads created / week            | 25–50            | Data flowing correctly from conversations to CRM.                 |
| Qualification rate                       | 30–50%           | Agent is collecting required data and scoring leads.              |
| Conversion rate (form + booking)         | 10–20%           | Qualified leads converting via contact form or meeting booking.   |
| False positive rate                      | < 15%            | Unqualified leads not reaching sales managers.                    |
| First closed deal from agent             | Within 3 months  | Validates end-to-end value of the agent.                          |

---

## 2. User Stories

### 2.1 Primary Persona: Website Visitor (Prospect)

**US-1** — As a visitor, I want to ask about services so I understand what the company does.
> **Acceptance:** Agent explains services using KB content. Responses are accurate and conversational. No hallucinated offerings.

**US-2** — As a visitor, I want to see relevant case studies so I can evaluate if the company is a good fit.
> **Acceptance:** Agent recommends case studies matching visitor's industry/need from KB. Links or descriptions provided.

**US-3** — As a visitor, I want to get answers to common questions so I don't have to wait for a sales call.
> **Acceptance:** Agent answers FAQ (pricing frameworks, process, timelines) from KB. Admits when it doesn't know.

**US-4** — As a visitor, I want to fill in a contact form with my questions so the sales team can reach me.
> **Acceptance:** Contact form is presented in chat when there is a knowledge gap or user rejects booking. Form captures name, email, company, message.

**US-5** — As a qualified visitor, I want to book a meeting with a sales manager directly.
> **Acceptance:** Calendly link/embed is presented after qualification. Visitor can book without leaving the chat flow.

**US-6** — As a returning visitor, I want the agent to remember our previous conversation.
> **Acceptance:** Agent recognizes returning visitors (via email or cookie). Previous qualification status and context are restored.

### 2.2 Secondary Persona: Sales Team Member

**US-8** — As a sales manager, I want qualified leads delivered to HubSpot/Calendly so I can follow up quickly.
> **Acceptance:** Leads from agent conversations appear in HubSpot/Calendly with qualification data, contact info, and conversation context.

**US-9** — As a sales manager, I want to see what the prospect discussed with the agent before my call.
> **Acceptance:** Conversation summary or link to full log is accessible from the lead record.

**US-10** — As a sales manager, I want the agent to not make promises I can't keep.
> **Acceptance:** Guardrails prevent the agent from guaranteeing timelines, ROI, SLAs, or exact pricing.

---

## 3. Functional Requirements

### 3.1 Conversation Flow Overview

The agent operates as a reactive chat widget on the website. All conversations follow this high-level flow:

> **GREETING → DISCOVERY → QUALIFICATION → RECOMMENDATION → CONVERSION**

1. **Greeting** — Welcome message. Identify visitor intent (learn about services / specific question / pricing / case studies).
2. **Discovery** — Ask about their business, challenges, goals. Explain relevant services. Surface matching case studies from KB.
3. **Qualification** — Collect qualification data points (budget, timeline, authority, need). Score lead as Hot / Warm / Nurture / DQ.
4. **Recommendation** — Based on qualification, recommend next step: book a meeting (Hot/Warm), provide resources (Nurture).
5. **Conversion** — Present contact form or Calendly booking link. Capture lead data. Create HubSpot contact.

### 3.2 Qualification Framework

The agent must collect the following data points during conversation. Not all are required — the agent adapts based on conversation context.

| Data Point                | Priority                  | How to Collect                                                          |
| :------------------------ | :------------------------ | :---------------------------------------------------------------------- |
| Company / Organization    | **Required**              | Ask directly or infer from context                                      |
| Business challenge / Need | **Required**              | Open-ended discovery questions                                          |
| Industry / Vertical       | Optional                  | Ask or infer from company name / description                            |
| Budget indication         | Optional                  | Ask about budget range or project scope. Use pricing frameworks from KB |
| Timeline                  | Optional                  | Ask when they want to start or if there's a deadline                    |
| Decision authority        | Optional                  | Ask about their role and decision-making process                        |
| Contact name              | **Required for conversion** | Collected via contact form or Calendly booking                        |
| Email                     | **Required for conversion** | Collected via contact form or Calendly booking                        |
| Phone                     | Optional                  | Offered on contact form, not required                                   |

### 3.3 Lead Scoring & Outcomes

**Hot**
- **Criteria:** Clear need + budget + timeline + authority. Strong fit.
- **Action:** Book meeting immediately.
- **Conversion path:** Calendly link presented in chat.

**Warm**
- **Criteria:** Need identified but missing budget/timeline/authority. Potential fit.
- **Action:** Book meeting immediately.
- **Conversion path:** Calendly link presented in chat.

**Nurture**
- **Criteria:** Early-stage interest. No immediate need or budget. Exploring options.
- **Action:** Provide resources.
- **Conversion path:** Offer to send case studies / subscribe to updates.

**DQ (Disqualified)**
- **Criteria:** No fit (wrong industry, no budget, outside service scope). Spam.
- **Action:** Politely disengage.
- **Conversion path:** Thank visitor. No conversion action.

### 3.4 Conversion Paths

#### 3.4.1 Contact Form (Knowledge Gap / Rejection to Book a Meeting)

When the user asks something outside the agent's knowledge OR rejects booking a meeting, the agent presents an in-chat contact form.

| Field           | Required | Notes                                                                             |
| :-------------- | :------- | :-------------------------------------------------------------------------------- |
| Full Name       | Yes      | Free text                                                                         |
| Email           | Yes      | Validated format                                                                  |
| Company         | Yes      | Free text. Pre-filled if collected during conversation.                           |
| Message / Notes | No       | Optional. Pre-filled with conversation summary/question from the user if available. |

#### 3.4.2 Meeting Booking (Hot / Warm Leads)

When full/partial qualification criteria are met, the agent presents a Calendly booking link. The visitor can book a discovery call with a sales manager directly from the chat.

| Aspect          | Details                                                           |
| :-------------- | :---------------------------------------------------------------- |
| **Integration** | Calendly link embedded or linked in chat message                  |
| **Trigger**     | Lead scored as Hot (need + budget + timeline + authority)          |
| **Fallback**    | If visitor declines booking, offer contact form instead           |
| **Data passed** | Visitor name, email, company (pre-filled if collected)            |

---

## 4. Knowledge Base & RAG

### 4.1 Content Sources (MVP)

The agent's knowledge comes exclusively from curated sources loaded into the Botpress Knowledge Base. The agent must never hallucinate or invent information not present in the KB.

| Source                    | Content Type                            | Notes                                   |
| :------------------------ | :-------------------------------------- | :-------------------------------------- |
| Internal knowledge base   | Company info, services, processes       | *Primary source of truth*               |
| Sales presentations       | Service descriptions, value propositions | *Primary source of truth*              |
| Case studies              | Client examples, results, testimonials  | *Matched to visitor's industry/need*    |
| FAQ                       | Common questions and answers            | *Sales-related FAQ*                     |
| Pricing frameworks        | Pricing ranges, models, factors         | *No exact quotes — frameworks only*     |
| ICP definitions           | Ideal customer profiles                 | *Used for qualification logic*          |
| Qualification criteria    | Scoring rules, thresholds               | *Informs lead categorization*           |
| Public reviews            | Clutch, DesignRush profiles             | *Social proof and credibility*          |

### 4.2 Source of Truth Hierarchy

1. **Internal knowledge base + Sales presentations** — primary
2. **Case studies + FAQ + Pricing frameworks** — supporting
3. **Public reviews** — social proof only

> *Website content is NOT a primary source — it may not be fully up to date.*

### 4.3 RAG Behavior Rules

- **Grounded responses** — Agent must only use information present in the KB. If the answer is not in the KB, the agent must say so and offer to connect the visitor with a sales manager.
- **Citation** — When referencing case studies or specific data, the agent should indicate the source (e.g., "We worked with a similar company in fintech...").
- **No hallucination** — The agent must never invent services, client names, pricing, or results not present in the KB.

---

## 5. Data Model

### 5.1 Conversation Variables (Botpress)

These variables are collected and stored during each conversation session in Botpress.

| Variable                 | Type    | Required |
| :----------------------- | :------ | :------- |
| `visitor_id`             | String  | Yes, Auto |
| `visitor_name`           | String  | No       |
| `visitor_email`          | String  | No       |
| `visitor_company`        | String  | No       |
| `visitor_phone`          | String  | No       |
| `visitor_industry`       | String  | No       |
| `business_need`          | String  | No       |
| `budget_indication`      | String  | No       |
| `timeline`               | String  | No       |
| `decision_authority`     | String  | No       |
| `lead_score`             | Enum    | Yes      |
| `conversion_action`      | Enum    | Yes      |
| `conversation_summary`   | String  | Auto     |
| `qualification_complete` | Boolean | Yes      |

**Variable descriptions:**

- `visitor_id` — Auto-generated from Botpress built-in userId. Persisted via browser cookie. Used to identify returning visitors.
- `visitor_name` — Visitor's name (collected via conversation or form).
- `visitor_email` — Email address (collected via form or Calendly).
- `visitor_company` — Company or organization name.
- `visitor_phone` — Phone number (optional on form).
- `visitor_industry` — Industry/vertical (inferred or stated).
- `business_need` — Primary challenge or need described by visitor.
- `budget_indication` — Budget range or "not discussed".
- `timeline` — When they want to start ("ASAP", "Q2", "exploring").
- `decision_authority` — Role in decision-making ("decision maker", "researcher").
- `lead_score` — Hot / Warm / Nurture / DQ.
- `conversion_action` — form_submitted / meeting_booked / resources_sent / none.
- `conversation_summary` — AI-generated summary of the conversation.
- `qualification_complete` — Whether minimum qualification data was collected.

### 5.2 Memory & Returning Visitors

The system must support returning visitor recognition. Stored per visitor (via Botpress Tables + user variables):

| Data                          | Details                                                     |
| :---------------------------- | :---------------------------------------------------------- |
| **Interaction history**       | Previous conversation summaries and outcomes                |
| **Qualification status**      | Last known lead score (Hot / Warm / Nurture / DQ)           |
| **Funnel stage**              | Where they are in the sales process                         |
| **Previously asked questions** | Topics already covered (avoid repetition)                  |
| **Visitor identity**          | Matched by email (primary) or browser cookie (fallback)     |

**Behavior:** If a returning visitor is recognized, the agent greets them by name (if known) and references their previous conversation. It does not re-ask questions already answered.

### 5.3 HubSpot Contact (Read for MVP)

> **MVP Scope:** HubSpot CRM write operations are **OUT of scope for MVP.**
>
> Contact creation in HubSpot will be handled via contact form submission (existing website form → HubSpot) or Calendly's native HubSpot sync, not via direct Botpress → HubSpot write integration.
>
> *Post-MVP (v2): Direct Botpress → HubSpot integration for contact creation, property mapping, lifecycle stage updates, and conversation log attachment.*

---

## 6. Boundaries, Guardrails & Persona

### 6.1 Agent Persona

| Aspect                 | Details                                                             |
| :--------------------- | :------------------------------------------------------------------ |
| **Role**               | AI sales assistant for pre-sales consultation                       |
| **Tone**               | Professional, friendly, consultative. Not pushy or overly casual.   |
| **Positioning**        | Sales augmentation — explicitly NOT a replacement for the sales team |
| **Name**               | TBD (configurable in Botpress Instructions)                         |
| **Knowledge boundary** | Only what's in the KB. Admits when it doesn't know.                 |

### 6.2 The Agent Must NOT

**Must NOT Do:**

- Provide exact pricing without context
- Guarantee outcomes or results
- Disclose confidential client information
- Reveal internal infrastructure or technical details
- Make up information not in the KB
- Discuss competitors by name
- Engage with off-topic or inappropriate requests

**Must NOT Promise:**

- Fixed timelines without discovery
- ROI or revenue growth guarantees
- Full replacement of the sales team
- SLA commitments without agreement
- Specific deliverables or scope without a call
- Discounts or special pricing

### 6.3 Edge Case Handling

| Scenario                        | How to Handle                                                                          |
| :------------------------------ | :------------------------------------------------------------------------------------- |
| **Spam / Irrelevant**           | Politely redirect. If persistent, disengage. Do not engage with profanity.             |
| **Competitor questions**        | Do not discuss competitors by name. Redirect to the company's strengths.               |
| **Off-topic (technical, support)** | Explain the agent's scope. Offer to connect with the right team.                    |
| **Pricing pressure**            | Provide pricing frameworks only. Guide toward a discovery call for specifics.          |
| **Returning but DQ'd visitor**  | Welcome back. Offer updated resources. Do not re-qualify aggressively.                 |
| **Agent doesn't know the answer** | Admit it clearly. Offer to connect visitor with a human.                            |

---

## 7. Non-Functional Requirements

| Requirement          | Specification                                                                                          |
| :------------------- | :----------------------------------------------------------------------------------------------------- |
| **Response time**    | < 3 seconds per message (target from Discovery Doc)                                                    |
| **Availability**     | 24/7 (Botpress Cloud SLA)                                                                              |
| **Expected load**    | 500–1,000 leads/month                                                                                  |
| **Channel**          | Website chat (reactive). Single channel for MVP.                                                       |
| **Browser support**  | Modern browsers (Chrome, Firefox, Safari, Edge). Mobile responsive.                                    |
| **Branding**         | Widget styled to match company brand (colors, logo). "Powered by Botpress" removed (Plus plan).        |
| **Language**         | English only                                                                                           |
| **Data privacy**     | GDPR compliant. SOC 2 certified (Botpress Cloud). No PII stored beyond what visitor provides.          |
| **Monitoring**       | Botpress analytics dashboard. Conversation logs accessible. Optional Langfuse integration.             |
| **Versioning**       | Every publish = new version in Botpress. One-click rollback.                                           |

---

## 8. Technical Architecture

### 8.1 Platform & Stack

| Component            | Details                                                                       |
| :------------------- | :---------------------------------------------------------------------------- |
| **Chat platform**    | Botpress Cloud (Plus plan recommended)                                        |
| **LLM models**       | OpenAI GPT-4o (qualification) + GPT-4o Mini (FAQ). Configurable per node.     |
| **Knowledge base**   | Botpress KB with vector DB (RAG)                                              |
| **Widget**           | Botpress Webchat (JavaScript embed on website)                                |
| **Meeting booking**  | Calendly (link in chat messages)                                              |
| **Contact form**     | Botpress in-chat form or redirect to website form                             |
| **CRM**              | HubSpot (read-only for MVP; write via Calendly/form sync)                     |
| **Testing**          | Botpress Emulator + batch testing script (Chat API + CSV)                     |
| **Observability**    | Botpress Analytics + optional Langfuse                                        |

### 8.2 Integration Architecture (MVP)

> **Visitor → Botpress Webchat → Qualification → Conversion Path:**

| Lead Category | Conversion Path                                         |
| :------------ | :------------------------------------------------------ |
| **Hot**       | Calendly link → Calendly syncs to HubSpot               |
| **Warm**      | Calendly link → Calendly syncs to HubSpot               |
| **Nurture**   | Resources / case studies sent in chat                    |
| **DQ**        | Polite disengage                                        |

---

## 9. Out of Scope (MVP)

The following items are explicitly excluded from the MVP. They may be considered for v2 based on MVP learnings.

- **CRM write operations (Botpress → HubSpot)** — HubSpot sync handled via Calendly + form. Direct write integration in v2.
- **Follow-up messages (email/chat)** — Requires email integration (SendGrid/MailGun). v2 feature.
- **Voice interaction** — Text chat only for MVP. Voice in v2 if demand validated.
- **Proactive chat (auto-trigger)** — MVP is reactive only. Proactive triggers (page time, exit intent) in v2.
- **Multi-channel (WhatsApp, Messenger)** — Website chat only. Additional channels in v2.
- **A/B testing of conversation flows** — Not natively supported in Botpress. Consider for v2 with Langfuse.
- **Advanced analytics / custom dashboards** — Botpress built-in analytics for MVP. Custom dashboards in v2.
- **CRM data read (enrich conversations)** — Discovery Doc lists as not in MVP scope. v2 feature.

---

## 10. Acceptance Criteria (MVP Go-Live)

The MVP is ready for production when ALL of the following are verified:

1. **Agent correctly qualifies leads into Hot/Warm/Nurture/DQ categories**
   - Verification: 50+ test conversations pass with correct categorization.
2. **Agent answers service/FAQ questions accurately from KB**
   - Verification: 10+ FAQ scenarios verified against KB content. No hallucination.
3. **Agent recommends relevant case studies based on visitor profile**
   - Verification: 5+ industry-matched case study recommendations verified.
4. **Contact form is presented for knowledge gap and rejection to book a meeting**
   - Verification: Form submission tested. Data received correctly.
5. **Calendly booking link is presented for Hot/Warm leads**
   - Verification: Booking flow tested end-to-end. Calendar invite confirmed.
6. **Agent respects all guardrails (no pricing, no guarantees, no confidential info)**
   - Verification: 10+ adversarial test scenarios pass.
7. **Returning visitors are recognized and context is restored**
   - Verification: 3+ returning visitor scenarios tested.
8. **Lead data reaches HubSpot via Calendly sync or form submission**
   - Verification: 5+ leads verified in HubSpot with correct data.
9. **Widget loads correctly on production website (desktop + mobile)**
   - Verification: Tested on Chrome, Safari, Firefox. Mobile responsive.
10. **Response time < 3 seconds under normal load**
    - Verification: Measured across 20+ conversations.
11. **Conversation logs are accessible in Botpress for review**
    - Verification: Sales team can access and search conversation history.

---

## 11. Risks & Mitigations

**Incorrect answers cost deals**
> Mitigation: KB-only responses. Guardrails enforced. Agent admits when it doesn't know. Regular KB review cycle.

**Agent hallucination**
> Mitigation: Botpress KB grounding + "limit to KB" setting. Batch testing catches gaps. Langfuse traces for debugging.

**Poor qualification accuracy**
> Mitigation: Iterative tuning based on sales team feedback. Batch test dataset grows over time. A/B qualification prompts.

**Low visitor engagement**
> Mitigation: Optimize greeting message. v2: add proactive triggers. Monitor bounce rate in analytics.

**Sales team doesn't trust agent output**
> Mitigation: Involve sales from day 1. Demo in testing phase. Frame as pilot. Share conversation logs.

**Botpress AI Spend exceeds $100/mo cap**
> Mitigation: Hybrid models (GPT-4o Mini for FAQ). Monitor in dashboard. Contact Botpress to raise cap if needed.

---

## 12. References

| Document                | Full Title                                                          |
| :---------------------- | :------------------------------------------------------------------ |
| **Discovery Document**  | AI Sales Agent — Discovery Document (Feb 2026)                      |
| **MVP Roadmap**         | AI Sales Agent — MVP Deployment Roadmap (Feb 16 → Mar 31, 2026)     |
| **Tool Comparison**     | AI Sales Agent — Comparison Table (6 tools, 11 criteria)            |
| **Scoring Matrix**      | AI Sales Agent — Tool Scoring Matrix (weighted scores)              |
| **Botpress Overview**   | Botpress Detailed Overview (platform analysis)                      |

---

*End of document. This PRD is based on the Discovery Document and decisions made during the MVP planning phase (Feb 2026). Updates to scope or requirements should be reflected in a new version of this PRD.*
