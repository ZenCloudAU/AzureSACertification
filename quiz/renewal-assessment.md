# 🏆 Renewal Assessment — AZ-305 Azure Solutions Architect Expert

**Result:** 92% · March 11, 2026 · Valid → April 29, 2027
**Format:** 26 scenario-based questions · Open-book · Pass threshold: 57%

All 26 questions from the March 2026 renewal, with answers, explanations, and the key exam signal for each.

---

## Summary Table

| Q | Domain | Answer | Key Signal |
|---|--------|--------|-----------|
| 1 | Data storage | Cosmos DB for Table | 99.99% SLA requirement |
| 2 | Data storage | SQL MI General Purpose | SQL Server Agent (instance-scoped) |
| 3 | Data storage | Sharding | Exceed per-DB storage ceiling |
| 4 | Data storage | Hyperscale | 40 TB gates out GP and BC |
| 5 | Data storage | Premium SSD | Lowest tier clearing both IOPS + throughput |
| 6 | Data storage | Azure Files Premium | NFS = Premium-only |
| 7 | Data storage | Azure Key Vault | CMK requires Key Vault |
| 8 | Data storage | ZRS | Regional compliance + zone HA |
| 9 | Infrastructure | Azure Front Door | Single hostname + HTTP |
| 10 | Infrastructure | ExpressRoute | Minimise latency |
| 11 | Business continuity | Availability Zone | Power outage / physical location |
| 12 | Infrastructure | Azure Batch | Large-scale parallel + min admin |
| 13 | Infrastructure | Azure Files | Simultaneous multi-container mount |
| 14 | Infrastructure | Azure Functions (Consumption) | Infrequent HTTP + minimise cost |
| 15 | Infrastructure | VNet (permanent post-deployment) | VNet settings cannot be changed |
| 16 | Infrastructure | Azure Cache for Redis | Dynamic SQL caching |
| 17 | Infrastructure | Service Bus Topics | Multiple destinations, own copy |
| 18 | Infrastructure | 70 APIs | Count APIs not servers |
| 19 | Infrastructure | Blob Storage / ADLS Gen2 | Event Hubs Capture target |
| 20 | Infrastructure | NAT Gateway | Outbound-only SNAT, IP pool |
| 21 | Infrastructure | Azure Virtual WAN | Automated + central security + backbone |
| 22 | Infrastructure | UDR | Force traffic through NVA |
| 23 | Infrastructure | Azure Bastion | RDP without public IP |
| 24 | Infrastructure | Pipeline (ADF) | Groups + sequences ADF activities |
| 25 | Infrastructure | Azure Databricks | Managed Apache Spark |
| 26 | Infrastructure | ADLS Gen2 | HNS + HDFS + object storage |

---

## Full Q&A

---

**Q1.** A production application needs a table-based NoSQL API with a 99.99% SLA. Which Azure service provides this?

**Answer: Azure Cosmos DB for Table**

Azure Table Storage SLA is 99.9%. Cosmos DB for Table SLA is 99.99% (single region). All other table-API features — schemeless, consumption pricing, replication — are shared. The SLA gap is the only differentiator on this question type.

---

**Q2.** A database workload requires SQL Server Agent jobs for overnight ETL. The company wants fully managed PaaS. Which service should you use?

**Answer: Azure SQL Managed Instance (General Purpose)**

SQL Server Agent is an instance-scoped feature. It is not available in any Azure SQL Database tier. Only SQL Managed Instance supports it. General Purpose is selected over Business Critical because no elevated HA or fastest-failover requirement is stated.

---

**Q3.** A database workload grows beyond the maximum storage capacity of a single Azure SQL Database. What architectural pattern should you implement?

**Answer: Sharding**

Sharding horizontally partitions data across multiple independent databases by shard key. It is the solution when data exceeds what a single database can hold. Elastic pools share DTU/vCore resources for cost efficiency but do not break the per-DB storage ceiling.

---

**Q4.** A company hosts a 40 TB database in Azure SQL. They require the fastest possible in-region failover. Which tier should they use?

**Answer: Hyperscale**

**Gate 1:** 40 TB exceeds General Purpose (4 TB max) and Business Critical (~16 TB max). Only Hyperscale passes.
**Gate 2:** Hyperscale with an HA replica provides failover in seconds.

The trap: Business Critical = "fastest failover" is a well-known association. The storage gate eliminates it before failover speed is evaluated.

---

**Q5.** A production VM workload requires 12,000 IOPS and 500 MB/s throughput. Cost must be minimised. Which disk tier?

**Answer: Premium SSD**

Premium SSD clears both thresholds (~20,000 IOPS, ~900 MB/s). Ultra Disk provides significantly higher numbers but violates cost minimisation when Premium is sufficient. Select the lowest tier that clears all performance gates.

