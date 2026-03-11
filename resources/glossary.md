# 📖 Glossary — AZ-305 Azure Solutions Architect Expert

60+ key Azure service definitions, distinctions, and architect-level notes.

---

## Identity & Governance

| Term | Definition |
|------|-----------|
| **Microsoft Entra ID** | Azure's cloud-based Identity and Access Management (IAM) service. Formerly Azure Active Directory (Azure AD). |
| **RBAC (Role-Based Access Control)** | Azure's authorisation system for managing access to Azure resources using role assignments at a defined scope. |
| **Role assignment** | Combination of: security principal + role definition + scope. Up to 2,000 per subscription. |
| **PIM (Privileged Identity Management)** | Azure AD P2 feature. Enables just-in-time elevation for admin roles, reducing standing privilege. |
| **Conditional Access** | Azure AD P1 feature. Policy engine that evaluates signals (user, device, location, risk) to grant/block/require MFA. |
| **Managed Identity** | An Azure AD identity automatically managed by Azure for an Azure resource. No credential management needed. System-assigned (tied to resource lifecycle) or user-assigned (standalone). |
| **Service Principal** | Identity for applications/services to access Azure resources. Manual credential management. |
| **B2B (Business-to-Business)** | Invite external partners as guest users. Partner manages their own credentials. |
| **B2C (Business-to-Consumer)** | Customer-facing identity platform. Users sign in with social/local accounts. |
| **Password Hash Sync (PHS)** | Hybrid identity method. On-prem AD password hashes synced to Azure AD. Most resilient — works even if on-premises is down. |
| **Pass-Through Auth (PTA)** | Hybrid identity method. Authentication requests passed to on-prem AD via connector agent. Requires on-premises connectivity. |
| **Federation (AD FS)** | Full token-based federation with on-premises AD FS. Used for complex claims, SmartCard, legacy compliance. |
| **Management Group** | Container above subscriptions for organising governance. Max depth: 6 levels below tenant root. |
| **Azure Policy** | Service for creating, assigning, and managing governance rules. Evaluates resource compliance and optionally remediates. |
| **Policy Initiative** | A collection of policy definitions grouped for a governance goal (e.g., ISO 27001 compliance set). |
| **Azure Blueprints** | Packages Policy + RBAC + ARM templates + Resource Groups for repeatable environment provisioning. |

---

## Monitoring

| Term | Definition |
|------|-----------|
| **Azure Monitor** | Unified monitoring platform. Collects Metrics, Logs, and Traces from Azure resources. |
| **Azure Metrics** | Numerical time-series data (CPU%, bytes, request count). 93-day default retention. Near real-time. |
| **Log Analytics Workspace** | Central store for Azure Monitor Logs. Queried with KQL. Configurable retention 30–730 days. |
| **KQL (Kusto Query Language)** | Query language for Log Analytics. SQL-inspired but optimised for time-series log data. |
| **Application Insights** | APM (Application Performance Monitoring) for web apps. Captures traces, exceptions, dependencies, page views. |
| **Azure Monitor Agent (AMA)** | Current recommended agent for collecting guest OS metrics/logs. Replaces MMA/OMS agent. |
| **Alerts** | Rules that fire when metric/log thresholds are crossed. Can trigger Action Groups (email, webhook, ITSM, runbook). |
| **Workbooks** | Interactive Azure Monitor reports combining metrics, logs, text, and parameters. |
| **Microsoft Defender for Cloud** | Cloud Security Posture Management (CSPM) + Cloud Workload Protection Platform (CWPP). Secure Score, threat alerts, regulatory compliance view. |

---

## Networking

