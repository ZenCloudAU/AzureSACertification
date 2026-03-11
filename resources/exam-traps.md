# ⚠️ Exam Traps — AZ-305 Azure Solutions Architect Expert

30 common distractor patterns, wrong answer magnets, and elimination rules.

> Source: Compiled from renewal assessment experience (92%), community study guides (TechManDan, Charbel Nemnom), and uploaded study materials.

---

## Trap 1 — Business Critical "fastest failover" overrides storage capacity check

**Trap:** Business Critical is deeply associated with "fastest failover." The brain fires BC immediately when it reads that phrase.

**Reality:** Business Critical on Azure SQL Database tops out well below 40 TB. If the scenario says 40 TB, BC is eliminated at Gate 1 (storage) before you even get to failover speed.

**Rule:** Check storage ceiling first. Always Gate 1 before Gate 2.

---

## Trap 2 — Traffic Manager looks correct for "latency routing" but violates single hostname

**Trap:** Traffic Manager explicitly supports latency-based routing globally — matches 2 of 3 stated requirements.

**Reality:** Traffic Manager is DNS-based. The client ultimately connects to a regional endpoint, so the hostname the client sees changes per region. "Single hostname" requirement → Front Door (anycast proxy, one global hostname regardless of serving region).

**Rule:** Single hostname = Front Door. Full stop.

---

## Trap 3 — Premium Azure Files "more redundancy options" assumption

**Trap:** Premium sounds like it has more features. Surely premium supports GZRS?

**Reality:** Premium Azure Files does NOT support geo-redundant options (GRS, GZRS, RA-GRS, RA-GZRS). Standard (GPv2) actually has more redundancy choices. Premium trades geo-redundancy for higher IOPS and lower latency.

**Rule:** NFS = Premium only. GZRS = Standard only. The requirements tell you which tier is forced.

---

## Trap 4 — Elastic pools solve the per-database storage ceiling

**Trap:** When a database exceeds GP's 4 TB, elastic pools seem like the scaling answer.

**Reality:** Elastic pools are for cost efficiency across many DBs with variable usage patterns. They do NOT break the per-DB storage ceiling.

**Rule:** Exceed single-database storage ceiling = sharding. Elastic pools = cost efficiency.

---

## Trap 5 — SQL Server Agent confusion between SQL DB tiers

**Trap:** Assuming a premium SQL DB tier (Business Critical, Hyperscale) supports SQL Server Agent.

**Reality:** SQL Server Agent is an instance-scoped feature. No tier of Azure SQL Database supports it. Only Azure SQL Managed Instance.

**Rule:** Any instance-scoped feature (Agent, CLR, linked servers, Service Broker) = SQL Managed Instance. Eliminate all SQL DB tiers.

---

## Trap 6 — Availability Set vs Availability Zone wording

**Trap:** Both protect VMs. The question wording is subtle.

**Reality:**
- "Hardware failure; datacenter failure acceptable" = Availability Set
- "Power outage; physical location failure; regional failure acceptable" = Availability Zone

**Rule:** Physical location / datacenter / power outage = Zone. Rack / hardware / single server = Set.

---

## Trap 7 — ExpressRoute is encrypted

**Trap:** ExpressRoute is a dedicated private circuit — surely it must be encrypted?

**Reality:** ExpressRoute does NOT encrypt traffic by default. It is a private connection, not an encrypted one. To get both privacy AND encryption, add IPSec over ExpressRoute.

**Rule:** ExpressRoute = lowest latency, private. NOT encrypted. S2S VPN = encrypted but public internet.

---

## Trap 8 — Service Bus queues for pub/sub scenarios

**Trap:** Service Bus queues are for reliable messaging — they look like a safe answer for any messaging scenario.

**Reality:** SB queues are point-to-point. Only one consumer receives each message. When the scenario requires **multiple destinations each receiving their own copy**, the answer is **Service Bus Topics with subscriptions**.

**Rule:** Multiple independent consumers, each needs own copy = Topics. Single consumer = Queue.

---

## Trap 9 — Azure Disk for shared ACI storage

**Trap:** Azure Disk is persistent storage — it should work for two containers sharing data.

**Reality:** Azure Disk is ReadWriteOnce — it can only be mounted by a single container at a time. For simultaneous mounting across multiple containers in the same ACI group, use Azure Files.

**Rule:** Two containers, simultaneous access = Azure Files. Single container = Disk.

---

