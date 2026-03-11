# 📚 Book & Study Notes — AZ-305 Azure Solutions Architect Expert

Compiled from: uploaded study guides (Architect_certification_exam.docx, Az_305.docx, Study_Notes.rtf, final_305.docx), the Azure Solution Architect Map (503923771 mind map PDF), and community resources.

---

## Source 1 — Architect Certification Exam Study Guide (uploaded)

A structured 6-day study track covering AZ-305 domains in sequential order.

### Day-by-Day Coverage

**Day 1 — Azure Fundamentals**
- Azure portal navigation and resource hierarchy
- Azure subscriptions and management groups
- Azure pricing and support tiers
- Azure Active Directory (now Microsoft Entra ID) fundamentals

**Day 2 — Azure Networking**
- Virtual Networks, Subnets, Network Security Groups
- VPN Gateway and ExpressRoute
- Azure Load Balancers and Application Gateways

**Day 3 — Virtual Machines and Containers**
- Azure VM storage options
- Azure Container Instances and Container Registries
- Azure Kubernetes Service (AKS) features

**Day 4 — Azure Storage**
- Storage accounts: GPv1, GPv2, FileStorage, BlockBlob
- Blob, Queue, File, and Table storage options
- Storage replication and data protection

**Day 5 — Monitoring and Security**
- Microsoft Defender for Cloud (formerly Security Center)
- Azure Monitor and alerting
- Azure Backup and Disaster Recovery

**Day 6 — Azure Solutions**
- Azure App Service deployment options
- Azure Functions triggers
- Azure Logic Apps connectors
- Azure API Management capabilities

---

## Source 2 — Az_305.docx (uploaded — personal study notes)

Key notes extracted from the personal study document:

### Azure Databricks — Credential Passthrough

Standard clusters with credential passthrough are limited to a **single user**. Standard clusters support Python, SQL, Scala, and R (SparkR on Runtime 6.0+, sparklyr on Runtime 10.1+).

For multi-user credential passthrough to ADLS Gen2, use **Premium tier** Databricks workspace with Unity Catalog or AAD credential passthrough.

Reference: https://docs.microsoft.com/en-us/azure/databricks/security/credential-passthrough/adls-passthrough

### Azure Firewall Manager — Hierarchical Policies

Central IT teams can author **global firewall policies** that enforce organisation-wide rules. Business units can author **local policies** using a DevOps self-service model for better agility. Local policies inherit from and cannot override global policies.

**Key constraint:** Base policies and local policies must be in the **same region** — you can still apply the policy to a Secured Hub in a different region.

### Azure SQL — DTU vs vCore Models

- **DTU model:** Bundled CPU + memory + I/O. Simpler — General Purpose and Business Critical tiers map to Standard and Premium in the DTU world.
- **vCore model:** Separate CPU, memory, and I/O configuration. More granular. Allows Azure Hybrid Benefit (bring existing SQL Server licence).

For the architect exam: **vCore** is the recommended modern model. DTU Standard and Premium still appear in distractor options — map them to vCore equivalents for elimination.

| DTU Tier | vCore Equivalent | Notes |
|----------|-----------------|-------|
| Basic | N/A | Very small DBs only |
| Standard | General Purpose | Up to 4 TB |
| Premium | Business Critical | High HA, in-memory OLTP |

---

## Source 3 — Study_Notes.rtf (uploaded)

### Azure Active Directory — Identity Fundamentals

**Subscription trust model:**
- A subscription is associated with a **single** Azure AD directory (tenant)
- A single tenant can have multiple subscriptions
- Virtual Networks **cannot span subscriptions**
- Multiple subscriptions can roll up to an Azure Enterprise Agreement

**Azure AD identity types:**
- Cloud identities — exist in Azure AD only (admin accounts you create)
- Directory-synchronized — exist on-prem, synced via AD Connect
- Guest users — external identities invited via B2B

**Azure AD group types:**
- Security groups — manage resource access (most common)
- Microsoft 365 groups — collaboration (shared mailbox, calendar, SharePoint)

### RBAC — Role Assignment Mechanics

**Access assignment methods:**
- Direct assignment — assign role directly to a user
- Group assignment — assign role to group; members inherit
- Rule-based / dynamic — group membership based on user/device attributes (requires P1)

**Role definition structure:**
- `Actions` / `NotActions` — management plane operations (ARM)
- `DataActions` / `NotDataActions` — data plane operations (blob read, queue write)
- `AssignableScopes` — where role can be assigned

**Administrative Units:** Azure AD resource that scopes admin permissions to a subset of the tenant (e.g., a geographic region or department). Only users and groups can be in an AU.

### Authentication Protocols

| Protocol | Type | Token issued | Use case |
|---------|------|-------------|---------|
| OAuth 2.0 | Authorisation framework | Access token | App accesses another app's data |
| OpenID Connect | Auth layer on OAuth 2.0 | Access token + ID token (JWT) | User sign-in, identity verification |
| SAML 2.0 | Federation | SAML assertion | Enterprise SSO, legacy IdP integration |
| WS-Federation | Federation | SAML/JWT | Legacy AD FS integration |

---

## Source 4 — final_305.docx (uploaded — personal notes)

### Azure Storage — Lifecycle Management

Use **lifecycle management policies** on Standard V2 and Blob storage accounts to automatically transition blobs between access tiers (Hot → Cool → Cold → Archive) or delete them based on age or last access time. Reduces storage costs for data that ages out of frequent access patterns.

