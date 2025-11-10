# IndoorApp Architecture

IndoorApp is a story about a small on-prem monolith that grows wings and learns to fly in a locally simulated cloud.
This document shows every chapter — from old-world castles to blue-green skylines.

---

# 1. As-Is Architecture — The On-Prem Castle 🏰

```mermaid
flowchart LR

  U["👥 End Users (Employees / HR)"]
  W["🧱 IndoorApp Monolith (IIS / Apache)"]
  DB[("🗄️ On-Prem DB Server")]
  FS[("📂 On-Prem File Share")]
  AD["🔐 Corp AD / LDAP"]

  U -->|HTTPS| W
  W --> DB
  W --> FS
  W --> AD

  subgraph OnPremDC["🏰 On-Prem DC (The Old Kingdom)"]
    W
    DB
    FS
    AD
  end

  classDef svc fill:#EAF2FF,stroke:#3B82F6,stroke-width:1.5px,color:#1E3A8A;
  classDef data fill:#FFF3D6,stroke:#D97706,stroke-width:1.5px,color:#7C2D12;
  classDef idp fill:#FCE7F3,stroke:#DB2777,stroke-width:1.5px,color:#831843;

  class W svc;
  class DB,FS data;
  class AD idp;

  style OnPremDC fill:#F8FAFC,stroke:#94A3B8,stroke-width:1.5px
```

---

# 2. To-Be Architecture — Cloud Simulation City 🌆

```mermaid
flowchart TB

  subgraph Client["👤 Client"]
    U[User]
  end

  subgraph BlueGreen["🌆 Blue / Green Release Zone"]
    Ablue["🟦 Blue Web (Nginx)"]
    AblAPI["🟦 Blue API (Node/Python)"]
    Agreen["🟩 Green Web (Nginx)"]
    AgrAPI["🟩 Green API (Node/Python)"]
    Ablue --> AblAPI
    Agreen --> AgrAPI
  end

  DB[("🗄️ Postgres Container")]
  S3[("🪣 S3 (LocalStack)")]
  BLOB[("📦 Azure Blob (Azurite)")]
  MQ["📨 SQS Queue"]
  SNS["📣 SNS Topic"]
  LOGS[("📈 Central Logs")]
  REG["📦 Local Docker Registry"]
  CI["⚙️ GitHub Actions (Simulated)"]

  U -->|HTTPS| Ablue
  U -->|feature flag| Agreen

  AblAPI --> DB
  AgrAPI --> DB

  AblAPI --> S3
  AgrAPI --> BLOB

  AblAPI --> MQ
  AgrAPI --> SNS

  AblAPI -.-> LOGS
  AgrAPI -.-> LOGS

  CI --> REG
  REG --> Ablue
  REG --> Agreen

  classDef blue fill:#DBEAFE,stroke:#1D4ED8,color:#1E3A8A,stroke-width:2px;
  classDef green fill:#DCFCE7,stroke:#15803D,color:#14532D,stroke-width:2px;
  classDef store fill:#FFEAF5,stroke:#BE185D,color:#831843,stroke-width:2px;
  classDef queue fill:#EDE9FE,stroke:#7C3AED,color:#4C1D95,stroke-width:2px;
  classDef infra fill:#F1F5F9,stroke:#475569,color:#1E293B,stroke-width:1.5px;

  class Ablue,AblAPI blue;
  class Agreen,AgrAPI green;
  class DB,S3,BLOB store;
  class MQ,SNS queue;
  class REG,CI,LOGS infra;
```

---

# 3. Deployment View — Local Mini-Cloud Blueprint 🗺️

```mermaid
graph TD

  subgraph Laptop["💻 Dev / PM Laptop"]
    VS["VS Code + Docker Desktop"]
    LS["LocalStack"]
    AZ["Azurite"]
    REG["Local Docker Registry"]
  end

  subgraph Runtime["🟦🟩 Local Blue-Green Runtime"]
    WEB_B["🟦 Web (Blue)"]
    API_B["🟦 API (Blue)"]
    WEB_G["🟩 Web (Green)"]
    API_G["🟩 API (Green)"]
    PG[("🗄️ Postgres")]
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

  classDef runtime fill:#F8FAFC,stroke:#CBD5E1,stroke-width:1.5px;
  classDef blue fill:#DBEAFE,stroke:#1D4ED8,stroke-width:2px;
  classDef green fill:#DCFCE7,stroke:#15803D,stroke-width:2px;
  classDef db fill:#FFF3D6,stroke:#D97706,stroke-width:2px;

  class WEB_B,API_B blue;
  class WEB_G,API_G green;
  class PG db;
  class Laptop,Runtime runtime;
```

---

# 4. Sequence: Book a Room — A Tiny Journey 🚶‍♂️🏨

```mermaid
sequenceDiagram
  autonumber
  participant User
  participant Web as Web (Nginx)
  participant API as API Service
  participant DB as Postgres
  participant S3 as S3
  participant MQ as SQS Queue

  User->>Web: Open /book
  Web->>API: POST /booking
  API->>DB: INSERT booking
  DB-->>API: bookingId
  API->>S3: Upload booking slip
  API->>MQ: Publish "BookingCreated"
  API-->>Web: 201 Created + bookingId
  Web-->>User: Show confirmation
```

---

# 5. Data Flow — IndoorApp

```mermaid
flowchart LR

  U["👤 User"]
  WEB["🌐 Web UI"]
  API["🔧 API Service"]
  DB[("🗄️ Postgres")]
  S3["🪣 S3 / Azure Blob"]
  Q["📨 SNS / SQS"]
  W["⚙️ Async Worker (future)"]

  U --> WEB --> API
  API -->|CRUD| DB
  API -->|Store files| S3
  API -->|Emit events| Q
  Q --> W

  classDef user fill:#DBEAFE,stroke:#2563EB,stroke-width:2px,color:#1E3A8A;
  classDef api fill:#F3E8FF,stroke:#8B5CF6,stroke-width:2px,color:#5B21B6;
  classDef db fill:#FEF3C7,stroke:#F59E0B,stroke-width:2px,color:#92400E;
  classDef store fill:#FCE7F3,stroke:#DB2777,stroke-width:2px,color:#A11A50;
  classDef queue fill:#EDE9FE,stroke:#7C3AED,stroke-width:2px,color:#4C1D95;

  class U user;
  class WEB,API api;
  class DB db;
  class S3 store;
  class Q,W queue;
```

---

# 6. Notes & Legend 🗝️

## Release Colors
- 🟦 **Blue** = Current live service  
- 🟩 **Green** = New version warming up  

## Worlds
- 🏰 **On-Prem** = The old kingdom  
- 🌆 **Cloud Simulation** = The new skyline  

## Visual Language
- **Cylinders** = Databases  
- **Purple** = Async messaging  
- **Pink** = File/Object storage  

---