| Term | Definition |
|------|-----------|
| **VNet (Virtual Network)** | Fundamental building block. Isolated network in Azure. Resources in same VNet can communicate by default. |
| **Subnet** | Segment within a VNet. Used to logically divide resources and apply NSGs/UDRs. |
| **NSG (Network Security Group)** | Stateful L3/L4 packet filter. Applied to subnet or NIC. Inbound/outbound rules by port, protocol, IP. |
| **ASG (Application Security Group)** | Group VMs by function (web tier, DB tier). Use in NSG rules instead of explicit IPs. |
| **VNet Peering** | Low-latency connection between two VNets in same/different regions. **Non-transitive** — traffic does not flow A→B→C without explicit peering A→C. |
| **Azure Front Door** | Global L7 (HTTP/HTTPS) reverse proxy. Anycast, single hostname, WAF, SSL offload, latency routing, CDN caching. |
| **Azure Traffic Manager** | DNS-based global load balancing. Protocol-agnostic (any TCP/UDP). DNS redirects to regional endpoints. No proxy, no WAF. |
| **Application Gateway** | Regional L7 HTTP/HTTPS load balancer. URL-based routing, WAF, SSL offload, session affinity. |
| **Azure Load Balancer** | Regional L4 (TCP/UDP) load balancer. Internal (ILB) or external (public). No HTTP inspection. |
| **ExpressRoute** | Private dedicated circuit connecting on-premises to Azure via partner/provider. Lowest latency. NOT encrypted by default. Max 100 Gbps. |
| **VPN Gateway** | Encrypted tunnel (IPSec) over public internet. S2S (site-to-site) or P2S (point-to-site). Max ~10 Gbps. |
| **NAT Gateway** | Managed outbound SNAT for subnets. Pool of public IPs. Outbound-only — no inbound. Up to 16 IPs. |
| **Azure Bastion** | PaaS-managed jump server. Browser RDP/SSH over HTTPS. No public IP or open port 3389/22 needed on VM. Requires `AzureBastionSubnet` (/27+). |
| **UDR (User Defined Route)** | Custom route table overriding Azure system routes. Used to force traffic through NVA/firewall as next hop. |
| **NVA (Network Virtual Appliance)** | Third-party (or Azure Firewall) virtual appliance deployed in a VNet for traffic inspection/filtering. |
| **Private Endpoint** | A NIC in your VNet with a private IP mapped to an Azure PaaS service. Traffic stays within VNet. |
| **Service Endpoint** | Extends VNet identity to PaaS service. Traffic still exits VNet but PaaS allows only from that VNet. Public IP of PaaS remains accessible from elsewhere. |
| **Azure Firewall** | Managed stateful L3–L7 firewall-as-a-service. FQDN filtering, TLS inspection, IDPS. Typically deployed in hub VNet. |
| **Azure Virtual WAN** | Managed global WAN topology. Automated branch connectivity, inter-hub routing, Microsoft backbone. Standard tier adds Firewall Manager, NVA, ExpressRoute. |

---

## Storage

| Term | Definition |
|------|-----------|
| **LRS (Locally Redundant Storage)** | 3 copies within one datacenter in one region. Lowest cost, least fault tolerance. |
| **ZRS (Zone-Redundant Storage)** | 3 copies across 3 Availability Zones within one region. Survives datacenter failure. |
| **GRS (Geo-Redundant Storage)** | 6 copies: 3 in primary region (LRS) + 3 in secondary paired region (LRS). Read access to secondary only after failover. |
| **GZRS (Geo-Zone-Redundant Storage)** | 6 copies: 3 across 3 AZs in primary region (ZRS) + 3 in secondary region (LRS). Highest durability. |
| **RA-GRS / RA-GZRS** | Read-access variants of GRS/GZRS. Secondary region readable at all times (not just after failover). |
| **CMK (Customer-Managed Key)** | Customer controls Azure Storage encryption key via Azure Key Vault. Default is Microsoft-managed key (MMK). |
| **Managed HSM** | FIPS 140-2 Level 3 validated single-tenant HSM. Higher assurance than Key Vault (Level 2). Used for regulated industries. |
| **HNS (Hierarchical Namespace)** | Feature of ADLS Gen2. Enables true directory operations, POSIX ACLs, and HDFS (ABFS driver) compatibility. Cannot be disabled post-creation. |
| **ABFS driver** | Azure Blob File System driver. Makes ADLS Gen2 accessible via HDFS API from Databricks, Spark, Hadoop. |
| **Blob tiers** | Hot (frequently accessed), Cool (infrequent, 30-day minimum), Cold (rare, 90-day minimum), Archive (offline, 180-day minimum). |
| **Lifecycle management** | Azure Storage policy to automatically tier or delete blobs based on age/last access. |

