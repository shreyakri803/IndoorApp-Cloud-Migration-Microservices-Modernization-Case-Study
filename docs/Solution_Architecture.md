# Solution Architecture

## As-Is (4 bullets)
- On-prem VMs, single monolith.  
- Manual deployments, downtime windows.  
- No automated rollback; ad-hoc fixes.  
- Scale-up requires tickets and lead time.

## To-Be (4 bullets)
- Containerized Web/API/DB (docker-compose).  
- CI/CD blueprint with build/test stages.  
- Blue/Green deployment + scripted rollback.  
- Local cloud emulators (LocalStack/Azurite) for S3/Blob.

---

## Diagrams (Mermaid)
```mermaid
flowchart LR
User --> Web[Nginx Web]
Web --> API[IndoorApp API]
API --> DB[(Postgres)]
API --> S3[(LocalStack S3)]
API --> Blob[(Azurite Blob)]