## Trap 10 — APIM registers servers/applications, not APIs

**Trap:** "30 APIs on 5 servers" — the answer seems to involve 5, or the product of APIs × servers.

**Reality:** APIM registers API endpoints, not servers. Count APIs only.

**Rule:** 30 + 40 = 70. Ignore server count entirely.

---

## Trap 11 — CDN for dynamic SQL query performance

**Trap:** CDN improves web app performance — seems relevant for a web app query question.

**Reality:** CDN caches **static content** (images, CSS, JS, video). It does nothing for dynamic SQL data retrieval.

**Rule:** Dynamic SQL performance = Azure Cache for Redis. Static content = CDN.

---

## Trap 12 — Private Link solves outbound internet SNAT

**Trap:** Private Link controls network access to PaaS services — sounds like the answer for controlling outbound connectivity.

**Reality:** Private Link is for **inbound** private access to PaaS services from within your VNet (e.g., privately accessing a storage account). It does not provide outbound SNAT with a public IP pool.

**Rule:** Outbound internet with public IP pool + no inbound = NAT Gateway.

---

## Trap 13 — VPN Gateway for global multi-branch automated connectivity

**Trap:** VPN Gateway connects branches to Azure — seems like the answer for multi-branch global connectivity.

**Reality:** VPN Gateway is point-to-point and requires manual configuration per location. It lacks automated branch-to-branch routing and centralised security management.

**Rule:** Multi-branch global + automated connectivity + centralised security + Microsoft backbone = Virtual WAN Standard.

---

## Trap 14 — NSGs control inter-subnet routing for NVA inspection

**Trap:** NSGs can block traffic between subnets — surely they can force traffic through a firewall?

**Reality:** NSGs filter traffic but cannot change the **routing path**. Azure still routes inter-subnet traffic directly. Only UDRs can change the next-hop to route through an NVA.

**Rule:** Force traffic path change (through NVA) = UDR. Filter traffic = NSG.

---

## Trap 15 — Azure Table Storage supports 99.99% SLA

**Trap:** Azure Table Storage is a NoSQL service — perhaps it offers high SLAs like Cosmos DB?

**Reality:** Azure Table Storage SLA is **99.9%**. Cosmos DB for Table is **99.99%** (single region). All other features (schemeless, consumption pricing, replicas) are shared between both services.

**Rule:** 99.99% requirement = Cosmos DB for Table. 99.9% acceptable = Azure Table Storage (cheaper).

---

## Trap 16 — Ultra Disk is the right answer when Premium SSD meets requirements

**Trap:** Ultra Disk provides the highest performance — a scenario asking for "best performance" makes Ultra Disk attractive.

**Reality:** When requirements specify a cost constraint ("minimise cost"), choose the **lowest tier that meets all thresholds**. If Premium SSD clears both IOPS and throughput requirements, Ultra Disk violates cost minimisation.

**Rule:** Lowest tier clearing ALL performance gates, then minimise cost.

---

## Trap 17 — Event Hubs Capture outputs to SQL Database

**Trap:** The data pipeline ends in analysis — SQL Database is the "proper" storage layer for structured data.

**Reality:** Event Hubs Capture can only output to **Azure Blob Storage** or **Azure Data Lake Storage Gen2** in Avro format. SQL Database is not a Capture target. Raw streaming data lands in Blob/ADLS first (cheapest), then moves to SQL via ADF or Databricks.

**Rule:** Event Hubs Capture → Blob or ADLS Gen2 only.

---

## Trap 18 — HNS can be enabled after ADLS Gen2 account creation

**Trap:** Surely you can upgrade a standard Blob Storage account to ADLS Gen2 by enabling HNS later?

**Reality:** Hierarchical Namespace (HNS) is configured at **account creation** and **cannot be disabled or enabled afterward**. Existing standard Blob accounts cannot be converted (you must create a new account).

**Rule:** HNS is permanent at account creation.

---

## Trap 19 — Azure Databricks = Stream Analytics for Apache Spark

**Trap:** Stream Analytics processes streaming data — and the scenario mentions Spark. Aren't they the same?

**Reality:** Azure Stream Analytics uses a SQL-based DSL, not Apache Spark. Azure Databricks is the managed Apache Spark service. HDInsight can run Spark, but it is not managed in the same way.

**Rule:** "Managed Apache Spark" = Azure Databricks. "Real-time SQL-based stream processing" = Stream Analytics.

---

## Trap 20 — ADF Pipeline = Dataset

