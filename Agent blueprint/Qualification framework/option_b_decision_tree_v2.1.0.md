# Sales AI Agent Decision Tree v2.1.0 — CHAMP · Scored Flow

> Updated: February 26, 2026

> Agent evaluates CHAMP signals · Hot + Warm → Calendly (tagged) · Nurture → resources + re-qualify · DQ → polite close
> Knowledge gap = partial answer OR admit gap → suggest alternative + contact email → resume discovery
> Early Nurture = CH not established after Step 5 → Nurture flow → if CH improves, resume at Step 6

---

## Main Conversation Flow (Steps 1–9)

```
┌─────────────────────────────────────────────────────────────────────┐
│                     VISITOR INITIATES CHAT                          │
│                  (Landing page: Sales AI Agent)                     │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 1: GREETING                                                   │
│                                                                     │
│  "Hi — I'm here to help you figure out if an inbound Sales AI       │
│   Agent could work for your business. Mind if I ask a few           │
│   questions?"                                                       │
│                                                                     │
│  Context: No broad Halo Lab pitch. Visitor already chose this page. │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 2: INTRODUCTION                                               │
│                                                                     │
│  Brief framing: what an inbound Sales AI Agent is, what it does     │
│  (qualify leads, book meetings, handle FAQs), typical outcomes.     │
│  Sets expectations.                                                 │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 3: DISCOVERY_COMPANY                                          │
│                                                                     │
│  "Tell me about your company — what do you do?"                     │
│  "What's your role there?"                                          │
│                                                                     │
│  Collects: Company name, visitor's role, industry, team size.       │
│  Stored as single string in discovery_company variable.             │
│  Industry + location inferred (optional — for ICP exclusion check). │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
                  ┌─────────────────────────────┐
                  │  ICP EXCLUSION CHECK        │
                  │  (silent — no announcement) │
                  └─────────────┬───────────────┘
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
              ▼                                   ▼
    ┌───────────────────┐               ┌───────────────────┐
    │  PASSES           │               │  FAILS            │
    │  (No exclusions   │               │  Adult/18+        │
    │   detected)       │               │  russia-based     │
    │                   │               │                   │
    │  Continue flow    │               │  → AUTO DQ        │
    │        ↓          │               │  Polite close:    │
    └────────┬──────────┘               │  "Thanks for      │
             │                          │  reaching out —   │
             │                          │  this isn't       │
             │                          │  something we're  │
             │                          │  able to help     │
             │                          │  with."           │
             │                          │                   │
             │                          │  No conversion.   │
             │                          └───────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 4: DISCOVERY_USE_CASE [CHAMP: Challenges]                     │
│                                                                     │
│  "What are you looking for in a Sales AI Agent?"                    │
│                                                                     │
│  Qualify leads? Book meetings automatically? Answer FAQs 24/7?      │
│  Handle sales objections? What problem is driving this need?        │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 5: DISCOVERY_PAIN_POINTS [CHAMP: Challenges — depth]          │
│                                                                     │
│  "What's the biggest pain in your current sales process?"           │
│                                                                     │
│  Too slow to respond to leads? Sales team overwhelmed?              │
│  Low website conversion? No 24/7 coverage?                          │
│  Lost deals to faster competitors?                                  │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
                  ┌─────────────────────────────┐
                  │  EARLY NURTURE CHECKPOINT   │
                  │  Are use case + pain points │
                  │  defined? (CH signal)       │
                  └─────────────┬───────────────┘
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
              ▼                                   ▼
    ┌───────────────────┐               ┌───────────────────┐
    │  CH = POSITIVE    │               │  CH = UNCLEAR /   │
    │  (use case +      │               │  NEGATIVE         │
    │   pain defined)   │               │  (not defined)    │
    │                   │               │                   │
    │  Continue to      │               │  → EARLY NURTURE  │
    │  Step 6           │               │  Enter Nurture    │
    │        ↓          │               │  flow (N1–N5).    │
    └────────┬──────────┘               │                   │
             │                          │  If CH becomes    │
             │                          │  positive at N4:  │
             │                          │  → Resume at      │
             │                          │    Step 6         │
             │                          │  (NOT Calendly)   │
             │                          └───────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 6: DISCOVERY_VOLUME                                           │
│                                                                     │
│  "How many leads per month does your website currently get?"        │
│  "What kind of conversation volume do you expect after launch?"     │
│                                                                     │
│  Critical for Botpress plan scoping and pricing.                    │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 7: DISCOVERY_CURRENT_STACK                                    │
│                                                                     │
│  "What sales tools do you use today?"                               │
│                                                                     │
│  CRM (HubSpot, Salesforce, Pipedrive, none)?                        │
│  Website platform? Current chat tools?                              │
│  Integrations needed (Calendly, Slack, email)?                      │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 8: DISCOVERY_TIMELINE [CHAMP: Prioritization]                 │
│                                                                     │
│  "When are you hoping to have this live?"                           │
│  "Is there a trigger event — product launch, funding round?"        │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
       ┌────────────┐   ┌────────────┐   ┌────────────┐
       │  URGENT    │   │ REALISTIC  │   │ FLEXIBLE   │
       │  < _ weeks │   │ _+ weeks   │   │ "Someday"  │
       │            │   │            │   │ exploring  │
       │  P signal: │   │  P signal: │   │  P signal: │
       │  NEGATIVE  │   │  POSITIVE  │   │  NEGATIVE  │
       └─────┬──────┘   └─────┬──────┘   └─────┬──────┘
             │                │                 │
             └────────────────┴─────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 9: DISCOVERY_BUDGET [CHAMP: Money + Authority]                │
│                                                                     │
│  "What budget range are you working with for this project?"         │
│  "Who else is involved in the decision?"                            │
│                                                                     │
│  Reference floor: €5,000 (starting price, subject to change).       │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
       ┌────────────┐   ┌────────────┐   ┌────────────┐
       │  ≥ €5,000  │   │  < €5,000  │   │ "Not sure" │
       │            │   │ (explicit) │   │ / won't    │
       │ M signal:  │   │            │   │ say /      │
       │ POSITIVE   │   │ M signal:  │   │ undefined  │
       │            │   │ NEGATIVE   │   │            │
       │ Also check │   │            │   │ M signal:  │
       │ Authority: │   │ → DQ       │   │ UNCLEAR    │
       │ Decision-  │   │ trigger:   │   │            │
       │ maker?     │   │ insufficient│   │ Does NOT   │
       │ Y → A: POS │   │ budget     │   │ trigger DQ │
       │ N → A: NEG │   │            │   │            │
       └─────┬──────┘   └─────┬──────┘   └─────┬──────┘
             │                │                 │
             └────────────────┴─────────────────┘
                              │
                              ▼
```

