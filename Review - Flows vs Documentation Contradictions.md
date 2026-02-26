# Review: Flows vs Documentation Contradictions

> Cross-reference of the visual flow diagrams (Decision Tree v2) against all repository documentation.
> Scope: PRD, Qualification Framework v2, Handoff Rules, Data Points Schema v2, Decision Tree v2.
> Excluded: Archive folder.

---

## Contradictions Found

### 1. Calendly Trigger — Hot Only vs. Hot + Warm (HIGH)

**Flows show:** Both Hot AND Warm leads receive a Calendly link (tagged differently).

**PRD contradicts (Section 3.4.2):**
> Trigger: Lead scored as Hot (need + budget + timeline + authority)

The PRD only mentions Hot leads as the Calendly trigger. Warm leads are not mentioned for meeting booking. The Qualification Framework, Handoff Rules, and Decision Tree all consistently show both Hot and Warm getting Calendly. The PRD is the outlier.

**Impact:** Affects core routing logic. An implementation based on the PRD alone would exclude Warm leads from Calendly booking.

---

### 2. Knowledge Gap Path — Contact Form vs. Contact Email (HIGH)

**Flows show:** Knowledge gaps → acknowledge gap → offer alternative → provide **contact email** → continue discovery.

**PRD contradicts (US-4 + Section 3.4.1):**
> "When the user asks something outside the agent's knowledge OR rejects booking a meeting, the agent presents an in-chat contact form."

The PRD bundles knowledge gaps and Calendly declines together into the same contact form path. The Qualification Framework and Handoff Rules explicitly separate these: **contact email** for knowledge gaps (lower friction, doesn't interrupt discovery), **contact form** only for Calendly declines. The Handoff Rules provides detailed rationale for this distinction.

**Impact:** Affects UX design and implementation. Using a form for knowledge gaps would interrupt the conversation flow unnecessarily.

---

### 3. Nurture — Dead-End vs. N1–N5 Upgrade Path (HIGH)

**Flows show:** Nurture is a multi-step upgrade funnel: N1 (resources) → N2 (check-in) → N3 (re-qualification) → N4 (upgrade or soft nudge) → N5 (warm close). Nurture leads can upgrade to Warm or Hot.

**PRD contradicts (Section 3.3):**
> Nurture: "Action: Provide resources. Conversion path: Offer to send case studies / subscribe to updates."

The PRD treats Nurture as a one-shot action (send resources, done). The Qualification Framework explicitly acknowledges this difference: *"Not a dead end as in the current PRD."*

**Impact:** Completely different flow architecture. The N1–N5 upgrade path is a core feature of the detailed design that the PRD doesn't reflect.

---

### 4. `m_money` Enum — "unclear" Value is Unreachable (MEDIUM)

**Flows show (Decision Tree Step 9):** Budget has 3 outcomes:
- ≥ €5,000 → M: POSITIVE
- < €5,000 → M: NEGATIVE (DQ trigger)
- "Not sure" / won't say → M: NEGATIVE

**Data Schema contradicts:** The `m_money` field has 3 enum values: `positive`, `negative`, `unclear`. But per the Decision Tree and the Data Schema's own description ("NEGATIVE: below €5,000 (triggers DQ) or undefined"), "undefined" budget maps to NEGATIVE, not "unclear". This makes the `unclear` enum value unreachable for `m_money`.

A NEGATIVE `m_money` means two very different things (explicit <€5k = DQ, vs. undefined = still qualifiable) but the schema cannot distinguish between them.

**Impact:** Implementation confusion. Developer may not know when to use "unclear" vs. "negative" for money. Scoring logic could misfire.

---

### 5. Nurture Criterion — CH Confirmed + All A/M/P Missing (MEDIUM)

**Flows show (Decision Tree CHAMP Quick Reference):**
> NURTURE: weak/vague — M/P not established
> **(or CH confirmed + all 3 of A/M/P missing)**

**Qualification Framework and Handoff Rules don't cover this case explicitly.** QF defines Nurture as: "Challenges vague or early-stage. Money/timeline not established." Handoff Rules similarly: "CH weak/vague, or M/P not established."

Neither doc addresses the case where Challenges ARE confirmed but all 3 of Authority/Money/Prioritization are missing. The Decision Tree adds this as a parenthetical. It's logically correct (doesn't qualify as Warm, which requires at least 1 of A/M/P confirmed), but the other docs leave it implicit.

