# 🏗️ Domain 4 — Design Infrastructure Solutions

**Exam weight: 30–35% ← highest weight**
**Reference:** https://learn.microsoft.com/en-us/training/paths/design-infrastructure-solutions/

---

## Compute

### Service Selection Framework

| Service | Trigger keywords | Key constraint | Admin overhead |
|---------|-----------------|----------------|----------------|
| **Azure Batch** | Large-scale parallel, HPC, rendering, simulation, video encoding | No execution time limit | Minimal (managed) |
| **Azure Functions (Consumption)** | Infrequent, event-driven, HTTP trigger, min cost | **60 min max** (10 min default) | Minimal |
| **Azure Container Instances (ACI)** | Container, fast start, short-lived, no orchestration | Stateless preferred | Low |
| **Azure App Service** | Web app, REST API, managed PaaS | No HPC, no container orchestration | Low |
| **AKS** | Container orchestration at scale, microservices | Complex setup | High |
| **Azure Virtual Machines** | Full OS control, lift-and-shift, custom software | Permanent VNet assignment | Maximum |
| **Azure Databricks** | Managed Apache Spark, big data ETL, ML | Cluster startup time | Medium |

### Azure Batch

Managed HPC-scale parallel job processing. Automatically provisions and tears down compute nodes, schedules tasks, retries failures, and auto-scales.

**Use cases:** 3D rendering, financial risk simulation, media transcoding, scientific modelling.

**"Large-scale parallel + minimise administrative effort" = Azure Batch.** Functions has time limits. VMs need full manual management.

### Azure Functions — Plan Comparison

| Plan | Billing | Cold start | VNet integration | Best for |
|------|---------|------------|-----------------|---------|
| **Consumption** | Per execution | Yes | ❌ | Infrequent, bursty, cost-critical |
| Premium | Monthly | No (always warm) | ✅ | Consistent traffic + VNet required |
| Dedicated (App Service) | Monthly (shared) | No | ✅ | Predictable, co-located existing ASP |

**Consumption plan limits:** Default timeout 10 minutes, max timeout 60 minutes.

### Azure Container Instances — Volume Mounts

| Volume type | Multi-container mount | Persistent | Use case |
|------------|----------------------|------------|----------|
| **Azure Files** | ✅ Simultaneous | ✅ | Shared persistent state between containers |
| Azure Disk | ❌ ReadWriteOnce | ✅ | Single-container persistent |
| gitRepo | ❌ | Read-only snapshot | Code/config injection |
| emptyDir | ❌ | ❌ Ephemeral | Temp scratch space (lost on restart) |

**"Two containers needing simultaneous access" = Azure Files.** Azure Disk is single-mount only.

### VNet-First Design Principle

A VM's VNet assignment is **permanent** — changing it post-deployment requires full redeployment. Design network topology first; everything else can be changed post-deployment.

**VM design order:** VNet (permanent) → Availability (zones/sets) → VM size → Storage → Naming

---

## Networking

### Global Load Balancing — Front Door vs Traffic Manager

| Feature | Azure Front Door | Azure Traffic Manager |
|---------|-----------------|----------------------|
| OSI layer | **Layer 7 (HTTP/HTTPS)** | DNS-based (protocol-agnostic) |
| **Single hostname** | **✅ Anycast global IP** | **❌ DNS redirects to regional** |
| Reverse proxy | ✅ | ❌ |
| WAF | ✅ | ❌ |
| SSL/TLS offload | ✅ | ❌ |
| Caching / CDN | ✅ | ❌ |
| Latency routing | ✅ | ✅ |
| Failover routing | ✅ | ✅ |
| Non-HTTP protocols | ❌ | ✅ |

**"Single hostname + HTTP + global latency routing" = Azure Front Door.**
Traffic Manager is DNS-based: the client ultimately connects to a regional endpoint (hostname changes per region, so "single hostname" is violated).

### Full Load Balancing Matrix