---

## Step 10: CHAMP SCORING ENGINE

```
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 10: INTERNAL CHAMP EVALUATION                                 │
│                                                                     │
│  Agent evaluates all 4 CHAMP signals internally.                    │
│  Score is NEVER announced to the visitor.                           │
│  Language and next step naturally reflect the category.             │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     CHAMP SIGNAL EVALUATION                         │
│                                                                     │
│  CH — Challenges:  Clear use case + specific pain articulated?      │
│                    REQUIRED. Without this → max outcome = Nurture.  │
│                                                                     │
│  A  — Authority:   Decision-maker or confirmed budget access?       │
│                    STRONG. Differentiates Hot from Warm.            │
│                                                                     │
│  M  — Money:       Budget ≥ €5,000? → positive                      │
│                    Below €5,000 (explicit) → negative = DQ trigger. │
│                    "Not sure" / undefined → unclear (no DQ).        │
│                    STRONG. Differentiates Hot from Warm.            │
│                                                                     │
│  P  — Prioritiz.:  _+ weeks realistic deadline?                     │
│                    MODERATE. Differentiates Hot from Warm.          │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
                  ┌─────────────────────────┐
                  │   SCORING LOGIC         │
                  └─────────────┬───────────┘
                                │
     ┌──────────┬───────────────┼───────────────┬──────────┐
     │          │               │               │          │
     ▼          ▼               ▼               ▼          ▼
┌─────────┐ ┌─────────┐  ┌───────────┐  ┌─────────┐ ┌─────────┐
│   HOT   │ │  WARM   │  │  NURTURE  │  │   DQ    │ │   DQ    │
│         │ │         │  │ (Standard)│  │ (CHAMP) │ │ (ICP)   │
│All 4    │ │CH: ✓    │  │           │  │No need  │ │Adult/   │
│positive │ │         │  │CH: ✓      │  │Wrong    │ │18+      │
│         │ │1-2 of   │  │A: unclear │  │scope    │ │russia-  │
│CH: ✓    │ │A, M, P  │  │  /negative│  │No sales │ │based    │
│A:  ✓    │ │positive │  │M: unclear │  │team     │ │         │
│M:  ✓    │ │         │  │P: unclear │  │Spam     │ │         │
│P:  ✓    │ │At least │  │  /negative│  │ICP excl.│ │Caught   │
│         │ │1 of 3   │  │           │  │Budget   │ │at       │
│         │ │positive │  │None of    │  │< €5,000 │ │Step 3   │
│         │ │         │  │A/M/P is ✓ │  │(explicit)│ │         │
└────┬────┘ └────┬────┘  └─────┬─────┘  └────┬────┘ └────┬────┘
     │          │             │             │           │
     ▼          ▼             ▼             ▼           ▼
```