---

**Q6.** A Linux-based containerised workload needs to mount a shared Azure file share using NFS. Which Azure Files tier is required?

**Answer: Azure Files Premium**

NFS protocol is exclusively available on Azure Files Premium (FileStorage account). Standard Azure Files supports SMB 2.1/3.0 only. NFS on Standard is not supported regardless of tier or configuration.

---

**Q7.** A team wants to manage their own Azure Storage encryption keys and must be able to revoke access immediately. Where must the keys be stored?

**Answer: Azure Key Vault**

Customer-Managed Keys (CMK) require keys to be stored in Azure Key Vault (or Managed HSM). The storage account retrieves the key via Managed Identity at runtime. Revoking the key in Key Vault immediately renders data inaccessible.

---

**Q8.** EU data residency compliance requires storage data to remain in a single Azure region. Zone-level resilience within the primary region is required. Which redundancy option?

**Answer: ZRS (Zone-Redundant Storage)**

Compliance gate eliminates GRS, GZRS, RA-GRS, RA-GZRS (all replicate outside the region). Between LRS (single datacenter) and ZRS (3 AZs), ZRS provides zone-level fault tolerance as required.

---

**Q9.** A global web application requires a single hostname for all users worldwide, with L7-aware global load balancing and low latency routing. Which service?

**Answer: Azure Front Door**

Front Door is a global L7 (HTTP/HTTPS) reverse proxy with anycast routing — all users reach the same hostname regardless of which region serves them. Traffic Manager is DNS-based: the resolved hostname varies per region, violating the single-hostname requirement.

---

**Q10.** An enterprise needs to connect its on-premises datacenter to Azure with the lowest possible latency and consistent bandwidth. Which connectivity option?

**Answer: ExpressRoute**

ExpressRoute uses a private dedicated circuit, bypassing the public internet entirely. This provides the lowest, most consistent latency of any Azure connectivity option. VPN Gateway uses the public internet (variable latency). ExpressRoute does not encrypt by default — add IPSec if needed.

---

**Q11.** A company needs VMs to remain available if an entire Azure datacenter loses power. What should you implement?

**Answer: Availability Zones**

Power outage / physical datacenter failure = Availability Zone territory. AZs are separate physical datacenters with independent power, cooling, and networking. SLA: 99.99%.

Availability Sets only protect against rack-level hardware failures — they do not survive a full datacenter outage.

---

**Q12.** A data science team needs to run 5,000 independent rendering tasks, each taking up to 4 hours. Infrastructure must auto-provision, auto-deprovision, and require minimal admin. Which compute service?

**Answer: Azure Batch**

Batch is designed for large-scale parallel HPC. No task time limit. Nodes are auto-provisioned and deprovisioned. Minimal admin overhead once pool and jobs are configured.

Functions (Consumption) max timeout is 60 minutes — cannot run 4-hour tasks.

---

**Q13.** Three containers in an ACI group need to simultaneously read and write from the same persistent volume. Which volume type?

**Answer: Azure Files**

Azure Files supports ReadWriteMany — multiple containers can mount the same share simultaneously. Azure Disk is ReadWriteOnce (single container). emptyDir is ephemeral.

---

**Q14.** An HTTP-triggered function runs infrequently (< 500 invocations/day), executes in under 2 minutes, and must minimise cost. Which Functions plan?

**Answer: Consumption plan**

Consumption plan charges per execution only — zero cost during idle. Under 2 minutes is well within the 60-minute max. For < 500 invocations/day, consumption pricing is far cheaper than any reserved/monthly plan.

---

**Q15.** A developer wants to change the Virtual Network of a deployed Azure VM to a different VNet. What is the correct answer?

**Answer: VNet assignment is permanent — cannot be changed post-deployment. The VM must be deleted and redeployed into the target VNet.**

This is a key Azure constraint. VNet selection is made at VM deployment time and is immutable. All other VM properties (size, disks, NSGs, public IPs) can be modified. Plan network topology first.

---

**Q16.** An application performs repeated SQL queries for product catalogue data that changes once per day. Response times are slow. What should you implement?

**Answer: Azure Cache for Redis**

Redis caches SQL query results in memory. Cache-aside pattern: hit Redis first → cache miss triggers SQL query → write result to Redis → return. Product catalogue (changes once daily) is ideal for TTL-based caching. CDN caches only static content, not dynamic SQL results.

---

**Q17.** An order processing system publishes order events. Three downstream services (billing, notifications, audit) each need to receive every order event independently. Which messaging pattern?

**Answer: Azure Service Bus Topics with Subscriptions**

Topics (pub/sub): each subscription gets its own copy of each message. Three subscriptions → three independent copies. Queue pattern delivers each message to one consumer only.

---

