# Sales AI Agent — Botpress Build Summary

> Created: March 17, 2026 | Updated: April 29, 2026
> Status: MVP in progress
> Platform: Botpress Cloud Studio (no-code)
> Client: Halo Lab (B2B design agency)

---

## 1. What the Bot Does

The Sales AI Agent is a website chatbot that qualifies inbound visitors for Halo Lab through natural conversation. It uses the CHAMP framework (Challenges, Authority, Money, Prioritization) to evaluate leads and routes them to one of four outcomes:

- **Hot** → share Cal.com booking link
- **Warm** → share Cal.com booking link (with missing signals flagged)
- **Nurture** → share resources, attempt to upgrade
- **DQ** → polite close

Target sectors: SaaS, Fintech, Healthcare (B2B). Hard exclusions: Adult/18+ content, Russia-based companies. Budget floor: €5,000.

---

## 2. Model Configuration

| Slot | Model | Notes |
|------|-------|-------|
| Best Model | Claude Haiku 4.5 (standard) | Switched from GPT-4.1 (robotic responses, poor instruction-following) |
| Fast Model | Claude Haiku 4.5 (standard) | Reasoning Mode causes 400 errors (Botpress LLMz doesn't support extended thinking) |
| RAG Model | Claude Haiku 4.5 (standard) | — |
| Fallback | GPT-5 Mini | — |

---

## 3. Global Instructions (Agents)

Global instructions are split across two Agents that apply to every autonomous node:

**Personality Agent (v4)** — identity, tone (professional, friendly, consultative, not pushy), message rewriting rules (preserve intent, preserve questions, never modify visitor data). Service scope and knowledge boundary moved to node-level prompts.

**Policy Agent (v3)** — hard limits on what the bot must never do (provide exact pricing, guarantee outcomes, discuss competitors by name, fabricate URLs, modify visitor data, recommend external services) and never promise (fixed timelines, ROI guarantees, discounts, specific deliverables without a call, response times for the team).

**Knowledge Agent** — DISABLED globally. Caused duplicate answers when enabled. Individual nodes use Search Knowledge cards to pull from the KB instead.

**Summary Agent (v1)** — generates a pre-call brief for the sales manager. Strict rules: only visitor-stated info, never attribute bot responses as visitor data, never fabricate emails from name + company.

**Knowledge Base** — 13 documents loaded with YAML front-matter metadata (type + topic) covering company profile, service info, sales info, technical details, and case studies.

---

## 4. Main Flow

### Architecture

Start → Discovery (AN) → Scoring Engine (SN) → DQ Bridge / Nurture Bridge / Handoff Bridge

### Discovery Node

Single combined **Autonomous Node** covering the full discovery process:

- 2.1 Opening (REQUIRED)
- 2.2 Company Profile (REQUIRED)
- 2.3 Challenges (REQUIRED — CHAMP)
- 2.4 Timeline (REQUIRED — CHAMP)
- 2.5 Budget (REQUIRED — CHAMP)
- 2.6 Authority (REQUIRED — CHAMP)
- 2.7 Technical Context (optional — after all required topics)
- 2.8 Qualification Complete

**Prompt structure:** Topic-based (Conversation Style, Scope, Guardrails) — not step-based. Tool references inline in Scope section.

**Cards on this node:**
- Search Knowledge (Main KB)
- Conversation_LogsTable (Execute Table card — replaced Knowledge Gap Logger workflow)
- Execute Workflow: Handle Objections
- Execute Workflow: Edge Cases

**Early Nurture logic (handled inside Discovery node):**
If `ch_challenges` is set to `"unclear"` or `"negative"`, the agent uses the Main KB to share a service overview and expected outcomes verbally (one piece of information at a time, no links). It asks if the content resonated. If the visitor confirms → update `ch_challenges` to `"positive"` and continue to the next discovery question. If the visitor denies → `ch_challenges` stays as-is, don't push, ask if they have other questions and use the KB to answer them.

**Transition:**
- `workflow.discovery_complete === true` → Scoring Engine (Standard Node)

**DQ triggers (inside Discovery):**
- Budget explicitly < €5,000: set `m_money = "negative"`, `discovery_complete = true`, provide `salesai@halo-lab.team`, close gracefully → transition to Scoring Engine
- ICP exclusion (Adult/18+ or Russia-based): set `icp_exclusion_flag = true`, `discovery_complete = true`, close gracefully → transition to Scoring Engine

**CHAMP signal defaults:** All set to `"none"` in Botpress variable configuration.

### Scoring Engine

**Type:** Standard Node (used in both Discovery and Nurture flows)

**Cards:**
- AI Task — fills in any missed CHAMP variables from conversation context
- Execute Code — sets `lead_score` (unchanged scoring rules)
- AI Transition — routes based on `lead_score`

**Scoring rules:**
- All 4 positive → Hot
- CH positive + 1–2 of A/M/P positive (at least 1) → Warm
- CH negative/unclear, OR CH positive but 0 of A/M/P positive → Nurture
- No need / wrong scope / spam → DQ

**AI Transition routes to:**
- DQ → DQ Bridge (Execute Code card sets `conversation_stage = "dq_closed"`) → End
- Nurture → Nurture Bridge
- Hot/Warm → Handoff Bridge

---

## 5. Handoff Path (Hot/Warm)

### Flow

Handoff Bridge → Handoff AN → Standard Node (AI Transition) → DQ Bridge + End / Exit

### Node Details

**Handoff (Autonomous Node):**
The LLM generates a natural qualification summary message — confident framing for Hot, exploratory for Warm. Never announces the score or mentions CHAMP. Then presents the Cal.com event link.

Cards:
- Search Knowledge (Main KB)
- Conversation_LogsTable (Execute Table card — replaced Knowledge Gap Logger workflow)
- Execute Workflow: Edge Cases
- Execute Workflow: Handle Objections

**Transition:** `workflow.handoff_completed === true` → Standard Node with AI Transition

**AI Transition routes based on `conversation.lead_score`:**
- DQ → DQ Bridge → End
- Hot/Warm → Exit

**Outcomes after presenting Cal.com link:**

| Scenario | What happens | conversion_action |
|----------|-------------|-------------------|
| Visitor confirms they will book | Bot confirms warmly, sets handoff_completed | `"booking_link_shared"` |
| Visitor asks for alternative | Bot offers contact form ("Can I take your details?") | `"form_submitted"` if accepted |
| Visitor declines everything | Bot provides salesai@halo-lab.team, closes positively | `"none"` |

**Important:** The bot shares the Cal.com link but does NOT book the meeting itself. It cannot confirm whether the visitor actually completed the booking. `conversion_action` is set to `"booking_link_shared"` (not `"meeting_booked"`).

### Pre-Call Brief — Currently Not Active

The pre-call brief was designed to send a structured summary to the sales team via Email Notifier when a meeting was booked. Since the bot now only shares a Cal.com link and cannot confirm whether the visitor actually booked, there is no reliable trigger for the pre-call brief. This is a known gap for post-MVP.

---

## 6. Nurture Workflow

A separate sub-workflow called from the Main Flow when the Scoring Engine routes a lead as Nurture (CH positive but A/M/P signals are unclear or negative).

### Node Structure

Two **Autonomous Nodes** with a **Scoring Engine** (Standard Node) between them.

**Node 1: Nurture + Requalify (AN)** → Scoring Engine (SN) → Node 2: Nurture Close (AN) or Handoff Bridge or DQ Bridge

### Node 1 Cards (N1–N2)

- Search Knowledge (Main KB)
- Conversation_LogsTable (Execute Table card — replaced Knowledge Gap Logger workflow)
- Execute Workflow: Edge Cases
- Execute Workflow: Handle Objections

### Node 1 Steps

**N1 — Ask What Matters:**
Identifies which CHAMP signals (A, M, P only — CH is always positive) are unclear or negative. Offers the visitor topic choices based on their weak signals (timeline/pricing/talking points for manager). Sets `nurture_stage = "N1_topics_offered"`.

**N2 — Address + Requalify:**
Shares KB content on the visitor's chosen topic conversationally. Asks: "What would need to change on your side for this to become something you'd want to move forward on?"

Three scenarios:
- **Factual question (no signal change):** Answer from KB, re-ask requalification question.
- **No signal change:** Acknowledge with one sentence (no email, no closing, no next steps). Set `nurture_requalified = true`.
- **Improved signals:** Update CHAMP variables, acknowledge progress in one sentence (no email, no booking link). Set `nurture_requalified = true`.

### Node 1 Transition

`workflow.nurture_requalified === true` → Scoring Engine (same Standard Node as Discovery)

### Scoring Engine (between Node 1 and Node 2)

AI Task fills missed variables → Execute Code sets `lead_score` → AI Transition routes:
- DQ → DQ Bridge → End
- Hot/Warm → Handoff Bridge
- Nurture (signals did not improve) → Node 2 (N3 Nudge + Close)

### Node 2: Nurture Close (AN)

**N3 — Nudge + Close:**
Offers to collect visitor details: "Would it help if I took your details so our team can follow up when the timing is better?"

- If accepted: confirm already-captured details, ask only for missing (email). Set `conversion_action = "form_submitted"`.
- If declined: acknowledge warmly. Set `conversion_action = "none"`.

Set `nurture_stage = "N3_closed"`, `workflow.n3_closed = true`.

### Node 2 Cards

- Search Knowledge (Main KB)
- Conversation_LogsTable (Execute Table card)
- Execute Workflow: Edge Cases
- Execute Workflow: Handle Objections

### Node 2 Transition

`workflow.n3_closed === true` → Exit

---

## 7. Objection Handling (UC3)

Implemented as a separate sub-workflow called via Execute Workflow cards from Discovery, Nurture, Handoff, and Edge Cases nodes.

### Trigger

Visitor raises a concern about pricing, timing, competitors, scope, trust, authority, process, or anything signaling hesitation.

### Pattern: Check → Close

After addressing any objection, ask if that addresses the concern. If confirmed → brief sentence, set `objection_addressed = true`, exit. If pushback → address with different KB content, ask again. If visitor signals readiness to move forward → exit immediately.

### Cards

- Search Knowledge (Main KB)
- Conversation_LogsTable (Execute Table card — knowledge gap logs)
- Execute Workflow: Edge Cases
- Conversation_LogsTable (Execute Table card — unidentified objection logs)

### Transition

`workflow.objection_addressed === true` → Standard Node with AI Transition based on `conversation.lead_score`:
- DQ → DQ Bridge → End
- All other scores → Exit (return to caller)

### By Objection Type

| Type | Agent behavior | Hard limits |
|------|---------------|-------------|
| Pricing | Share pricing framework from KB, suggest phased approach. Save budget if revealed, evaluate M signal | NEVER provide exact pricing or promise discounts |
| Timing | Note the concern, re-check P signal, suggest phased approach | NEVER promise fixed timelines or SLA commitments |
| Competitor | NEVER repeat competitor names — replace with "other tools"/"other solutions". Redirect to strengths from KB | NEVER discuss competitors by name |
| Scope | Clarify needs, refine CH signals. If scope doesn't match → set DQ + wrong_scope | NEVER promise specific deliverables |
| Trust | Provide case studies and social proof from KB | NEVER guarantee outcomes, ROI, or sales team replacement |
| Authority | Ask who else is involved, evaluate A signal, offer shareable materials | — |
| Process | Acknowledge preference, ask how they evaluate. Offer options or route to "skip to human" edge case | — |
| Unidentified | Log objection to Conversation_LogsTable, address from KB or acknowledge gap, provide salesai@halo-lab.team | — |

---

## 8. Knowledge Gap Handling

**No longer a separate workflow.** Knowledge gap logging is now handled via Conversation_LogsTable Execute Table cards directly on each Autonomous Node.

### Trigger

All three conditions must be true:
1. Search Knowledge was called in this turn
2. KB returned no answer, incomplete answer, or content that doesn't address the question
3. The bot already sent a response acknowledging the gap

### Data Written to Conversation_LogsTable

- `visitor_id`: {{event.userId}}
- `knowledge_gap_question`: the visitor's question (quoted as closely as possible)
- `unidentified_objections`: empty string ""

Knowledge gaps do NOT change lead score. Qualification continues after the gap is handled.

## 8b. Edge Cases Workflow

Implemented as a separate sub-workflow called via Execute Workflow cards from Discovery, Nurture, Handoff, and Handle Objections nodes.

### Trigger

Spam/abuse, off-topic/gibberish, request to skip to human or book a call, prior conversation reference, sensitive data shared, out-of-scope request (services outside Sales AI Agent).

### Cards

- Search Knowledge (Main KB)
- Conversation_LogsTable (Execute Table card — knowledge gap logs)
- Execute Workflow: Handle Objections

### Transition

`workflow.edge_case_handled === true` → Standard Node with AI Transition:
- `workflow.edge_case_soft_close === true` → End
- `workflow.edge_case_soft_close === false` → Exit (return to caller)

### Already DQ'd Visitors

If `conversation.lead_score` is already "DQ" when entering this node, always set `edge_case_soft_close = true` after handling — do not return to the caller.

---

## 9. Variables

### User Scope (persist across sessions)

`conversation_stage`, `icp_exclusion_flag`, `previous_lead_score`, `qualification_complete`, `visitor_company`, `visitor_email`, `visitor_industry`, `visitor_location`, `visitor_name`, `visitor_role`

### Conversation Scope (single session)

**CHAMP signals:** `ch_challenges`, `a_authority`, `m_money`, `p_prioritization` (defaults = `"none"`)

**Discovery data:** `budget_indication`, `current_chat_tools`, `current_crm`, `decision_authority`, `expected_volume`, `integrations_needed`, `leads_per_month`, `other_stakeholders`, `pain_points`, `timeline`, `trigger_event`, `use_case`, `visitor_team_size`, `website_platform`

**Scoring & qualification:** `champ_positive_count`, `lead_score`, `lead_score_reason`, `conversion_action`, `booking_fallback_used`

**Nurture tracking:** `nurture_stage`, `nurture_upgraded_to`, `resources_shared`

**Knowledge gaps & objections:** `knowledge_gap_triggered`, `knowledge_gap_question`, `contact_form_question`, `unidentified_objections`

**Other:** `pre_call_brief_sent`

### Workflow Scope (scoped to individual sub-workflows/flows)

| Variable | Flow/Workflow | Purpose |
|----------|---------------|---------|
| `nurture_exit` | Main Flow | Tracks how/why the visitor exited the nurture path |
| `dq_message` | DQ Close | Stores the differentiated DQ close message |
| `handoff_authorized` | Handoff | Confirms the lead is authorized for handoff (entry guard check) |

---

## 10. Tables

| Table | Columns | Purpose |
|-------|---------|---------|
| LeadsTable | 16 | One row per qualified conversation. CHAMP signal columns removed — signals stay in conversation variables only |
| Conversation_LogsTable | 5 | Knowledge gaps and objections logged in real-time |
| ContactFormTable | 6 | Contact form submissions. `visitor_email` required |

### Write Strategy

- **Real-time writes:** Knowledge Gap Logger + Handle Objections workflows write to Conversation_LogsTable immediately
- **Batch upsert:** LeadsTable + ContactFormTable are written via Insert Record cards in the Before Conversation Ends trigger

---

## 11. Integrations

| Integration | Status | Purpose |
|-------------|--------|---------|
| Cal.com | Active | Event link display. Bot shows the link, visitor books themselves. Bot does NOT create the meeting |
| Email Notifier | Inactive | Was designed for pre-call brief delivery to salesai@halo-lab.team. No trigger available since bot can't confirm Cal.com bookings |
| Google Calendar | Rejected | Previously configured via GCP service account. Removed |
| Calendly | Blocked | Installed but unusable due to free-plan API limitations |

---

## 12. Edge Flow Handling

**Before Conversation Ends:** Triggers LeadsTable upsert (Insert Record cards) + ContactFormTable write.

**Error Flow:** Same table writes + error notification email to salesai@halo-lab.team.

**Timeout Flow:** Soft close message → table writes via Before Conversation Ends.

**UC4 Returning Visitor:** Dropped for MVP. Botpress Webchat with localStorage enabled (90 days) automatically restores previous conversations. Full v1 spec preserved in the repo for post-MVP evaluation.

---

## 13. Architecture Decisions

- **Option B (Scored Flow):** Bot automatically evaluates CHAMP signals and routes leads (not manual scoring by sales team)
- **Global instructions split:** Personality Agent (behavior) / Policy Agent (hard limits)
- **Transition routing:** AN → SN (AI Transition) pattern across all flows. AN sets a workflow boolean, SN routes based on lead_score or soft_close
- **Bridge Standard Nodes:** Connect autonomous node transitions to sub-workflows (DQ Bridge, Nurture Bridge, Handoff Bridge)
- **Single Discovery node:** Early + Deep Discovery combined to eliminate double-message UX and transition failures
- **Early Nurture absorbed into Discovery:** No separate Nurture Bridge — handled inline within the Discovery node prompt
- **Prompt structure:** Topic-based (Conversation Style, Scope, Guardrails) — "When to Use Tools" section removed, tool references inline in Scope
- **Knowledge Gap Logger workflow replaced:** All nodes now use Conversation_LogsTable Execute Table cards directly instead of a separate UC2 workflow
- **Nurture split into 2 ANs:** Node 1 (N1-N2 nurture + requalify) → Scoring Engine → Node 2 (N3 nudge + close). Scoring engine shared with Discovery
- **Consistent Data Collection guardrails:** All nodes use the same 4-step process (identify → save → tools → respond)
- **Consistent DQ triggers:** All nodes handle budget DQ and ICP exclusion inline with graceful close

---

## 14. Known Issues & Bugs

| Issue | Status | Details |
|-------|--------|---------|
| Nurture `nurture_stage` variable bug | Likely resolved | Was skipping N2/N3 in single-node setup. Nurture now split into 2 ANs with scoring engine between — needs re-testing |
| Pre-call brief not active | By design | Can't trigger without booking confirmation from Cal.com |
| Publish Failed error (variable description limit) | Resolved | Root cause: `lead_score` description exceeded Botpress 256-char limit |
| CHAMP signals defaulting to "unclear" | Resolved | Fixed by setting variable defaults to `"none"` in Botpress |
| Google Calendar Create Event failing | Resolved (removed) | Was due to missing attendees field; integration replaced by Cal.com |
| Execute Code crashing silently | Resolved | Optional chaining (`?.`) not supported in Botpress JS — replaced with `&&` checks |
| Claude Haiku 4.5 Reasoning Mode 400 errors | Resolved | LLMz engine doesn't support extended thinking protocol — use standard mode |

---

## 15. Key Learnings

- **Emulator vs. Publish:** Emulator does not enforce all server-side validation (e.g., variable description length limits). Always test publish separately.
- **Knowledge Agent:** Even with Knowledge Agent enabled globally, autonomous nodes skip KB retrieval unless a Search Knowledge card is explicitly added to that node.
- **Botpress JS limitations:** Optional chaining (`?.`) and template literals not reliably supported — use `&&` checks and string concatenation.
- **Variable description limits:** Botpress enforces 256-char limit at publish time (not in Emulator).
- **localStorage behavior:** Botpress Webchat with localStorage enabled automatically restores previous conversations for returning visitors on the same browser/device.
- **Policy Agent rule:** Never generate or fabricate URLs — KB contains no links, and hallucinated URLs were observed in testing.
- **Routing logic placement:** Belongs in transition cards, not LLM prompts (except where the LLM needs to understand outcomes to set correct variables).
