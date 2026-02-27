flowchart TD
    A_TRIGGER(["Agent detects spam/profanity/abusive behaviour"]) --> A_STAGE{"Is conversation_stage = greeting?"}

    A_STAGE -- "Yes" --> A_MSG_YES["Respond politely.'I'm here to help with Sales AI Agent projects. Is there something I can assist with?'"]
    A_STAGE -- "No" --> A_MSG_NO["Redirect politely.'I want to make sure I can help you — let's keep things on track. Where were we?'"]

    A_MSG_YES --> A_CHECK{Behaviour changed?}
    A_MSG_NO --> A_CHECK

    A_CHECK -- "Yes" --> A_STAGE_CHK["Check conversation_stage"] --> A_RESUME(["Resume Discovery from UC1"])
    A_CHECK -- "No" --> A_CLOSE["Polite close.'It doesn't seem like I can help right now. Feel free to come back if you'd like to explore a Sales AI Agent.'"]

    A_CLOSE --> A_LOG["Log: lead_score = 'DQ', lead_score_reason = 'spam', conversation_stage = 'dq_closed', conversion_action = 'none'"]

    %% Styling
    style A_TRIGGER fill:#ffc9c9,stroke:#c92a2a
    style A_CLOSE fill:#ffc9c9,stroke:#c92a2a
    style A_LOG fill:#ffd8a8,stroke:#e8590c
    style A_RESUME fill:#b2f2bb,stroke:#2b8a3e


    B_TRIGGER(["Agent detects off-topic question/irrelevant message/gibberish"]) --> B_STRIKE1["Redirect with empathy. Off-topic: 'That's outside what I specialize in — I'm focused on Sales AI Agents. Is automating your lead qualification something you're exploring?', Gibberish: 'I didn't quite catch that — could you rephrase?'"]

    B_STRIKE1 --> B_CHECK1{Behaviour changed?}

    B_CHECK1 -- "Yes" --> B_STAGE1["Check conversation_stage"] --> B_RESUME1(["Resume Discovery from UC1"])
    B_CHECK1 -- "No" --> B_STRIKE2["Try once more, offer alternative path. Off-topic: 'I'm not the best fit for that, but I can share our team's email. Anything about Sales AI Agents I can help with?', Gibberish: 'I'm still having trouble understanding. Could you try typing that again?'"]

    B_STRIKE2 --> B_CHECK2{Behaviour changed?}

    B_CHECK2 -- "Yes" --> B_STAGE2["Check conversation_stage"] --> B_RESUME2(["Resume Discovery from UC1"])
    B_CHECK2 -- "No" --> B_CLOSE["Polite close.'You can reach our team at (email) for help. Feel free to come back anytime.'"]

    %% Styling
    style B_TRIGGER fill:#a5d8ff,stroke:#1971c2
    style B_CLOSE fill:#ffc9c9,stroke:#c92a2a
    style B_RESUME1 fill:#b2f2bb,stroke:#2b8a3e
    style B_RESUME2 fill:#b2f2bb,stroke:#2b8a3e


    C_TRIGGER(["Visitor asks for a human/skips Discovery"]) --> C_QUAL{"Is qualification_complete = true?"}

    C_QUAL -- "Yes" --> C_CALENDLY(["Move to UC1: Present Calendly link."])
    C_QUAL -- "No" --> C_DATA{"Are name, email, company captured?"}

    C_DATA -- "Yes" --> C_FORM(["Move to UC1: Contact form."])
    C_DATA -- "No" --> C_ASK["Acknowledge. Ask for missing information."]

    C_ASK --> C_PROVIDES{"Visitor provides data?"}
    C_PROVIDES -- "Yes" --> C_FORM2(["Move to UC1: Contact form."])
    C_PROVIDES -- "No" --> C_EMAIL["Provide a contact email."]

    %% Styling
    style C_TRIGGER fill:#a5d8ff,stroke:#1971c2
    style C_CALENDLY fill:#b2f2bb,stroke:#2b8a3e
    style C_FORM fill:#b2f2bb,stroke:#2b8a3e
    style C_FORM2 fill:#b2f2bb,stroke:#2b8a3e
    style C_EMAIL fill:#ffd8a8,stroke:#e8590c


    D_TRIGGER(["Visitor is inactive/unresponsive for +2 minutes."]) --> D_CHECK["Soft check. 'Still there? No rush — let me know when you're ready to continue.'"]

    D_CHECK --> D_RESPONSE{"Visitor answers within 2 minutes?"}

    D_RESPONSE -- "Yes" --> D_RESUME(["Resume conversation"])
    D_RESPONSE -- "No" --> D_CLOSE["Soft close.'I'll be here whenever you're ready. Feel free to come back anytime.'"]

    %% Styling
    style D_TRIGGER fill:#a5d8ff,stroke:#1971c2
    style D_RESUME fill:#b2f2bb,stroke:#2b8a3e
    style D_CLOSE fill:#ffc9c9,stroke:#c92a2a