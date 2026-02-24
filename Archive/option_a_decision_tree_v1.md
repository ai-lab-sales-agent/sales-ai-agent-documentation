# Sales AI Agent Decision Tree — Option A (CHAMP · Soft Flow)

> No automatic scoring · All leads → Calendly · Sales team decides fit
> Contact form = knowledge gap escalation only

---

## Main Conversation Flow (Steps 1–9: Shared)

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
    │   detected)       │               │  Russia-based     │
    │                   │               │                   │
    │  Continue flow    │               │  Polite close:    │
    │        ↓          │               │  "Thanks for      │
    └────────┬──────────┘               │  reaching out —   │
             │                          │  this isn't       │
             │                          │  something we're  │
             │                          │  able to help     │
             │                          │  with."           │
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
       └─────┬──────┘   └─────┬──────┘   └─────┬──────┘
             │                │                 │
             ▼                ▼                 ▼
       "Timelines       "That works         Note as
       under _ weeks    well with our       early-stage.
       are tight —      typical build       Continue.
       we can discuss   timelines."
       phased approach
       on a call."
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
       │ States     │   │ Gives      │   │ "Not sure" │
       │ specific € │   │ range      │   │ / won't    │
       │            │   │            │   │ say        │
       └─────┬──────┘   └─────┬──────┘   └─────┬──────┘
             │                │                 │
             ▼                ▼                 ▼
       Check if ≥        Note the range     "No problem —
       €5,000                                our projects
                                             typically start
                                             at €5,000.
                                             Let's discuss
                                             what's possible
                                             on a call."
             │                │                 │
             └────────────────┴─────────────────┘
                              │
                              ▼
```

---

## Step 10: QUALIFICATION_SUMMARY (Option A)

```
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 10: QUALIFICATION_SUMMARY                                     │
│                                                                     │
│  Agent recaps collected information conversationally.               │
│  NO score announced. CHAMP signals shape the language.              │
│                                                                     │
│  "Based on what you've shared, it sounds like you're looking to     │
│   build an inbound lead qualification agent integrated with         │
│   [CRM], targeting a go-live in about [timeline]. Let me connect    │
│   you with our team to walk through the approach."                  │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                    ┌───────────┴───────────┐
                    │                       │
                    ▼                       ▼
             ┌────────────┐          ┌────────────┐
             │  CORRECT   │          │ CORRECTION │
             │            │          │ needed     │
             └─────┬──────┘          └─────┬──────┘
                   │                       │
                   │                       ▼
                   │               Update info.
                   │               Re-summarize.
                   │                       │
                   └───────────────────────┘
                              │
                              ▼
```

---

## Step 11: HANDOFF (Option A)

```
┌─────────────────────────────────────────────────────────────────────┐
│  STEP 11: HANDOFF DECISION                                          │
│                                                                     │
│  Has agent collected: company + role, use case, current stack,      │
│  timeline indication, budget range (even approximate)?              │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
     ┌──────────┬───────────────┼───────────────┬──────────┐
     │          │               │               │          │
     ▼          ▼               ▼               ▼          ▼
┌──────────┐ ┌──────────┐ ┌───────────┐ ┌──────────┐ ┌──────────┐
│SUFFICIENT│ │  WEAK    │ │  NURTURE  │ │KNOWLEDGE │ │ VISITOR  │
│  DATA    │ │ SIGNALS  │ │ SITUATION │ │   GAP    │ │ DECLINES │
│          │ │ budget/  │ │ early-    │ │ at any   │ │ CALENDLY │
│          │ │ timeline │ │ stage     │ │ point    │ │          │
└────┬─────┘ └────┬─────┘ └─────┬─────┘ └────┬─────┘ └────┬─────┘
     │            │             │            │            │
     ▼            ▼             ▼            ▼            ▼
  Present     Flag softly.  → Nurture    Offer contact  Acknowledge.
  Calendly    Still offer    Flow        form. Agent    Offer contact
  link.       Calendly +    (see below)  continues      form as
  Recap key   phased                     qualifying     fallback.
  points.     approach.                  after submit.  Close warmly.
     │           │             │             │            │
     ▼           ▼             │             ▼            ▼
  Calendly   Calendly —        │          Form →        Form →
  link       sales team        │          Botpress      Botpress
             assesses          │          Table.        Table.
             fit on call       │          Calendly
                               │          still offered
                               │          at end.
                               ▼
```

---

## Nurture Flow — Option A

```
┌─────────────────────────────────────────────────────────────────────┐
│  NURTURE: Visitor is early-stage, not ready to commit               │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  N1: SHARE RESOURCES                                                │
│                                                                     │
│  "Let me share a couple of examples of inbound Sales AI Agents      │
│   we've built — these might give you a better feel for what's       │
│   possible." [share case studies]                                   │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  N2: CHECK IN                                                       │
│                                                                     │
│  "Did any of those feel relevant to what you're working on?"        │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  N3: RE-QUALIFICATION [CHAMP: Challenges revisit]                   │
│                                                                     │
│  "What would need to change on your side for this to become         │
│   something you'd want to explore more concretely?"                 │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│  N4: SOFT CALENDLY NUDGE                                            │
│                                                                     │
│  "Even if it's just exploratory, a 20-minute call with our team     │
│   might help clarify things — no commitment needed.                 │
│   Want me to share the link?"                                       │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
              ▼                                   ▼
     ┌──────────────────┐               ┌──────────────────┐
     │  ACCEPTED        │               │  DECLINED        │
     │  → Calendly      │               │  → N5: Warm      │
     │                  │               │    close         │
     └──────────────────┘               └────────┬─────────┘
                                                 │
                                                 ▼
                                  ┌──────────────────────────┐
                                  │  N5: WARM CLOSE          │
                                  │                          │
                                  │  "Totally understood —   │
                                  │  feel free to come back  │
                                  │  when the timing is      │
                                  │  better. I'll make a     │
                                  │  note of your situation  │
                                  │  so our team has context │
                                  │  if you reach out."      │
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
Answer from KB.        Answer what you can.        "That's a great
                       "Our team can give          question — I don't
                       more detail on a call."     have enough detail.
                                                   Let me capture your
                                                   question so our
                                                   team gets back
                                                   to you."
                                                         │
                                                         ▼
                                                  ┌──────────────┐
                                                  │ CONTACT FORM │
                                                  │              │
                                                  │ Name         │
                                                  │ Email        │
                                                  │ Company      │
                                                  │ Question     │
                                                  │ (pre-filled) │
                                                  │              │
                                                  │ → Botpress   │
                                                  │   Table      │
                                                  └──────┬───────┘
                                                         │
                                                         ▼
                                                  Agent continues
                                                  the conversation.
                                                  Qualification
                                                  does not stop.
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

## Recovery Paths (Option A)

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
Quick discovery (company + use case + timeline) → Calendly
```

### Visitor Becomes Unresponsive
```
[No response for 2+ minutes]
     │
     ▼
Agent: "Still there? No rush — let me know when you're ready
        to continue, or I can have our team reach out."
     │
     ▼
Wait for response or offer contact form
```

### Visitor Not a Good Fit (Option A = manual DQ)
```
[Budget too low / timeline impossible / no clear need]
     │
     ▼
Agent: "Based on what you've shared, let me connect you with
        our team — they can explore whether there's a way
        to make this work for your situation."
     │
     ▼
Still offer Calendly. Sales team makes final DQ decision.
```