**Impact:** Scoring edge case that could be implemented differently depending on which doc the developer follows.

---

### 6. Calendly Availability Fallback — Missing from Flows (MEDIUM)

**Handoff Rules detail:** If Calendly returns no available slots within the next 5 business days, the agent offers the contact form instead and sets `calendly_fallback_used = true`.

**Decision Tree (flows) don't include this scenario.** The flows show Calendly-decline fallback (visitor says no → contact form) but have no branch for "Calendly has no slots."

**Impact:** A developer building from the Decision Tree flows alone would miss this fallback, creating a dead-end when Calendly has no availability.

---

### 7. Warm Lead Definition — Ambiguous in PRD (MEDIUM)

**Flows show:** Warm = CH positive + 1–2 of A/M/P missing (at least 1 of A/M/P confirmed).

**PRD contradicts (Section 3.3):**
> Warm: "Need identified but missing budget/timeline/authority. Potential fit."

The PRD wording "missing budget/timeline/authority" could be interpreted as ALL THREE are missing, which would actually be Nurture (not Warm) per the detailed design. The QF is explicit: at least 1 of A/M/P must be confirmed.

**Impact:** Could lead to incorrect Warm/Nurture boundary in implementation if PRD is used as reference.

---

### 8. `visitor_phone` — Silently Dropped (LOW)

**PRD (Section 5.1):** Lists `visitor_phone` as a String, Optional conversation variable.

**Data Schema v2:** Does not include `visitor_phone` at all — not in Conversation Variables, Botpress Table, or Contact Form schema. The field removal is not documented.

**Impact:** Minor. Phone is collected via Calendly anyway. But the undocumented removal could cause confusion.

---

### 9. Scoring Matrix — Incomplete Category Representation (LOW)

**Data Schema Scoring Logic Summary table:** The Nurture row shows CH as "weak/vague" only. This doesn't represent the case from finding #5 (CH confirmed + all A/M/P missing). The table shows one representative row per category rather than all valid combinations.

**Impact:** Developer using the scoring matrix as a lookup table would miss a valid Nurture combination.

---

### 10. Unresponsive Visitor Language — Proactive vs. Reactive (LOW)

**Decision Tree:** Agent says "...or I can have our team reach out" (implies proactive outreach by the team).

**Handoff Rules:** Simply says "offer contact email" (reactive — visitor must initiate).

**Impact:** Minor wording difference but could affect how the feature is built (proactive callback vs. passive email).

---

## Summary

| # | Contradiction | Documents | Severity |
|---|---|---|---|
| 1 | Calendly trigger: Hot-only vs. Hot+Warm | PRD ↔ QF/Handoff/DT | **High** |
| 2 | Knowledge gap: form vs. email | PRD ↔ QF/Handoff/DT | **High** |
| 3 | Nurture: dead-end vs. N1–N5 upgrade | PRD ↔ QF/Handoff/DT | **High** |
| 4 | m_money "unclear" unreachable | Data Schema ↔ DT | **Medium** |
| 5 | Missing explicit Nurture criterion | DT ↔ QF/Handoff | **Medium** |
| 6 | Calendly no-slots fallback missing from flows | Handoff Rules ↔ DT | **Medium** |
| 7 | Warm definition ambiguity | PRD ↔ QF | **Medium** |
| 8 | visitor_phone silently dropped | PRD ↔ Data Schema | **Low** |
| 9 | Scoring matrix incomplete | Data Schema ↔ DT | **Low** |
| 10 | Unresponsive language difference | DT ↔ Handoff Rules | **Low** |

## Pattern

The 3 **High** severity contradictions (#1, #2, #3) all follow the same pattern: the **PRD was written first** as a high-level spec, and the detailed design docs (Qualification Framework, Handoff Rules, Decision Tree) evolved the design significantly — but the **PRD was never updated** to reflect these changes. The detailed docs are generally consistent with each other.

**Recommendation:** Update the PRD to align with the Qualification Framework, Handoff Rules, and Decision Tree on these 3 points. Resolve the m_money enum ambiguity in the Data Schema. Add the Calendly availability fallback to the Decision Tree flows.