| Service | Layer | Scope | Protocol | WAF | Key differentiator |
|---------|-------|-------|---------|-----|--------------------|
| Azure Load Balancer | 4 (TCP/UDP) | Regional | Any | ❌ | Internal/external, pure TCP/UDP |
| Application Gateway | 7 (HTTP) | Regional | HTTP/S | ✅ | URL-based routing, SSL offload |
| **Azure Front Door** | **7 (HTTP)** | **Global** | HTTP/S | ✅ | **Single hostname**, anycast, CDN |
| **Traffic Manager** | DNS | **Global** | **Any** | ❌ | Protocol-agnostic, DNS redirect |

**Rule:** Multi-region + HTTP + single hostname → Front Door. Multi-region + any protocol + no proxy → Traffic Manager. Single-region + HTTP → App Gateway. Single-region + TCP → Load Balancer.

### On-Premises Connectivity

| Method | Path | Latency | Encrypted by default | Max bandwidth |
|--------|------|---------|---------------------|---------------|
| P2S VPN | Public internet | Variable | ✅ (IPSec/TLS) | ~10 Gbps |
| S2S VPN | Public internet | Variable | ✅ (IPSec) | ~10 Gbps |
| **ExpressRoute** | **Private circuit** | **Lowest, consistent** | **❌ No** | **100 Gbps** |

**"Minimise latency" = ExpressRoute** — the only option that bypasses the public internet entirely.

⚠️ ExpressRoute does NOT encrypt traffic by default (private circuit ≠ encrypted). Add IPSec over ExpressRoute for encryption + low latency.

### NAT Gateway

Controlled, predictable outbound internet connectivity for subnet resources.

- Provides SNAT using a defined pool of public IP addresses
- **Outbound-only** — inbound connections blocked by design (no inbound path)
- Up to **16 public IPs** per gateway (~64,512 SNAT ports per IP → ~1M concurrent outbound)
- Scales automatically, no management overhead
- Attached at subnet level

**"Pool of public IPs, outbound only, inbound prevented" = NAT Gateway**

### Azure Virtual WAN

Managed global WAN replacing SD-WAN and hub-and-spoke at scale.

**Three requirements that point to Virtual WAN Standard:**
1. **Automated branch connectivity** (branch-to-Azure and branch-to-branch)
2. **Centralised security control** (Secured Virtual Hub + Azure Firewall Manager)
3. **Microsoft global backbone** for inter-office traffic

| Tier | Capabilities |
|------|-------------|
| Basic | S2S VPN only |
| Standard | S2S VPN, P2S VPN, ExpressRoute, inter-hub routing, Azure Firewall, NVA support |

### User Defined Routes (UDR)

Override Azure's default system routing to force traffic through a specific next hop.

**Why needed for NVA inspection:** Azure routes between subnets directly by default — traffic bypasses any inline firewall. UDRs override this by setting the NVA's private IP as the next hop for inter-subnet traffic.

UDR next-hop types: **Virtual appliance** (NVA/firewall) | Virtual network gateway | VNet | Internet | None (drop)

⚠️ Also enable **IP forwarding** on the NVA NIC to allow forwarding of packets not addressed to it.

### Azure Bastion

Browser-based RDP/SSH to Azure VMs without public IP or open management ports.

- Deployed in a dedicated **AzureBastionSubnet** (/27 minimum — must be named exactly `AzureBastionSubnet`)
- RDP/SSH via Azure portal over HTTPS (port 443)
- No public IP on VM, no port 3389 or 22 exposed in NSGs
- SKU tiers: Basic (browser only) vs Standard (native client, file transfer, shareable links)

**"RDP/SSH + minimise internet exposure + no public IP" = Azure Bastion**

---

## Application Architecture

### Azure Cache for Redis

In-memory data store for caching (cache-aside), session state, leaderboards, pub/sub.

**Cache-aside pattern:** App → check Redis → cache hit: return instantly → cache miss: query SQL DB → write to Redis → return. Dramatically reduces DB load for read-heavy dynamic queries.

**"Dynamic SQL data retrieval performance" = Redis.** CDN = static content only. HPC Cache = NFS/HPC. ExpressRoute = on-premises connectivity.

### Messaging Patterns

