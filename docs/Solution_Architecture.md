# As-Is Architecture — IndoorApp (On-Prem Monolith)

```mermaid
flowchart LR
  U[End Users (Employees/HR)] -->|HTTPS| W[IndoorApp Monolith (IIS/Apache)]
  W --> DB[(On-Prem DB Server)]
  W --> FS[(On-Prem File Share)]
  W --> AD[Corp AD/LDAP]
  subgraph On-Prem DC
    W
    DB
    FS
    AD
  end
  style On-Prem DC fill:#f7f7f7,stroke:#bbb,stroke-width:1px
`````
----------------


2) To-Be Architecture (cloud-ready, containers, blue/green)

We’ll **simulate AWS & Azure** locally (free):  
- **LocalStack** → AWS services: **ECR**(simulated), **S3**, **SQS**, **SNS**, **CloudWatch-like logs**  
- **Azurite** → Azure **Blob**  
- **Local Docker Registry** → acts like **ECR/ACR** 
- **GitHub Actions** → CI/CD (simulated with workflow files and logs)
  

```markdown
# To-Be Architecture — Cloud-Ready (Simulated AWS + Azure)

```mermaid
flowchart TB
  subgraph Client
    U[Web/Mobile Users]
  end

  subgraph BlueGreen[Blue/Green Services]
    Ablue[Web: Nginx (Blue)]
    AblAPI[API: Node/Python (Blue)]
    Ablue --> AblAPI
    Agreen[Web: Nginx (Green)]
    AgrAPI[API: Node/Python (Green)]
    Agreen --> AgrAPI
  end

  DB[(Postgres Container)]
  S3[(Object Storage - S3 via LocalStack)]
  BLOB[(Object Storage - Azure Blob via Azurite)]
  MQ[SQS Queue (LocalStack)]
  NOTIF[SNS Topic (LocalStack)]
  REG[Local Docker Registry (ECR/ACR-like)]
  CI[CI/CD: GitHub Actions (simulated)]
  LOGS[(Central Logs/CloudWatch-like - LocalStack)]

  U -->|HTTPS| Ablue
  U -->|switch flag| Agreen

  AblAPI --> DB
  AgrAPI --> DB
  AblAPI --> S3
  AgrAPI --> BLOB
  AblAPI --> MQ
  AgrAPI --> NOTIF
  AblAPI -. logs .-> LOGS
  AgrAPI -. logs .-> LOGS
  CI --> REG
  REG --> Ablue
  REG --> Agreen
```
-----------


## 3) Deployment View (who runs where)


```markdown
# Deployment View

```mermaid
graph TD
  subgraph DevLaptop[Dev/PM Laptop]
    VS[VS Code + Docker Desktop]
    LS[LocalStack]
    AZ[Azurite]
    REG[Local Docker Registry]
  end

  subgraph Runtime[Local Runtime]
    WEB_B[Web (Blue)]
    API_B[API (Blue)]
    WEB_G[Web (Green)]
    API_G[API (Green)]
    PG[(Postgres)]
  end

  VS --> REG
  REG --> WEB_B
  REG --> API_B
  REG --> WEB_G
  REG --> API_G
  API_B --> LS
  API_G --> AZ
  API_B --> PG
  API_G --> PG
```

----------


## 4) Key User Flow (Sequence: Book a Room)


```markdown
# Sequence — Book a Room (Blue/Green Safe Release)

```mermaid
sequenceDiagram
  participant User
  participant Web as Web (Nginx)
  participant API as API Service
  participant DB as Postgres
  participant S3 as S3 (LocalStack)
  participant MQ as SQS (LocalStack)

  User->>Web: Open /book
  Web->>API: POST /booking
  API->>DB: INSERT booking
  DB-->>API: BookingID
  API->>S3: Store booking slip
  API->>MQ: Publish "BookingCreated"
  API-->>Web: 201 Created + BookingID
  Web-->>User: Show confirmation

  Note over User,Web: Blue live; Green warmed up. Toggle when Green passes smoke.
```

---------


## 5) Data Flow (logical)


```markdown
# Data Flow — IndoorApp

```mermaid
flowchart LR
  subgraph Client
    U[User]
  end
  U --> WEB[Web UI]
  WEB --> API[API]
  API -->|CRUD| DB[(Postgres)]
  API -->|store slips| S3[S3/Blob]
  API -->|events| Q[SQS Topic/Queue]
  Q --> ASYNC[Async Worker (future)]
```

