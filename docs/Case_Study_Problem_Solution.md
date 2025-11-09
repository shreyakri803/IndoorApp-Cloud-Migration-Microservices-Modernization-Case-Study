# Case Study — NovaSphere: IndoorApp Cloud Migration

**Date:** 09 Nov 2025  
**Company (Hypothetical):** NovaSphere  
**Product:** IndoorApp (facility booking + attendance)  
**Role:** IT Project Manager (Simulated)

---

## Problem Statement
IndoorApp runs on an aging on-prem monolith with frequent outages, slow releases, and no reliable disaster recovery. Peak usage (morning attendance, meeting start times) causes performance drops that frustrate employees and overload IT Ops.

---

## Root Cause (Why it really hurts)
- Legacy VM stack + manual deployments → config drift and downtime during releases.  
- Tight coupling (monolith) → small changes require whole-app redeploy.  
- No automated rollback → longer MTTR during incidents.  
- Infra scale-up is manual and slow.

---

## Proposed Solution (What we’ll do)
- Containerize Web/API/DB and **simulate** cloud services locally (LocalStack for AWS, Azurite for Azure).  
- Design microservice-ready architecture with **Blue/Green** deployment and a scripted rollback.  
- Establish CI/CD blueprint (build, test, artifact, deploy).  
- Create full PM governance: Scope/WBS, RACI, RAID, Comms, Test/UAT, Status.

---

## Why This Works (Business value)
- **Fewer outages** via standardised containers and defined rollback.  
- **Faster releases** with build/test automation.  
- **No billing risk** (everything runs on free/local emulators).  
- **Clarity & control** through complete PM documentation.

---

## Success Criteria (Measurable)
- UAT pass on critical flows (Book room, Cancel booking, Dashboard).  
- Tabletop DR shows rollback ≤ 60 minutes (simulated).  
- Blue/Green go-live checklist signed off.

---

## Scope (In / Out)
- **In:** containerization, CI/CD design, DR/rollback runbook, PM docs, local cloud emulation.  
- **Out:** paid cloud deployment, production data migration.

---

## Methodology
- **Agile Scrum** for delivery (time-boxed), **Kanban** for ongoing ops.