---

## Routing After Scoring

```
┌─────────────────────────────────────────────────────────────────────┐
│  HOT LEAD                                                           │
│                                                                     │
│  "This sounds like a great fit. I'd love to get you on a call       │
│   with our team — here's a link to book a time."                    │
│                                                                     │
│  → Present Calendly link                                            │
│  → Tag as HOT in Botpress Table                                     │
│  → Calendly → syncs to HubSpot                                      │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│  WARM LEAD                                                          │
│                                                                     │
│  "There's definitely something to explore here. Let me get you      │
│   connected with our team — here's a link to book a call."          │
│                                                                     │
│  → Present Calendly link                                            │
│  → Tag as WARM in Botpress Table                                    │
│  → Sales team prepares for missing signals (budget objection, etc.) │
│  → Calendly → syncs to HubSpot                                      │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│  NURTURE                                                            │
│                                                                     │
│  → Nurture Flow (see below)                                         │
│  → Can upgrade to Warm or Hot if signals improve                    │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│  DQ                                                                 │
│                                                                     │
│  "Thanks for reaching out — based on what you've described,         │
│   this service may not be the right fit right now."                 │
│                                                                     │
│  → Polite close. No conversion action. No Calendly.                 │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Additional Handoff Scenarios (Any Category)

```
              ┌─────────────────────────────────────┐
              │ After scoring, additional triggers: │
              └─────────────────┬───────────────────┘
                                │
              ┌─────────────────┼─────────────────┐
              │                                   │
              ▼                                   ▼
    ┌───────────────────┐               ┌───────────────────┐
    │  KNOWLEDGE GAP    │               │  VISITOR DECLINES │
    │  (any category,   │               │  CALENDLY         │
    │   any point)      │               │  (Hot or Warm)    │
    │                   │               │                   │
    │  1. Offer an      │               │  "No problem — I  │
    │  alternative the  │               │  can have someone │
    │  agent can help   │               │  reach out. Can I │
    │  with.            │               │  take your        │
    │                   │               │  details?"        │
    │  2. Provide       │               │                   │
    │  contact email    │               │  → Contact form   │
    │  for human        │               │    as fallback    │
    │  follow-up.       │               │  → Botpress Table │
    │                   │               │                   │
    │  3. Continue      │               │                   │
    │  discovery flow.  │               │                   │
    └───────────────────┘               └───────────────────┘
