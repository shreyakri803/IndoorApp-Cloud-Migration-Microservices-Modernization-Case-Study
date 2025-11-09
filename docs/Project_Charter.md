✅ PROJECT CHARTER

Project Name: IndoorApp Cloud Migration & Microservices Modernization
Company: NovaSphere Technologies
Prepared By: Shreya Kumari (IT Project Manager – Simulation)
Date: 09 November 2025

1. Project Purpose & Background

IndoorApp is NovaSphere’s internal application used daily for meeting room bookings, desk scheduling, and attendance management. The current on-premise monolithic system frequently experiences outages, slow performance, and downtime during deployments. These issues impact productivity and increase operational workload for the IT team.
This project aims to modernize IndoorApp by migrating it to a cloud-ready, containerized microservices architecture using Docker, LocalStack (AWS emulator), and Azurite (Azure emulator).

-------------
2. Project Objectives

Modernize IndoorApp from monolithic to modular microservices.

Improve reliability, performance, and scalability during peak usage.

Implement containerization using Docker for consistent deployments.

Simulate AWS/Azure integrations without real cloud cost.

Establish a CI/CD pipeline with blue-green deployment and rollback capability.

Reduce outages and improve end-user experience.

-----------------

3. Project Scope (High-Level)
✅ In Scope

AS-IS and TO-BE architecture documentation

Microservices design for Web, API, and Database components

Docker-based containerization

Integration with LocalStack (S3/SQS) and Azurite (Blob Storage)

CI/CD simulation using GitHub Actions

UAT planning, execution, DR simulation

PM artifacts: WBS, RAID, RACI, Status Reports, Test Plan

❌ Out of Scope

Real AWS/Azure deployment

Migration of actual user data

Complete UI/UX redesign

Integration with external enterprise systems

---------------------------------

4. Key Stakeholders

| Role            | Name            | Responsibilities                           |
| --------------- | --------------- | ------------------------------------------ |
| Project Sponsor | CTO (Simulated) | Oversight, approvals, governance           |
| Project Manager | Shreya Kumari   | Planning, execution, reporting             |
| Tech Lead       | Simulated Role  | Architecture guidance, engineering support |
| QA Analyst      | Simulated Role  | UAT planning & testing                     |
| IT Operations   | Simulated Role  | Deployment, DR, environment support        |


-----------------------


5. Assumptions

All cloud services will be simulated using free local tools.

Necessary tools (Docker, LocalStack, Azurite) are installed locally.

No external vendor support is required.

Documentation and approval cycles occur instantly (for simulation).

-------------------------------


6. Constraints

Zero cloud billing allowed (free tools only).

Limited local machine resources.

Tight project timeline due to simulation format.

Single-person ownership of the project workload.

---------------------------

7. Risks (High-Level)

Possible downtime during component migration.

Learning curve for containerization and cloud emulators.

Environment inconsistencies without centralized cloud services.

Limited monitoring leading to delayed issue detection.


----------------------------


8. Deliverables

AS-IS & TO-BE architecture diagrams

Containerized microservices

CI/CD simulation setup

Migration strategy

Go-Live & Rollback plan

UAT test plan & results

PM artifacts (Charter, Scope, WBS, RACI, RAID, status reports)

Final PPT deck

----------------------------


9. Approval

Approved By:
✔ CTO (Simulated) — NovaSphere Technologies
✔ IT Project Manager — Shreya Kumari