**Trap:** A Dataset contains the data — it's where you "combine" things.

**Reality:** Dataset is a data pointer/reference (table name, file path, query). A **Pipeline** is the orchestration container that groups and sequences activities with dependencies.

**Rule:** "Combine multiple activities" = Pipeline. "Reference a data location" = Dataset.

---

## Trap 21 — Point-to-Site VPN minimises latency

**Trap:** VPN sounds like a direct connection. Surely it can minimise latency to Azure?

**Reality:** P2S and S2S VPN both travel over the **public internet** with variable, unpredictable latency.

**Rule:** Minimise latency = ExpressRoute (private circuit, no internet). Any VPN = public internet.

---

## Trap 22 — Functions Consumption plan has no limits

**Trap:** Serverless means infinitely scalable with no constraints.

**Reality:** Functions Consumption plan has a **10-minute default timeout** and **60-minute maximum timeout**. For workloads requiring longer execution, use Premium or Dedicated plans.

**Rule:** Long-running = Functions Premium or Dedicated plan (or Azure Batch for HPC).

---

## Trap 23 — Azure Bastion requires an open RDP port

**Trap:** To RDP, port 3389 must be open in the NSG.

**Reality:** Azure Bastion tunnels RDP over **HTTPS (port 443)** from the browser via the Azure portal. Port 3389 is never opened — the VM does not need a public IP and the NSG never exposes RDP directly.

**Rule:** Bastion = no public IP, no port 3389. RDP tunnelled over HTTPS.

---

## Trap 24 — GRS satisfies regional compliance requirements

**Trap:** GRS is "redundant" — surely it meets a redundancy compliance requirement?

**Reality:** GRS replicates to a **paired secondary region** outside the compliance boundary. Any policy requiring data to stay in a single region eliminates GRS and GZRS.

**Rule:** Regional compliance → eliminate GRS and GZRS → choose ZRS for best fault tolerance within the region.

---

## Trap 25 — Availability Set provides 99.99% SLA

**Trap:** HA sounds like 4 nines.

**Reality:** Availability Set provides **99.95% SLA**. Four nines (99.99%) requires Availability Zones.

**Rule:** Set = 99.95%. Zones = 99.99%. Know the exact numbers.

---

## Trap 26 — Service Endpoint = Private Endpoint

**Trap:** Both restrict PaaS access to your VNet — they must do the same thing.

**Reality:** Service Endpoint extends the VNet's identity to the PaaS service, but the public IP of the PaaS remains accessible from outside your VNet (firewall rules can restrict it). Private Endpoint creates a private NIC in your VNet with a private IP — traffic never leaves the VNet and the PaaS service can be made completely private.

**Rule:** Complete private isolation = Private Endpoint. Network-restricted access = Service Endpoint.

---

## Trap 27 — Azure Batch uses Functions-style time limits for HPC

**Trap:** Serverless compute = short tasks. Azure Batch must have similar limits.

**Reality:** Azure Batch is designed for long-running HPC workloads. There is **no maximum execution time limit** on Batch tasks. VMs run jobs for hours or days as needed.

**Rule:** No time limit + large-scale + managed nodes = Azure Batch.

---

## Trap 28 — Azure Monitor is just for metrics

**Trap:** Azure Monitor is the metrics service.

**Reality:** Azure Monitor is an umbrella platform covering Metrics (numerical time-series), Logs (via Log Analytics, KQL), Application Insights (APM), Alerts, and Workbooks.

**Rule:** Azure Monitor = full observability stack (metrics + logs + APM + alerts + dashboards).

---

## Trap 29 — "Minimise costs" means pick the cheapest option in isolation

**Trap:** Cost minimisation always means the cheapest tier.

**Reality:** "Minimise costs" is always a soft constraint applied AFTER hard constraints (storage, protocol, SLA, RPO/RTO) are satisfied. Choose the cheapest option that still passes all hard gates.

**Rule:** Hard constraints first. Minimise cost among options that survive.

---

## Trap 30 — Virtual WAN Basic supports centralised security

**Trap:** Virtual WAN is the answer for centralised security — surely both tiers work?

**Reality:** Virtual WAN **Basic** only supports S2S VPN. Only Virtual WAN **Standard** supports Azure Firewall Manager, inter-hub routing, ExpressRoute, P2S VPN, and NVA deployment.

**Rule:** Central security / Azure Firewall Manager / inter-hub = Virtual WAN Standard.
