# IndoorApp Architecture

This document captures the entire technical landscape of IndoorApp, from its current on-prem setup to the cloud-ready simulated architecture with blue/green deployments.
---

## 1. As-Is Architecture — The On-Prem Castle 🏰

```mermaid
%%{init: {"theme":"dark","themeVariables":{
  "background":"transparent",
  "primaryColor":"#0B1221","primaryBorderColor":"#8BA3BE","primaryTextColor":"#E5E7EB",
  "secondaryColor":"#111827","secondaryBorderColor":"#64748B","tertiaryColor":"#1F2937",
  "lineColor":"#9CA3AF","edgeLabelBackground":"#111827"
}} }%%
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

  classDef svc fill:#0F172A,stroke:#60A5FA,stroke-width:1.6px,color:#E5E7EB;
  classDef data fill:#1F2937,stroke:#F59E0B,stroke-width:1.6px,color:#FDE68A;
  classDef idp fill:#1F2937,stroke:#F472B6,stroke-width:1.6px,color:#FBCFE8;

  class W svc;
  class DB,FS data;
  class AD idp;

  style OnPremDC fill:#0B1221,stroke:#64748B,stroke-width:1.2px,rx:8px,ry:8px;
```

---

## 2. To-Be Architecture — Cloud Simulation City 🌆

```mermaid
%%{init: {"theme":"dark","themeVariables":{
  "background":"transparent","lineColor":"#A3A3A3","edgeLabelBackground":"#0B1221"
}} }%%
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

  classDef blue fill:#0B1221,stroke:#60A5FA,color:#E5E7EB,stroke-width:2px;
  classDef green fill:#0B1221,stroke:#34D399,color:#E5E7EB,stroke-width:2px;
  classDef store fill:#0B1221,stroke:#F472B6,color:#E5E7EB,stroke-width:2px;
  classDef queue fill:#0B1221,stroke:#A78BFA,color:#E5E7EB,stroke-width:2px;
  classDef infra fill:#0B1221,stroke:#94A3B8,color:#E5E7EB,stroke-width:1.6px;

  class Ablue,AblAPI blue;
  class Agreen,AgrAPI green;
  class DB,S3,BLOB store;
  class MQ,SNS queue;
  class REG,CI,LOGS infra;
```

---

## 3. Deployment View — Local Mini-Cloud Blueprint 🗺️

```mermaid
%%{init: {"theme":"dark","themeVariables":{"background":"transparent","lineColor":"#9CA3AF"}} }%%
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

  classDef runtime fill:#0B1221,stroke:#64748B,stroke-width:1.2px;
  classDef blue fill:#0B1221,stroke:#60A5FA,stroke-width:2px;
  classDef green fill:#0B1221,stroke:#34D399,stroke-width:2px;
  classDef db fill:#0B1221,stroke:#F59E0B,stroke-width:2px;

  class WEB_B,API_B blue;
  class WEB_G,API_G green;
  class PG db;
  class Laptop,Runtime runtime;
```

---

## 4. Sequence: Book a Room — A Tiny Journey 🚶‍♂️🏨

```mermaid
%%{init: {"theme":"dark","themeVariables":{"background":"transparent","lineColor":"#9CA3AF","actorLineColor":"#9CA3AF","signalColor":"#CBD5E1"}} }%%
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

## 5. Data Flow — IndoorApp

```mermaid
%%{init: {"theme":"dark","themeVariables":{"background":"transparent","lineColor":"#9CA3AF"}} }%%
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

  classDef user fill:#0B1221,stroke:#60A5FA,stroke-width:2px,color:#E5E7EB;
  classDef api fill:#0B1221,stroke:#A78BFA,stroke-width:2px,color:#E5E7EB;
  classDef db fill:#0B1221,stroke:#F59E0B,stroke-width:2px,color:#E5E7EB;
  classDef store fill:#0B1221,stroke:#F472B6,stroke-width:2px,color:#E5E7EB;
  classDef queue fill:#0B1221,stroke:#A78BFA,stroke-width:2px,color:#E5E7EB;

  class U user;
  class WEB,API api;
  class DB db;
  class S3 store;
  class Q,W queue;
```

---

## Legend 🗝️
- 🟦 **Blue** = Current live service
- 🟩 **Green** = New version warming up
- 🏰 **On-Prem** = Old world
- 🌆 **Cloud Simulation** = New world
- **Cylinders** = Databases
- **Purple** = Async messaging
- **Pink** = File/Object storage
