# 📅 Study Plan — AZ-305 Azure Solutions Architect Expert

## Core Study Principle

**Architect's elimination model:** Every scenario has hard constraints (storage ceilings, protocol requirements, regional compliance) and soft attributes (cost, performance, simplicity). Apply hard constraints first — eliminate before optimising. This is how the exam tests you.

Reference: <https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/az-305>

---

## Full Exam — 6-Week Plan

Study order follows domain weight: Domain 4 (30–35%) first, then 1 → 2 → 3.

| Week | Domain | Topics | Primary Resources |
|------|--------|--------|-------------------|
| **1** | Domain 4 — Compute + Networking | VMs, Batch, Functions plans, ACI, App Service; VNet, NSG, Front Door, Traffic Manager, App Gateway, Load Balancer | MS Learn: Design infrastructure solutions |
| **2** | Domain 4 — App Arch + Data Integration | Redis, Service Bus (Q vs Topics), APIM, Event Hubs, ADF pipeline components, Databricks, ADLS Gen2, Synapse | MS Learn: Design infrastructure solutions |
| **3** | Domain 1 — Identity + Governance + Monitoring | Entra ID, hybrid identity, RBAC scope, PIM, Conditional Access, Management Groups, Azure Policy effects, Monitor + Defender | MS Learn: Design identity, governance, monitoring |
| **4** | Domain 2 — Relational Storage | SQL DB tiers (two-gate model), SQL MI vs SQL DB, sharding vs elastic pools vs Hyperscale | MS Learn: Design data storage solutions |
| **5** | Domain 2 — Non-Relational Storage | Cosmos DB, Table Storage SLA comparison, Blob tiers, Azure Files (Standard vs Premium), Disk tiers, redundancy (LRS/ZRS/GRS/GZRS), CMK, ADLS Gen2 | MS Learn: Design data storage solutions |
| **6** | Domain 3 — Business Continuity | AZs vs Sets (SLA, scope, failure type), ASR, Backup vaults, RTO/RPO design patterns, PaaS HA (SQL geo-replication, Cosmos multi-region) | MS Learn: Design business continuity solutions |

**Weeks 7–8 (optional):** Practice exams, gap analysis, weak domain review.

### Per-Week Approach

1. Read domain notes (`domains/`)
2. Review relevant comparison tables (`artifacts/service-comparison-tables.md`)
3. Run through the domain quiz (`quiz/01-` through `04-`)
4. Check applicable exam traps (`resources/exam-traps.md`)
5. Flag any glossary gaps (`resources/glossary.md`)

---

## Renewal Sprint — 1–2 Days

For the open-book annual renewal assessment:

| Priority | Action | File |
|----------|--------|------|
| 🔴 1 | Run full decision frameworks cold (no notes) | `artifacts/decision-frameworks.md` |
| 🔴 2 | Read all 30 exam traps | `resources/exam-traps.md` |
| 🟡 3 | Review the key numbers reference card | `artifacts/decision-frameworks.md` (bottom section) |
| 🟡 4 | Run the renewal assessment Q&A once | `quiz/renewal-assessment.md` |
| 🟢 5 | Review any missed questions in domain notes | `domains/` |

---

## Microsoft Learn Paths (Free)

| Learning Path | Domain |
|--------------|--------|
| AZ-305: Design identity, governance, and monitoring solutions | D1 |
| AZ-305: Design data storage solutions | D2 |
| AZ-305: Design business continuity solutions | D3 |
| AZ-305: Design infrastructure solutions | D4 |

All paths: <https://learn.microsoft.com/en-us/training/browse/?products=azure&roles=solution-architect>

---

## Recommended Practice Exam Providers

| Provider | Notes |
|----------|-------|
| **Microsoft Learn Practice Assessment** | Free, official, ~50 questions. Easier than real exam but good for gap ID |
| **Tutorials Dojo** (tutorialsdojo.com) | Highly rated, scenario-based, good explanations |
| **Whizlabs** | Good volume of questions |
| **MeasureUp** | Official Microsoft practice test partner |

> ⚠️ Avoid brain dump sites (exam dumps). Microsoft certification agreement explicitly prohibits their use — revocation of certification if caught, and dumps typically contain stale/incorrect answers from retired exam versions.

---

## Well-Architected Framework — Five Pillars

Every design decision on the exam maps to a WAF pillar:

| Pillar | Design considerations |
|--------|----------------------|
| **Reliability** | HA/DR, redundancy, failover, graceful degradation |
| **Security** | Identity (least privilege), encryption (CMK), network isolation (Private Link) |
| **Cost Optimisation** | Right-sizing, consumption models (Functions), lifecycle policies |
| **Operational Excellence** | Monitoring (Azure Monitor), IaC, CI/CD, tagging |
| **Performance Efficiency** | Caching (Redis), async messaging, auto-scaling, CDN |

Reference: <https://learn.microsoft.com/en-us/azure/well-architected/>
