flowchart TD
    LOOKUP["Check from the Users Table for match (internal):1. visitor_name 2. visitor_company 3. previous_lead_score = Hot/Warm/Nurture/DQ/unscored 4. conversation_stage 5. qualification_complete = (boolean) 6. nurture_stage = (N1-N5 or null) 7. resources_shared = (array of links already sent) 8. conversion_action = meeting_booked/form_submitted/resources_sent/none"]

    LOOKUP --> MATCH{Match?}
    MATCH -- "No" --> NO_MATCH([move to UC1: Step 1.Greeting])
    MATCH -- "Yes" --> GREET[Greet by name]

    GREET --> SCORE_CHECK["previous_lead_score in the User Table check (internal)"]

    %% ===== HOT =====
    SCORE_CHECK --> HOT[HOT]

    HOT --> HOT_CONV{"conversation_action = meeting_booked?"}
    HOT_CONV -- "Yes" --> HOT_FOLLOWUP["Follow-up conversation. No re-qualification."]
    HOT_CONV -- "No" --> HOT_FORM{"conversation_action = form_submitted?"}

    HOT_FORM -- "Yes" --> HOT_REOFFER1[Re-offer Calendly]
    HOT_FORM -- "No" --> HOT_REOFFER2[Re-offer Calendly]

    HOT_REOFFER1 --> HOT_RESP1{Visitor response?}
    HOT_RESP1 -- "No" --> HOT_DECLINE(["No worries — your details are already with our team, they'll follow up. Anything else I can help with?"])
    HOT_RESP1 -- "Yes" --> HOT_BOOKED(["move to UC1: Meeting Booked flow"])

    HOT_REOFFER2 --> HOT_RESP2{Visitor response?}
    HOT_RESP2 -- "Yes" --> HOT_BOOKED(["move to UC1: Meeting Booked flow"])
    HOT_RESP2 -- "No" --> HOT_FORM_FB2(["move to UC1: Contact Form"])

    HOT_DECLINE --> UC2_HOT([UC2: Visitor asks\nquestions])
    HOT_FOLLOWUP --> UC2_HOT

    %% ===== WARM =====
    SCORE_CHECK --> WARM[WARM]
    WARM --> WARM_MISSING["Check what A/M/P is missing — internal"]
    WARM_MISSING --> WARM_PROBE["Probe missing criteria: If M missing: 'Last time, budget was still being figured out. Has anything changed?'; If A missing: 'You mentioned needing to check with your team. Any updates?'; If P missing: 'Has your timeline firmed up since we last spoke?'"]

    WARM_PROBE --> WARM_CHECK{A/M/P improved?}
    WARM_CHECK -- "Yes, all 4 CHAMP = positive" --> WARM_TO_HOT["change lead_score to Hot (internal)"]
    WARM_CHECK -- "Yes, but still one of A/M/P = unclear" --> WARM_NOCHANGE["no changes to lead_score (internal)"]
    WARM_CHECK -- "No" --> WARM_NOCHANGE

    WARM_TO_HOT --> HOT_CONV
    WARM_NOCHANGE --> HOT_CONV

    %% ===== NURTURE =====
    SCORE_CHECK --> NUR_ENTRY[NURTURE]
    NUR_ENTRY --> NUR_STAGE["Check nurture_stage value(internal)"]

    NUR_STAGE --> NUR_N4U["N4_upgraded. Check the nurture_upgraded_to value (internal)"]
    NUR_STAGE --> NUR_N1N5[N1/N4_nudged/N5. Start from N2: Check-in. If cases resonate/ If anything changed]
    NUR_STAGE --> NUR_N2N3["N2 or N3: Start from N2. Check in — if cases resonate, if anything changed"]
    NUR_STAGE --> NUR_NULL["null: Start from N1. Share resources"]

    NUR_N4U --> NUR_QUAL{Is qualification_complete = true?}
    NUR_N1N5 --> NUR_CONTINUE["Follow the next steps of\nUC1: Nurture Flow"]
    NUR_N2N3 --> NUR_CONTINUE
    NUR_NULL --> NUR_CONTINUE

    NUR_QUAL -- "Yes" --> HOT_CONV
    NUR_QUAL -- "No" --> NUR_MOVE_S6(["Move to UC1:\nStep 6: Discovery_volume"])
    
    %% ===== DQ =====
    SCORE_CHECK --> DQ[DQ]
    DQ --> DQ_REASON["Check lead_score_reason (internal)"]

    DQ_REASON --> DQ_ICP["by ICP exclusion/spam"]
    DQ_REASON --> DQ_CHAMP["by CHAMP/insufficient budget"]

    DQ_ICP --> DQ_NOREQ["No re-qualification.UC2: Visitor asks questions OR Warm close."]

    DQ_CHAMP --> DQ_CHANGE{"Has anything changed?"}
    DQ_CHANGE -- "No" --> DQ_NOREQ["No re-qualification. UC2: Visitor asks questions OR Warm close."]
    DQ_CHANGE -- "Yes" --> DQ_SIGNALS{"Signals improved?"}

    DQ_SIGNALS -- "No" --> DQ_NOREQ
    DQ_SIGNALS -- "Yes" --> DQ_UPDATE["Update CHAMP signals (internal)"]

    DQ_UPDATE --> DQ_QUAL{Is qualification_complete true?}
    DQ_QUAL -- "Yes" --> DQ_REQUALIFY[Re-qualify the lead]
    DQ_QUAL -- "No" --> DQ_STAGE["Check conversation_stage (internal)"]

    DQ_REQUALIFY --> DQ_MOVE_S10(["Move to UC1. Step 10: Scoring Engine"])
    DQ_STAGE --> DQ_RESUME(["Resume Discovery from UC1"])

    %% ===== UNSCORED =====
    SCORE_CHECK --> UNSCORED[UNSCORED]
    UNSCORED --> UNS_STAGE["Check conversation_stage (internal)"]

    UNS_STAGE --> UNS_DISC{"Is conversation_stage = discovery_company or further?"}
    UNS_DISC -- "Yes" --> UNS_RESD["Resume Discovery from UC1."]
    UNS_DISC -- "No" --> UNS_REF["Reference previous consultation"]

    UNS_REF --> UNS_READY{"Visitor ready to start Discovery?"}
    UNS_READY -- "Yes" --> UNS_START(["Start Discovery from UC1\n(Steps 1-10)"])
    UNS_READY -- "No" --> UNS_UC2(["UC2: Visitor asks questions"])

    %% Styling
    style HOT fill:#b2f2bb,stroke:#2b8a3e
    style WARM fill:#a5d8ff,stroke:#1971c2
    style NUR_ENTRY fill:#c3fae8,stroke:#0ca678
    style DQ fill:#ffc9c9,stroke:#c92a2a
    style UNSCORED fill:#e8e8e8,stroke:#666
    style NO_MATCH fill:#b2f2bb,stroke:#2b8a3e
    style LOOKUP fill:#e8e8e8,stroke:#666
    style DQ_ICP fill:#ffc9c9,stroke:#c92a2a
    style DQ_CHAMP fill:#ffc9c9,stroke:#c92a2a
    style DQ_NOREQ fill:#ffc9c9,stroke:#c92a2a
    style WARM_TO_HOT fill:#b2f2bb,stroke:#2b8a3e
    style NUR_N4U fill:#ffd8a8,stroke:#e8590c
    style NUR_N1N5 fill:#a5d8ff,stroke:#1971c2
    style NUR_N2N3 fill:#a5d8ff,stroke:#1971c2
    style NUR_NULL fill:#a5d8ff,stroke:#1971c2