Key constraint: Lifecycle management applies to **block blobs** — not to append blobs or page blobs.

### Azure Files — Key Differentiators

- File storage account supports **DFS (Distributed File System) namespaces** and Azure Files
- Lifecycle management is NOT available for Azure Files (only Blob storage)
- Azure Files supports identity-based authentication: on-premises AD DS, Azure AD DS, Azure AD Kerberos (for hybrid joined clients)
- SMB Multichannel available on Premium tier for improved throughput

### Azure SQL — Third Statement (Server + Storage in Different Regions)

The uploaded notes flag this as an exam trap: when designing SQL for geo-redundancy, if the VM/app server and storage (SQL MI) are in different regions, latency implications must be evaluated. SQL MI requires the server and storage to be in the same managed instance region by definition — you cannot separate them.

---

## Source 5 — Azure Solution Architect Map (PDF mind map)

The PDF captures the Azure service landscape as a mind map. Key groupings for the exam:

### Compute groupings

```
Fully Managed / Serverless:
  Azure Functions → event-driven, serverless
  App Service → web apps, REST APIs
  Static Web Sites → Blob Storage + CDN
  App Center → mobile

Container-based:
  ACI → single container, stateless, fast
  AKS → K8s orchestration, microservices
  Service Fabric/Mesh → stateful microservices, actors (Dapr)
  Web App for Containers → containerised App Service
  Azure Container Registry → image storage + ACR Tasks (CI build)
  ARO (Azure Red Hat OpenShift) → managed OpenShift

HPC:
  Azure Batch → large-scale parallel jobs
  HPC VMs → N/H series, InfiniBand

Lift & Shift / Legacy:
  Virtual Machines → full OS, custom software
  WCF Relay / Hybrid Connections → legacy .NET app connectivity
```

### Networking groupings

```
Routing/LB:
  Front Door → global L7 + CDN
  Application Gateway (+ AGIC) → regional L7, AKS Ingress
  Traffic Manager → global DNS routing
  Internal/External Load Balancers → L4 regional

Security:
  Azure Firewall → stateful managed firewall
  NSG/ASG → subnet/NIC level packet filtering
  Azure Web Application Firewall → WAF on Front Door / App GW
  NVA → marketplace 3rd-party firewalls (Palo Alto, Checkpoint, etc.)

Identity security:
  PAM AAD PIM → privileged access management
  CASB: Microsoft Cloud App Security (Defender for Cloud Apps)
  Azure AD Proxy → hybrid AAD for on-prem apps
  Azure Sentinel → SIEM (now Microsoft Sentinel)
  Security Center → Defender for Cloud (posture + CWPP)

Connectivity:
  S2S VPN → small scale, encrypted, public internet
  ExpressRoute + private/MS peering → large scale, private circuit
  Virtual WAN → branch offices, global backbone
  Azure Bastion → workstations/VMs to Azure VMs, no public IP
```

### Messaging groupings

```
Event-driven:
  Event Grid → discrete events, reactive, push
  Event Hub → event streaming, series, high volume, IoT Hub compatible

Pub/Sub / Messaging:
  Service Bus Topics/Subscriptions → pub/sub, multiple consumers
  Service Bus Queue → point-to-point, reliable
  Storage Queues → simple, cheap, large queue depth

Relay:
  WCF Relay → legacy .NET WCF bridge
  Hybrid Connections → on-premises to cloud hybrid, no VPN
```

### Analytics groupings

```
All-in-one:
  Azure Synapse → DW + Spark + Pipelines + Power BI integration

Batch analytics:
  HDInsight → Hadoop/Spark/Kafka managed (open-source)
  Azure Databricks → managed Spark, ML, Python/Scala/R

Real-time:
  Stream Analytics → SQL-based, real-time streaming
  Azure Data Explorer → KQL, log/telemetry exploration

Visual/BI:
  Power BI → reporting and dashboards
  Azure Analysis Services → SSAS tabular semantic layer
```

---

## Community Study Notes

### From TechManDan Blog (community)

**Most common wrong answers on AZ-305:**
1. Selecting Traffic Manager when "single hostname" is stated → should be Front Door
2. Forgetting to apply storage gate before BC/HS choice → leads to BC when HS is needed
3. Assuming Premium Azure Files = more redundancy → premium has LESS geo-redundancy
4. Treating SQL DB tiers as equivalent for instance-scoped features → always check for SQL Agent, CLR first

### From Charbel Nemnom Study Guide (MVP/MCT)

Key architect mindset reminders:
- The exam tests **design judgment**, not memorisation. Multiple answers may be technically possible — the exam rewards the most appropriate design for the constraints given.
- **Cost minimisation** is a recurring constraint. The cheapest option that satisfies ALL stated requirements is the correct answer, even if higher-tier options would also work.
- Read every scenario for **all constraints simultaneously** — storage, protocol, SLA, compliance, cost. Missing one constraint = wrong answer.
- **WAF pillar alignment** matters in longer scenarios. Each design decision maps to one of the five pillars. Identify which pillar is being tested.

### Microsoft Learn Practice Assessment (key gaps identified)

Based on Phil's renewal result and community reports:
- **Domain 2 (Data Storage):** Most traps are in the SQL tier selection (two-gate model) and Azure Files protocol constraints.
- **Domain 4 (Infrastructure):** Front Door vs Traffic Manager distinction is tested multiple times in different phrasings. UDR vs NSG is a consistent trap.
- **Business continuity:** Availability Set vs Zone wording is precise — memorise the exact failure scope for each.