```

---

## Nurture Flow (with Upgrade Path)

```
┌─────────────────────────────────────────────────────────────────────┐
│  NURTURE FLOW                                                       │
│                                                                     │
│  Entry conditions:                                                  │
│  1. EARLY NURTURE: CH unclear/negative after Step 5                 │
│     (use case + pain points not defined)                            │
│  2. STANDARD NURTURE: CH positive, but none of A/M/P               │
│     are positive after Step 10                                      │
│                                                                     │
│  Agent frames check-in with intent to upgrade.                      │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  N1: SHARE RESOURCES                                                │
│                                                                     │
│  "Let me share a couple of examples of what we've built — these     │
│   should give you a clearer sense of what's possible."              │
│  [share case studies]                                               │
│                                                                     │
│  Agent notes visitor's reaction.                                    │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  N2: CHECK IN                                                       │
│                                                                     │
│  "Did any of those feel close to what you're thinking about?"       │
│                                                                     │
│  Agent uses response to refine understanding of Challenges.         │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  N3: RE-QUALIFICATION [CHAMP revisit]                               │
│                                                                     │
│  "What would need to change on your side for this to become         │
│   something you'd want to move forward on?"                         │
│                                                                     │
│  Surfaces blockers. Agent re-evaluates CHAMP signals.               │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
                  ┌─────────────────────────────┐
                  │  DID CHAMP SIGNALS IMPROVE? │
                  └─────────────┬───────────────┘
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
              ▼                                   ▼
    ┌───────────────────┐               ┌───────────────────┐
    │  YES — UPGRADE    │               │  NO — NO UPGRADE  │
    │                   │               │                   │
    │  STANDARD NURTURE:│               │  N4: Soft Contact │
    │  CH + 1 signal    │               │  form nudge       │
    │  confirmed → WARM │               │                   │
    │  All 4 → HOT      │               │  "Even if it's    │
    │  Present Calendly │               │  just exploratory  │
    │  with upgraded    │               │  — I can have     │
    │  tag.             │               │  someone follow   │
    │                   │               │  up directly. Can │
    │  EARLY NURTURE:   │               │  I take your      │
    │  CH becomes       │               │  details?"        │
    │  positive →       │               │                   │
    │  RESUME DISCOVERY │               │                   │
    │  at Step 6        │               │                   │
    │  (NOT Calendly)   │               │                   │
    └───────────────────┘               └────────┬──────────┘
                                                 │
                                   ┌─────────────┴─────────────┐
                                   │                           │
                                   ▼                           ▼
                          ┌──────────────┐           ┌──────────────┐
                          │  ACCEPTED    │           │  DECLINED    │
                          │              │           │              │
                          │  → Contact   │           │  → N5: Warm  │
                          │  form        │           │    close     │
                          │  (tag:       │           │              │
                          │   Nurture)   │           │              │
                          └──────────────┘           └──────┬───────┘
                                                            │
                                                            ▼
                                          ┌──────────────────────────┐
                                          │  N5: WARM CLOSE          │
                                          │                          │
                                          │  "Totally understood —   │
                                          │  come back when the      │
                                          │  timing is better. I'll  │
                                          │  make a note so our team │
                                          │  has context if you do   │
                                          │  reach out."             │
                                          └──────────────────────────┘
```

---

## Knowledge Gap Escalation (Any Point in Flow)

```
                    ┌──────────────────────┐
                    │  VISITOR ASKS        │
                    │  A QUESTION          │
                    └──────────┬───────────┘
                               │
    ┌──────────────────────────┼──────────────────────────┐
    │                          │                          │
    ▼                          ▼                          ▼
┌──────────┐           ┌──────────────┐           ┌──────────────┐
│ CAN      │           │ CAN ANSWER   │           │ CANNOT       │
│ ANSWER   │           │ PARTIALLY    │           │ ANSWER       │
│          │           │              │           │              │
│ Services │           │ Pricing      │           │ Exact prices │
│ Process  │           │ (frameworks) │           │ Custom integ │
│ Use cases│           │ General      │           │ Contract     │
│ Case     │           │ timelines    │           │ terms        │
│ studies  │           │              │           │ Tech edge    │
│          │           │              │           │ cases        │
└────┬─────┘           └──────┬───────┘           └──────┬───────┘
     │                        │                          │
     ▼                        ▼                          ▼
Answer from KB.        1. Answer what you can.   1. Acknowledge the gap.
                       2. Suggest alternative       Do NOT invent an
                          the agent CAN help        answer.
                          with.                  2. Suggest alternative
                       3. Provide contact email     the agent CAN help
                          for full details:         with.
                          "You can reach our     3. Provide contact email
                          team at [email] —         for human follow-up:
                          they'll get back          "You can reach our
                          to you."                  team at [email] —
                       4. Continue discovery         they'll get back
                          flow.                     to you."
                                                 4. Continue discovery
                                                    flow.
     │                        │                          │
     └────────────────────────┴──────────────────────────┘
                              │
                              ▼
                   ┌────────────────────┐
                   │ Return to          │
                   │ discovery flow     │
                   └────────────────────┘
