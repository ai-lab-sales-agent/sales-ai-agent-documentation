# Sales AI Agent — Botpress Build Summary

> Created: March 17, 2026
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

**Personality Agent** — identity, tone (professional, friendly, consultative, not pushy), service scope, knowledge boundary, data collection rules, objection handling patterns, conversation continuity, and global DQ triggers (ICP exclusions + budget floor).

**Policy Agent** — hard limits on what the bot must never do (provide exact pricing, guarantee outcomes, discuss competitors by name, fabricate URLs) and never promise (fixed timelines, ROI guarantees, discounts, specific deliverables without a call).

**Knowledge Agent** — DISABLED globally. Caused duplicate answers when enabled. Individual nodes use Search Knowledge cards to pull from the KB instead.

**Summary Agent** — generates a conversation summary (used in conversation context).

**Knowledge Base** — 8 documents loaded covering services, case studies, pricing frameworks, FAQs, and objection responses.

---

## 4. Main Flow

### Architecture

Start → Discovery → Scoring Engine → Handoff / Nurture / DQ

### Discovery Node

Single combined **Autonomous Node** covering Steps 1–9 of the discovery process:

- Steps 1–2: Greeting, introduction
- Step 3: Company info, role, industry, location (ICP check here)
- Steps 4–5: Use case, pain points (CH signal evaluation)
- Steps 6–7: Lead volume, tech stack
- Step 8: Timeline, trigger events
- Step 9: Budget, decision authority

**Prompt structure:** Topic-based (Conversation Style, Scope, Guardrails, When to Use Tools) — not step-based.

**Cards on this node:**
- Search Knowledge (Main KB)
- Execute Workflow: Knowledge Gap Logger (UC2)
- Execute Workflow: Handle Objections (UC3)

**Early Nurture logic (handled inside Discovery node):**
If `ch_challenges` is set to `"unclear"` or `"negative"`, the agent uses the Main KB to share a service overview and expected outcomes verbally (one piece of information at a time, no links). It asks if the content resonated. If the visitor confirms → update `ch_challenges` to `"positive"` and continue to the next discovery question. If the visitor denies → `ch_challenges` stays as-is, don't push, ask if they have other questions and use the KB to answer them. There is no separate Nurture Bridge or Early Nurture transition for this.

**Transition conditions:**
- `icp_exclusion_flag = true` → DQ_Bridge
- `m_money = "negative"` (budget explicitly below €5K) → DQ_Bridge
- `qualification_complete = true` → Scoring_Engine

**CHAMP signal defaults:** All set to `"none"` in Botpress variable configuration.

### Scoring Engine

**Type:** Standard Node

**Cards:**
- Execute Code — counts positive CHAMP signals, assigns `lead_score` and `lead_score_reason`, sets `champ_positive_count` and `qualification_complete = true`
- AI Transition — routes to Hot, Warm, Nurture, or DQ

**Scoring rules:**
- All 4 positive → Hot
- CH positive + 1–2 of A/M/P positive (at least 1) → Warm
- CH negative/unclear, OR CH positive but 0 of A/M/P positive → Nurture
- No need / wrong scope / spam → DQ

### DQ Close

**Type:** Standard Node with variable-based differentiated messages.

- ICP exclusion (`icp_exclusion_flag = true`): "Thanks for reaching out — this isn't something we're able to help with."
- All other DQ: "Thanks for reaching out — based on what you've described, this service may not be the right fit right now."

Sets `lead_score = "DQ"`, `lead_score_reason`, `conversation_stage = "dq_closed"`, `conversion_action = "none"`.

---

## 5. Handoff Path (Hot/Warm)

### Flow

Entry Guard → Booking (Autonomous) → Completed_Lead_Converted → Handoff_Bridge → End

### Node Details

**Entry Guard (Standard Node):**
Verifies `lead_score` is "Hot" or "Warm". If not, redirects to Nurture or DQ Close.

**Booking (Autonomous Node):**
The LLM generates a natural qualification summary message — confident framing for Hot, exploratory for Warm. Never announces the score or mentions CHAMP. Then presents the Cal.com event link. Collects `visitor_email` if not already captured.

Cards:
- Search Knowledge (Main KB)
- Execute Workflow: Knowledge Gap Logger (UC2)
- Execute Workflow: Handle Objections (UC3)

**Outcomes after presenting Cal.com link:**

| Scenario | What happens | conversion_action |
|----------|-------------|-------------------|
| Visitor clicks the link | Bot confirms, moves to Completed | `"booking_link_shared"` |
| Visitor declines booking | Bot offers contact form ("Can I take your details?") | `"form_submitted"` if accepted |
| Visitor declines everything | Bot provides salesai@halo-lab.team, closes positively | `"none"` |

**Important:** The bot shares the Cal.com link but does NOT book the meeting itself. It cannot confirm whether the visitor actually completed the booking. `conversion_action` is set to `"booking_link_shared"` (not `"meeting_booked"`).

