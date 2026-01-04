# Kafka-Orchestration-Practice
```mermaid
graph TD
    %% Define Styles
    classDef service fill:#f9f,stroke:#333,stroke-width:2px,color:black;
    classDef topic fill:#ff9,stroke:#f66,stroke-width:2px,stroke-dasharray: 5 5,color:black;
    classDef analytics fill:#ccf,stroke:#333,stroke-width:2px,color:black;

    %% Nodes
    OS["Order Service (Producer)"]:::service
    PS["Payment Service (Consumer/Producer)"]:::service
    IS["Inventory Service (Consumer/Producer)"]:::service
    AS["Analytics Engine (Consumer)"]:::analytics

    T_OC[("Topic: order_created")]:::topic
    T_PS[("Topic: payment_success")]:::topic
    T_PF[("Topic: payment_failed")]:::topic
    T_OS[("Topic: order_shipped")]:::topic
    T_OOS[("Topic: out_of_stock")]:::topic

    %% Flow
    OS -->|"1. Generates Order"| T_OC
    T_OC -->|"Reads Order"| PS
    PS -->|"2a. Valid Payment"| T_PS
    PS -->|"2b. Invalid Payment"| T_PF
    T_PS -->|"Reads Paid Order"| IS
    IS -->|"3a. Stock Available"| T_OS
    IS -->|"3b. Out of Stock"| T_OOS

    %% Analytics
    T_OC -.-o|"Monitor"| AS
    T_PS -.-o|"Monitor"| AS
    T_PF -.-o|"Monitor"| AS
    T_OS -.-o|"Monitor"| AS
    T_OOS -.-o|"Monitor"| AS


```
```mermaid
sequenceDiagram
    participant User
    participant Orchestrator
    participant PaymentSvc
    participant InventorySvc

    User->>Orchestrator: Start Order (ORD-123)
    
    Note over Orchestrator: State: PENDING_PAYMENT
    Orchestrator->>PaymentSvc: CMD: CHARGE_CARD
    
    PaymentSvc-->>Orchestrator: REPLY: SUCCESS
    
    Note over Orchestrator: State: PENDING_INVENTORY
    Orchestrator->>InventorySvc: CMD: SHIP_ITEMS
    
    InventorySvc-->>Orchestrator: REPLY: SUCCESS
    
    Note over Orchestrator: State: COMPLETED