**Q18.** Company A has App1 with 30 APIs on 5 servers and App2 with 40 APIs on 10 servers. Both are published through Azure API Management. How many APIs are registered?

**Answer: 70 APIs**

APIM registers APIs (endpoints/operations), not servers. 30 + 40 = 70. Server count (5 + 10 = 15) is irrelevant. This is a deliberate distractor.

---

**Q19.** Azure Event Hubs captures streaming telemetry. The raw event data must be stored durably for archiving and batch analytics. Which storage targets does Event Hubs Capture support?

**Answer: Azure Blob Storage and Azure Data Lake Storage Gen2**

Event Hubs Capture outputs to Blob Storage or ADLS Gen2 in Apache Avro format only. Azure SQL, Cosmos DB, and other targets are not supported by Capture. Raw streaming data lands in Blob/ADLS first, then is transformed downstream.

---

**Q20.** A subnet hosts 300 VMs that need predictable outbound internet connectivity using a defined pool of public IP addresses. Inbound connections must be blocked. Which service?

**Answer: Azure NAT Gateway**

NAT Gateway: outbound SNAT only, up to 16 public IPs, ~1M concurrent connections, automatically scales, attached at subnet level. No inbound path by design. Predictable IP pool for firewall allowlisting.

---

**Q21.** A company has 30 global branch offices requiring automated connectivity to Azure and branch-to-branch routing via the Microsoft backbone, with centralised security management. Which service?

**Answer: Azure Virtual WAN Standard**

Virtual WAN Standard: automated S2S VPN to branches, inter-hub routing via Microsoft backbone (branch-to-branch), Azure Firewall Manager integration (Secured Virtual Hub). Basic tier = S2S VPN only, no inter-hub or Firewall Manager.

---

**Q22.** All traffic from a spoke VNet subnet must be inspected by a Network Virtual Appliance (NVA) in the hub VNet before reaching the internet. What configuration enables this?

**Answer: User Defined Routes (UDR)**

UDRs override Azure's default routing. Add a route for `0.0.0.0/0` with next hop = NVA private IP to the spoke subnet route table. Also enable IP forwarding on the NVA's NIC. NSGs can filter but cannot change routing paths.

---

**Q23.** An operations team needs to RDP to Azure VMs without any public IP on the VMs and without opening port 3389. What should you implement?

**Answer: Azure Bastion**

Bastion enables browser-based RDP over HTTPS (port 443) via the Azure portal. VMs need no public IP and no port 3389 exposure. Deploy in `AzureBastionSubnet` (/27 minimum). No client-side RDP software required.

---

**Q24.** An Azure Data Factory orchestration needs to run a Copy Activity, then a Mapping Data Flow, then a Stored Procedure call in sequence. Which ADF component groups and sequences these activities?

**Answer: Pipeline**

An ADF Pipeline is the container that groups and sequences Activities (Copy, Data Flow, Stored Procedure, etc.) with dependency links and condition branches. Dataset = data reference. Linked Service = connection. Activity = single operation. Pipeline = workflow orchestrating multiple activities.

---

**Q25.** A data engineering team needs a fully managed Apache Spark environment on Azure for large-scale ETL and machine learning workloads. Which service?

**Answer: Azure Databricks**

Databricks is the managed Apache Spark platform on Azure: auto-scaling clusters, collaborative notebooks (Python/SQL/Scala/R), Delta Lake, MLflow, and native ADLS Gen2 integration. No other Azure service is a managed Spark runtime (HDInsight runs Spark but is not a managed Databricks equivalent).

---

**Q26.** A team needs to store large volumes of JSON files that will be queried using the HDFS API from Apache Spark on Azure. Which storage service is required?

**Answer: Azure Data Lake Storage Gen2**

ADLS Gen2 = Azure Blob Storage + Hierarchical Namespace (HNS). HNS enables HDFS-compatible access via the ABFS driver. Standard Blob Storage without HNS does not support HDFS semantics. Three exam signals: object storage + HNS + HDFS/Spark.

---

## Post-Assessment Analysis

### Wrong Answers (~2 missed for 92%)

**Q4 — Hyperscale vs Business Critical (40 TB + fastest failover)**
The "fastest failover" association with Business Critical fires before the storage constraint is checked. Two-gate model prevents this: Gate 1 eliminates GP and BC at 40 TB. Among survivors (Hyperscale only), Gate 2 confirms Hyperscale HA replica = seconds.

**Q9 — Front Door vs Traffic Manager (single hostname + global + latency)**
Traffic Manager latency routing matches 2/3 stated requirements. The exam exploits this. The tiebreaker is always "single hostname" — Front Door is a reverse proxy with an anycast global hostname. Traffic Manager is DNS-based with per-region resolved endpoints.

---

*Renewal assessment complete. See `resources/exam-traps.md` for all 30 distractor patterns.*