**Completed_Lead_Converted (Standard Node):**
Sets `conversation_stage = "completed"`. No pre-call brief is triggered (since the bot can't confirm booking completion).

**Handoff_Bridge → End.**

### Pre-Call Brief — Currently Not Active

The pre-call brief was designed to send a structured summary to the sales team via Email Notifier when a meeting was booked. Since the bot now only shares a Cal.com link and cannot confirm whether the visitor actually booked, there is no reliable trigger for the pre-call brief. This is a known gap for post-MVP.

---

## 6. Nurture Workflow

A separate sub-workflow called from the Main Flow when the Scoring Engine routes a lead as Nurture (standard entry — after full discovery, CH positive but none of A/M/P confirmed).

### Node Structure

Single **Autonomous Node** covering steps N1–N5.

### Cards

- Search Knowledge (Query KB) — proactive case study retrieval at N1 + reactive visitor questions
- Execute Workflow: Knowledge Gap Logger (UC2)
- Execute Workflow: Handle Objections (UC3)

### Transitions

- `m_money = "negative"` → DQ Close (with `lead_score_reason = "insufficient_budget"`)
- `nurture_stage = "N4_upgraded"` AND `qualification_complete = false` → Exit to Deep Discovery (Step 6)
- `nurture_stage = "N4_upgraded"` AND `qualification_complete = true` → Hot/Warm Handoff workflow

### Entry Logic

If `nurture_stage` is already set (not null), skip N1 and start from N2. This handles returning visitors who already received resources.

### Steps

**N1 — Share Resources:**
Agent pulls 1–2 case studies from KB relevant to visitor's industry/use case. If no close match, shares the most related and is transparent. Saves `resources_shared` (JSON array). Sets `nurture_stage = "N1_resources_shared"`.

**N2 — Check In:**
Asks if the resources resonated. Sets `nurture_stage = "N2_checked_in"`.

**N3 — Re-qualification (CHAMP Revisit):**
Asks what would need to change for them to move forward. Updates CHAMP signals that become clearer. Sets `nurture_stage = "N3_requalified"`.

**N4 — Evaluate Upgrade:**

| Condition | Action |
|-----------|--------|
| Signals improved AND `qualification_complete = true` | Set `nurture_upgraded_to` ("Warm"/"Hot"), update `lead_score`, set `nurture_stage = "N4_upgraded"` → redirect to Handoff |
| CH became positive AND `qualification_complete = false` | Set `nurture_stage = "N4_upgraded"`, `conversation_stage = "discovery_volume"` → redirect to Deep Discovery (Step 6) |
| Signals did NOT improve | Soft contact form nudge → if accepted: `nurture_stage = "N4_nudged"`, `conversion_action = "form_submitted"`. If declined → N5 |

**N5 — Warm Close:**
Positive close message. Sets `nurture_stage = "N5_warm_closed"`, `conversion_action = "resources_sent"`.

### Variables

The Nurture node has read+write access to all CHAMP signals, `lead_score`, `nurture_stage`, `nurture_upgraded_to`, `resources_shared`, `conversion_action`, and `conversation_stage`. At N3, the LLM re-evaluates CHAMP signals from conversation context. For Standard Nurture upgrades, the LLM sets `nurture_upgraded_to` AND `lead_score` directly.

### Known Bug

`nurture_stage` variable gets stuck at `N1` and can jump to `N4`, skipping N2 (check-in) and N3 (re-qualification). Under investigation.

---

## 7. Objection Handling (UC3)

Implemented as a separate sub-workflow called via Execute Workflow cards from Discovery, Nurture, and Handoff Booking nodes.

### Trigger

Visitor raises a concern about pricing, timing, competitors, scope, trust, authority, or anything signaling hesitation.

### Key Principle

Objections do NOT change routing or lead score. The agent handles them inline and resumes the current flow.

### Handling Sequence

1. **Acknowledge** — validate the concern without dismissing it
2. **Address** — use KB content (case studies, pricing frameworks, social proof)
3. **Reframe** — redirect to value proposition or suggest phased approach
4. **Resume** — return to wherever the conversation was

### By Objection Type

| Type | Agent behavior | Hard limits |
|------|---------------|-------------|
| Pricing | Share pricing framework from KB, suggest phased approach | NEVER provide exact pricing or promise discounts |
| Timing | Note the concern, re-check P signal, suggest phased approach | NEVER promise fixed timelines or SLA commitments |
| Competitor | Redirect to Halo Lab's strengths, case studies, client results | NEVER discuss competitors by name |
| Scope | Clarify needs, refine CH signals. If scope doesn't match → DQ | NEVER promise specific deliverables |
| Trust | Provide case studies and social proof from KB | NEVER guarantee outcomes, ROI, or sales team replacement |
| Unidentified | Log objection to `unidentified_objections` array, provide salesai@halo-lab.team | — |
| Repeated | Acknowledge concern is significant, suggest direct conversation with sales team | Provide contact email |

---

## 8. Knowledge Gap Handling (UC2)

Implemented as a separate sub-workflow called via Execute Workflow cards from Discovery, Nurture, and Handoff Booking nodes.

### Trigger

Visitor asks a question that the KB cannot fully answer (partial or no answer).

### Handling Sequence

1. Check KB first
2. If fully answerable → answer from KB
3. If partially answerable → answer what's available, note the gap, suggest a call for more detail
4. If not answerable → acknowledge honestly, suggest alternative topic, provide salesai@halo-lab.team for human follow-up

### Data Written

- `knowledge_gap_triggered = true`
- `knowledge_gap_question` — the specific question
- `contact_form_question` — same question (for table / sales follow-up)

Knowledge gaps do NOT change lead score. Qualification continues after the gap is handled.

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
- **Transition routing:** Lives in cards, not LLM prompts
- **Guard instructions:** Live in Global Instructions (agents), not individual node prompts
- **Bridge Standard Nodes:** Connect autonomous node transitions to sub-workflows
- **Single Discovery node:** Early + Deep Discovery combined to eliminate double-message UX and transition failures
- **Early Nurture absorbed into Discovery:** No separate Nurture Bridge — handled inline within the Discovery node prompt
- **Prompt structure:** Topic-based (Conversation Style, Scope, Guardrails, When to Use Tools) preferred over step-based
- **`nurture_entry` variable skipped:** `qualification_complete` handles routing instead

---

## 14. Known Issues & Bugs

| Issue | Status | Details |
|-------|--------|---------|
| Nurture `nurture_stage` variable bug | Open | Stops at N1, can jump to N4 skipping N2/N3 |
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
