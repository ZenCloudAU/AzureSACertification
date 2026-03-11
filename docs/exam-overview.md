# 📘 Exam Overview — AZ-305: Designing Microsoft Azure Infrastructure Solutions

## Certification at a Glance

| Field | Detail |
|-------|--------|
| Exam code | **AZ-305** |
| Full name | Designing Microsoft Azure Infrastructure Solutions |
| Certification | Microsoft Certified: Azure Solutions Architect Expert |
| Passing score | **700 / 1000** |
| Question count | 40–60 questions (mix of MCQ, case studies, drag-and-drop) |
| Duration | 120 minutes seat time (~140 min total appointment) |
| Related exams | None (AZ-303/304 retired March 2024) |
| Prerequisite | AZ-104: Microsoft Azure Administrator (or legacy path) |
| Validity | 1 year · renew via free annual online assessment |
| Last English update | **October 18, 2024** (significant update) |
| Last reviewed | January 14, 2026 (minor accuracy updates) |

> No negative marking. Flag questions for review within a case study — you cannot return across sections.

---

## Role Definition

As a Microsoft Azure solutions architect, you have subject matter expertise in designing cloud and hybrid solutions on Azure, including compute, network, storage, monitoring, and security. You advise stakeholders and translate business requirements into designs aligned with the:

- **Azure Well-Architected Framework (WAF)** — five pillars: Reliability, Security, Cost Optimisation, Operational Excellence, Performance Efficiency
- **Cloud Adoption Framework (CAF)** — phases: Strategy, Plan, Ready, Adopt, Govern, Manage, Secure

You partner with developers, administrators, security engineers, and data engineers.

**Prerequisites expected:** Advanced IT operations experience — networking, virtualisation, identity, security, BCDR, data platforms, governance. Azure administration, development, and DevOps process experience.

---

## Official Domain Breakdown (post-October 2024)

### Domain 1 — Design identity, governance, and monitoring solutions (25–30%)

- **Identity:** Microsoft Entra ID (Azure AD), hybrid identity (PHS/PTA/Federation), B2B, B2C, Managed Identities, Service Principals
- **Access control:** RBAC (Owner/Contributor/Reader/UAA + custom roles), scope hierarchy (MG → Sub → RG → Resource), PIM, Conditional Access
- **Governance:** Management groups (max 6 levels deep), Azure Policy (effects: Deny/Append/Modify/DeployIfNotExists/AuditIfNotExists/Audit), initiatives, compliance, Azure Blueprints
- **Monitoring:** Azure Monitor (Metrics 93-day retention, Log Analytics/KQL, Application Insights, Alerts, Workbooks), Microsoft Defender for Cloud (Secure Score, regulatory compliance)

### Domain 2 — Design data storage solutions (20–25%)

- **Relational:** Azure SQL Database (GP 4TB / BC ~16TB fastest failover / Hyperscale 128TB), SQL Managed Instance (instance-scoped features: Agent, CLR, linked servers), sharding, elastic pools
- **Non-relational:** Cosmos DB (99.99% SLA vs Table Storage 99.9%), Table Storage, Blob (tiers: Hot/Cool/Cold/Archive), Azure Files (Standard SMB/GZRS vs Premium NFS/no geo), Disk tiers (Standard HDD/SSD → Premium SSD → Ultra Disk)
- **Redundancy:** LRS / ZRS / GRS / GZRS — regional compliance gates
- **Security:** CMK via Azure Key Vault / Managed HSM, Private Endpoints, Service Endpoints
- **Analytics store:** ADLS Gen2 (Blob + Hierarchical Namespace + HDFS/ABFS — cannot disable HNS post-creation)

### Domain 3 — Design business continuity solutions (15–20%)

- **HA scope:** Availability Sets (99.95% SLA, hardware/rack failure, single DC) vs Availability Zones (99.99% SLA, datacenter failure, 3+ zones per region)
- **DR:** Azure Site Recovery (RPO ~30s for Azure VMs, cross-region failover), geo-replication, auto-failover groups
- **Backup:** Azure Backup (Recovery Services Vault for VMs/SQL/Files; Backup Vault for Disks/Blobs/PostgreSQL), soft delete (14 days), retention policies
- **RTO/RPO design:** Match architecture tier to business requirements; cost vs resilience tradeoff

### Domain 4 — Design infrastructure solutions (30–35%) ← highest

- **Compute:** VMs, Azure Batch (HPC parallel, managed), Functions (Consumption/Premium/Dedicated plans), ACI (Azure Files for shared multi-mount), App Service, AKS
- **Networking:** VNet, NSG, Front Door (L7 global, single hostname, WAF), Traffic Manager (DNS global, all protocols), Application Gateway (L7 regional), Load Balancer (L4 regional), ExpressRoute (private, lowest latency), VPN Gateway (S2S/P2S), NAT Gateway (outbound SNAT, no inbound), Bastion (RDP/SSH no public IP), Virtual WAN (automated global WAN + Firewall Manager), UDR (force traffic through NVA), Private Link, Service Endpoints
- **App architecture:** Redis Cache (in-memory SQL caching), Service Bus (Queue = point-to-point, Topics = pub/sub), Event Hubs (streaming, Capture → Blob/ADLS), APIM (registers APIs not servers), Logic Apps, Event Grid
- **Data integration:** ADF (Pipeline > Activity > Dataset > Linked Service > IR > Trigger), Azure Databricks (managed Spark), Stream Analytics, Synapse, ADLS Gen2

---

## Certification Path

```
AZ-900 Azure Fundamentals [optional entry point]
          ↓
AZ-104 Azure Administrator Associate [prerequisite]
          ↓
AZ-305 Azure Solutions Architect Expert ← this exam
          ↓
Free annual renewal assessment (open-book, ~26 questions)
```

---

## Renewal Assessment (for reference)

- **Format:** ~26 scenario-based questions, open-book, online
- **Domains covered:** All 7 original renewal domains (storage, HA/DR, compute, app arch, networking, data integration)
- **Pass threshold:** 57%+ (renewal scale differs from 700/1000 main exam)
- **Phil's renewal result:** 92% · March 11, 2026 · Valid → April 29, 2027

> Source: Official exam page https://learn.microsoft.com/en-us/credentials/certifications/exams/az-305/
