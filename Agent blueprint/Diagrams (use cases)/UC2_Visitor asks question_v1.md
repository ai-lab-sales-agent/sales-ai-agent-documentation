flowchart TD
    TRIGGER([Visitor asks a question]) --> KB["Check Knowledge Base for data (internal)"]

    KB --> CAN{Can answer a question?}

    CAN -- "Yes, fully" --> ANSWER["Answer the question from the KB"]
    CAN -- "Partially" --> PARTIAL["Answer what the agent can from the KB"]
    CAN -- "Cannot answer" --> CANNOT["Acknowledge the gap honestly."]

    PARTIAL --> GAP_LOG
    CANNOT --> GAP_LOG

    GAP_LOG["Log the gap in knowledge_gap_triggered and knowledge_gap_question (internal)"]

    GAP_LOG --> SUGGEST["Suggest an alternative. Provide a contact email."]

    ANSWER --> STAGE
    SUGGEST --> STAGE

    STAGE["Check conversation_stage (internal)"]

    STAGE --> RESUME([Resume Discovery from UC1])

    %% Styling
    style TRIGGER fill:#a5d8ff,stroke:#1971c2
    style KB fill:#d0ebff,stroke:#1971c2,stroke-dasharray: 5 5
    style ANSWER fill:#a5d8ff,stroke:#1971c2
    style PARTIAL fill:#a5d8ff,stroke:#1971c2
    style CANNOT fill:#a5d8ff,stroke:#1971c2
    style GAP_LOG fill:#d0ebff,stroke:#1971c2,stroke-dasharray: 5 5
    style SUGGEST fill:#a5d8ff,stroke:#1971c2
    style STAGE fill:#d0ebff,stroke:#1971c2,stroke-dasharray: 5 5
    style RESUME fill:#a5d8ff,stroke:#1971c2