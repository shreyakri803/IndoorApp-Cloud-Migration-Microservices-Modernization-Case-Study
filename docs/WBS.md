# Scope & WBS

## Deliverables
- Docs (charter, BRD, architecture, strategy, RACI, RAID, comms, test, status, lessons)
- Dockerized components (simulated), CI/CD design
- Go-Live & rollback checklist
- Jira backlog (CSV) + boards evidence

## WBS (Mermaid)
```mermaid
mindmap
  root((IndoorApp Migration))
    Planning
      Charter
      BRD
      Scope & WBS
      RAID & Comms
    Design
      As-Is
      To-Be
      Migration Strategy
    Build
      Containers
      IaC samples
    Test
      UAT
      DR tabletop
    Deploy
      Blue-Green
      Rollback
    Close
      Lessons Learned