```

---

## CHAMP Scoring Quick Reference

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CHAMP → CATEGORY MATRIX                          │
│                                                                     │
│  CH (Challenges)  A (Authority)  M (Money)  P (Prioritization)      │
│  ──────────────   ─────────────  ─────────  ────────────────        │
│                                                                     │
│   HOT:    ✓              ✓           ✓          ✓                   │
│                                                                     │
│   WARM:   ✓         1-2 of A/M/P positive                           │
│             (at least 1 of A/M/P confirmed as positive)             │
│                                                                     │
│   NURTURE (Early):  unclear/negative (after Step 5)                 │
│             Use case + pain points not defined                      │
│             → If CH becomes positive at N4: resume at Step 6        │
│                                                                     │
│   NURTURE (Standard): ✓   all of A/M/P unclear or negative         │
│             CH confirmed but none of A/M/P positive (after Step 10) │
│                                                                     │
│   DQ:     No relevant need, wrong scope, no sales team,             │
│             spam, ICP exclusion (Adult/18+, russia-based),          │
│             or budget explicitly below €5,000                       │
│                                                                     │
│  M (Money) SIGNAL:                                                  │
│    ≥ €5,000 → positive | < €5,000 explicit → negative (DQ)         │
│    "Not sure" / undefined → unclear (NO DQ)                         │
│                                                                     │
│  KEY RULE: Without Challenges confirmed → max outcome = Nurture     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Recovery Paths

### Visitor Goes Off-Topic
```
Visitor: [off-topic message]
     │
     ▼
Agent: "That's interesting! Coming back to your Sales AI Agent
        project..."
     │
     ▼
Return to current discovery step
```

### Visitor Wants to Skip Discovery
```
Visitor: "Can I just schedule a call?"
     │
     ▼
Agent: "Absolutely! Just a couple quick questions so our team
        can prepare the right approach for you."
     │
     ▼
Quick discovery (company + use case + timeline)
     │
     ▼
Agent scores with available data → Route accordingly
(Likely Warm due to incomplete signals, but CH may be clear)
```

### Visitor Becomes Unresponsive
```
[No response for 2+ minutes]
     │
     ▼
Agent: "Still there? No rush — let me know when you're
        ready to continue."
     │
     ▼
[Still no response]
     │
     ▼
Agent: "No worries — I'll save our conversation so we can
        pick up where we left off. Feel free to come back
        anytime."
     │
     ▼
Soft close. Context saved for returning visitor logic.
```

### Visitor Raises Objection
```
Visitor: [objection — price, timing, competition, trust, authority]
     │
     ▼
Agent: 1. Acknowledge the concern
       2. Address with KB content (case studies, frameworks,
          social proof)
       3. Reframe value if appropriate
     │
     ▼
Resume current discovery step or handoff flow

Note: If objection falls outside KB → Knowledge Gap flow
      (suggest alternative + contact email + resume)
Agent must NOT: offer discounts, guarantee outcomes,
                compare competitors by name, pressure visitor.
```

### Auto-DQ Scenarios
```
┌─────────────────────────────────────────────────────────────────────┐
│  AUTOMATIC DISQUALIFICATION TRIGGERS                                │
│                                                                     │
│  1. ICP exclusion detected at Step 3:                               │
│     - Adult/18+ content industry                                    │
│     - russia-based company                                          │
│     → Immediate polite close. No Calendly. No form.                 │
│                                                                     │
│  2. Insufficient budget detected at Step 9:                         │
│     - Budget EXPLICITLY stated as less than €5,000                  │
│     - "Not sure" / undefined budget → M: UNCLEAR (NOT DQ)          │
│     → DQ trigger (explicit < €5k only). Polite close.              │
│     → "Thanks for reaching out — based on what you've described,    │
│        this service may not be the right fit right now."            │
│                                                                     │
│  3. CHAMP scoring = DQ at Step 10:                                  │
│     - No relevant need (visitor has no sales challenge)             │
│     - Wrong scope (looking for something unrelated)                 │
│     - No sales team (no one to augment)                             │
│     - Spam / irrelevant                                             │
│     → "Thanks for reaching out — based on what you've described,    │
│        this service may not be the right fit right now."            │
│     → Polite close. No conversion action.                           │
└─────────────────────────────────────────────────────────────────────┘
```