| Pattern | Service | Consumer model | Delivery guarantee | Use case |
|---------|---------|----------------|-------------------|---------|
| Simple queue | **Azure Queue Storage** | One consumer at a time | At-least-once | Cheap, basic task distribution |
| Reliable queue | **Service Bus Queue** | Competing consumers | At-least-once, exactly-once | Reliable ordered processing |
| **Pub/sub** | **Service Bus Topics** | **Each subscriber = own copy** | At-least-once per subscription | Multiple independent consumers |
| Streaming | **Event Hubs** | Consumer groups (retain/replay) | N/A (log-based) | High-volume telemetry/events |
| Event-driven | **Event Grid** | Push to handlers | At-least-once | Reactive microservices, serverless triggers |

**"Multiple destinations each receiving their own independent copy" = Service Bus Topics**

Service Bus Premium: 80 GB storage per entity, 100 MB max message, VNet integration, geo-disaster recovery, dedicated capacity.

### Azure API Management (APIM)

APIM exposes and manages **API endpoints** — it does not register servers or applications.

**Object hierarchy:** API → Operation → Product (bundle) → Subscription (access key)

**Exam calculation trap:** Count APIs, not servers. "App1: 30 APIs on 5 servers + App2: 40 APIs on 10 servers" = **70 APIs to publish** (30+40; server count irrelevant).

---

## Data Integration

### Event Hubs Capture

Native feature that writes ingested events automatically to persistent storage in **Apache Avro format**.

- **Capture targets:** Azure Blob Storage ✅ / Azure Data Lake Storage Gen2 ✅ (no other targets)
- **Trigger:** Time window (e.g., every 5 min) OR size window (e.g., every 300 MB)
- **Cost principle:** Raw streaming data → Blob/ADLS (cheapest). Not SQL or Cosmos DB (expensive at sensor scale).

### Azure Data Factory Components

| Component | Role | Analogy |
|-----------|------|---------|
| **Linked Service** | Connection definition to a source/sink | Connection string |
| **Dataset** | Data structure/location pointer | Table/file reference |
| **Activity** | Single action (Copy, Mapping DF, Stored Proc, Notebook) | One task |
| **Pipeline** | Groups and sequences activities with dependencies | Workflow / orchestration |
| **Integration Runtime** | Compute engine that executes activities | Execution engine |
| **Trigger** | Starts a pipeline (Schedule / Event / Tumbling Window) | Cron job |

**"Combine multiple activities" = Pipeline** — not dataset, not linked service.

### Azure Analytics Services

| Service | Engine | Latency mode | Primary use case |
|---------|--------|-------------|-----------------|
| **Azure Databricks** | **Apache Spark** | Batch + near-real-time | Big data ETL, ML, streaming |
| Azure Stream Analytics | SQL DSL | **Real-time** (<1s) | IoT, telemetry stream processing |
| Azure Synapse Analytics | SQL + Spark + Pipelines | Batch | Unified analytics platform |
| Azure Data Explorer | KQL / Kusto | Near-real-time | Logs, telemetry, time-series exploration |
| Azure Analysis Services | SSAS tabular/OLAP | Batch (refresh cycle) | BI semantic model layer |
| Azure HDInsight | Open-source managed (Hadoop, Kafka, Spark) | Batch | Custom Spark/Hadoop clusters |

**"Managed Apache Spark" = Azure Databricks** — no other option in the choice set is a managed Spark runtime.

---

## Key Numbers

| Fact | Value |
|------|-------|
| Functions default timeout (consumption) | **10 minutes** |
| Functions max timeout (consumption) | **60 minutes** |
| NAT Gateway max public IPs | **16** (~1M concurrent connections) |
| AzureBastionSubnet minimum size | **/27** |
| ExpressRoute max bandwidth | **100 Gbps** |
| ExpressRoute encryption | **None by default** |
| VNet peering | **Non-transitive** |
| Service Bus Premium storage per entity | **80 GB** |
| APIM unit of registration | **APIs** (not servers) |
| Event Hubs Capture targets | **Blob Storage or ADLS Gen2 only** |
| Event Hubs Capture format | **Apache Avro** |
| Virtual WAN Standard: supports Firewall | ✅ |
| Virtual WAN Basic: supports only | S2S VPN |