---

## Compute

| Term | Definition |
|------|-----------|
| **Azure Batch** | Managed HPC-scale parallel job processing. Auto provisions/deprovisions nodes, schedules tasks, auto-scales. |
| **Azure Functions** | Serverless, event-driven compute. Consumption plan: pay per execution, max 60 min, auto-scale, cold start. |
| **Azure Container Instances (ACI)** | Run containers without managing a cluster. Fast start, per-second billing. Persistent storage via Azure Files volume mount. |
| **AKS (Azure Kubernetes Service)** | Managed Kubernetes. Customer manages worker nodes (or use virtual nodes). Complex setup, high control. |
| **Azure App Service** | PaaS for web apps and APIs. Managed runtime, built-in CI/CD, auto-scale. Limited container control. |
| **Availability Set** | VM grouping within one datacenter. Fault domains (racks) + update domains. SLA 99.95%. |
| **Availability Zone** | VM deployed to a specific physical zone (separate datacenter). SLA 99.99%. Protects against datacenter failure. |
| **VMSS (Virtual Machine Scale Set)** | Auto-scaling group of identical VMs. Supports both AZ and AS deployment. |

---

## Data & Analytics

| Term | Definition |
|------|-----------|
| **Azure SQL Database** | Fully managed PaaS relational database. Three tiers: General Purpose (4 TB), Business Critical (~16 TB, fastest failover), Hyperscale (128 TB). |
| **Azure SQL Managed Instance** | Fully managed SQL Server with near 100% compat. Supports instance-scoped features (SQL Agent, CLR, linked servers). Max 16 TB. |
| **Azure Cosmos DB** | Globally distributed, multi-model NoSQL database. 99.99% SLA (single region). APIs: Core (SQL), Table, Gremlin (graph), Cassandra, MongoDB. |
| **Sharding** | Horizontal partitioning of data across multiple independent databases by shard key. Used when data exceeds a single database's storage ceiling. |
| **Elastic pool** | Shared DTU/vCore resource pool for multiple Azure SQL Databases with variable/unpredictable workloads. Cost efficiency, not storage expansion. |
| **Azure Cache for Redis** | In-memory key-value store for caching, session, pub/sub, leaderboards. Cache-aside pattern reduces SQL load. |
| **Service Bus Queue** | Point-to-point reliable messaging. FIFO (sessions), duplicate detection, dead-letter queue. |
| **Service Bus Topic** | Pub/sub messaging. Each subscription gets an independent copy of the message. |
| **Event Hubs** | High-throughput event streaming platform. Consumer groups for parallel processing. Capture → Blob/ADLS in Avro format. |
| **Event Grid** | Event routing service. Push-based, reactive, low-latency. Triggers Azure Functions, Logic Apps, Webhooks on events. |
| **Azure Data Factory** | Cloud ETL/ELT orchestration. Components: Linked Service, Dataset, Activity, Pipeline, Integration Runtime, Trigger. |
| **Azure Databricks** | Managed Apache Spark platform. Batch + streaming + ML. Collaborative notebooks. Premium SKU for credential passthrough to ADLS. |
| **Azure Stream Analytics** | Real-time stream processing using SQL DSL. Inputs: Event Hubs, IoT Hub, Blob. Outputs: SQL, Blob, Cosmos DB, Power BI. |
| **Azure Synapse Analytics** | Unified analytics platform combining SQL DW, Spark, Pipelines, and linked services. |
| **Azure Data Explorer (ADX)** | Fast log and telemetry analytics using KQL. Optimised for time-series and exploratory queries. |
| **Azure Analysis Services** | PaaS SSAS tabular model for BI. Semantic model layer between raw data and Power BI. |
| **APIM (API Management)** | API gateway platform. Publishes and manages API endpoints (not servers). Policies, transformations, subscriptions, developer portal. |
