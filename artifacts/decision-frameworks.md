# 🧭 Decision Frameworks — AZ-305 Azure Solutions Architect Expert

Architect elimination trees for all key service-selection scenarios on the exam. Apply hard constraints first — eliminate before optimising.

---

## Framework 1 — Azure SQL Service Selection

```
Does the workload need INSTANCE-SCOPED features?
(SQL Server Agent / CLR / Linked Servers / Service Broker / cross-DB queries)
│
├── YES → Azure SQL Managed Instance (GP or BC tier)
│          ↓
│          Highest HA / fastest failover needed?
│          ├── YES → SQL MI Business Critical
│          └── NO  → SQL MI General Purpose
│
└── NO  → Azure SQL Database
           ↓
           GATE 1: How large is the database?
           ├── ≤ 4 TB → General Purpose or Business Critical eligible
           ├── 4–16 TB → Business Critical eligible
           └── > 16 TB → ONLY Hyperscale (128 TB max)
                          ↓
           GATE 2: What performance/HA attribute is needed? (among survivors)
           ├── Fastest failover → Business Critical (~20–30s)
           ├── Readable secondary + scale-out reads → Business Critical or Hyperscale
           ├── Very large DB + fast backup/restore → Hyperscale
           └── Standard workload, cost-conscious → General Purpose
```

**Critical trap:** BC = "fastest failover" is the exam's most common distractor. Storage gate runs first. 40 TB eliminates GP and BC before failover speed even matters.

---

## Framework 2 — Global Load Balancing

```
What protocol does the workload use?
│
├── HTTP / HTTPS only
│   ↓
│   Does the scenario require a SINGLE HOSTNAME globally?
│   ├── YES → Azure Front Door
│   │         (anycast reverse proxy, one hostname, WAF, SSL offload, CDN)
│   └── NO  → Azure Front Door (still recommended for L7 global)
│             OR Traffic Manager with URL-based policy
│
└── ANY protocol (TCP, UDP, non-HTTP)
    → Azure Traffic Manager
      (DNS-based routing, no proxy, latency/failover/priority/weighted modes)
```

**Critical trap:** Traffic Manager supports latency routing globally, so it matches 2 of 3 requirements in a "single hostname + HTTP + global latency" question. The tiebreaker is always "single hostname" — Front Door wins.

---

## Framework 3 — Regional vs Global Load Balancing

```
Single region or multi-region?
│
├── SINGLE REGION
│   ↓
│   HTTP/HTTPS?
│   ├── YES → Application Gateway (L7, URL routing, WAF, session affinity)
│   └── NO  → Azure Load Balancer (L4, TCP/UDP, internal or public)
│
└── MULTI-REGION
    ↓
    HTTP/HTTPS + single hostname?
    ├── YES → Azure Front Door
    └── NO  → Azure Traffic Manager (any protocol, DNS-based)
```

---

## Framework 4 — Storage Redundancy

```
Is there a regional data-residency compliance requirement?
(data must stay within one Azure region)
│
├── YES → Eliminate GRS, GZRS, RA-GRS, RA-GZRS (all replicate to secondary region)
│         Remaining options: LRS or ZRS
│         ↓
│         Need to survive datacenter/zone failure?
│         ├── YES → ZRS (3 zones in region, survives datacenter failure)
│         └── NO  → LRS (cheapest, single datacenter)
│
└── NO  → All options eligible
          ↓
          Need read access to secondary at all times (not just after failover)?
          ├── YES → RA-GRS or RA-GZRS
          └── NO
              ↓
              Need zone-level fault tolerance in primary AND geo-redundancy?
              ├── YES → GZRS (or RA-GZRS for read access)
              └── NO  → GRS (cheaper, one datacenter in primary region)
```

---

## Framework 5 — Azure Disk Tier Selection

```
Start with stated IOPS requirement:
│
├── ≤ ~2,000 IOPS → Standard HDD sufficient (dev/test only)
├── ≤ ~6,000 IOPS → Standard SSD sufficient
├── ≤ ~20,000 IOPS → Premium SSD sufficient ← most common exam answer
└── > 20,000 IOPS (up to 160,000) → Ultra Disk required

THEN apply throughput check on the surviving tier.
THEN apply cost constraint: lowest tier clearing ALL gates = answer.
```

**Trap:** Ultra Disk is never the answer when Premium SSD meets the stated thresholds — cost minimisation overrides a desire for headroom.

---

## Framework 6 — Azure Files: Standard vs Premium

```
Does the workload require NFS protocol?
├── YES → Premium (FileStorage account) — NFS is Premium-only
└── NO (SMB or REST only)
    ↓
    Does the compliance/DR requirement need geo-redundancy (GZRS)?
    ├── YES → Standard (GPv2) — GZRS only available on Standard
    └── NO  → Either tier eligible; Premium for IOPS-sensitive, Standard for cost
```

---

## Framework 7 — On-Premises Connectivity

```
Primary requirement?
│
├── MINIMISE LATENCY / guaranteed bandwidth
│   → ExpressRoute (private circuit, off public internet, up to 100 Gbps)
│   ⚠️ NOT encrypted by default — add IPSec over ER if needed
│
├── ENCRYPTED tunnel over public internet (acceptable latency)
│   ├── Site-to-site → VPN Gateway S2S
│   └── Remote users → VPN Gateway P2S
│
├── Branch offices: automated connectivity + central security + backbone
│   → Virtual WAN Standard
│
└── Secure browser-based RDP/SSH without public IP
    → Azure Bastion
```

---

## Framework 8 — Messaging Service Selection

```
What is the consumption pattern?
│
├── Single consumer processes each message
│   ├── Need reliability, ordering, dead-letter, sessions → Service Bus Queue
│   └── Cost over features → Azure Storage Queue
│
├── MULTIPLE consumers each need their OWN COPY → Service Bus Topics + Subscriptions
│
├── High-volume event STREAMING (telemetry, logs, clickstream)
│   → Event Hubs (consumer groups, replay, Capture → Blob/ADLS)
│
└── Reactive event routing (trigger Functions, Logic Apps on resource events)
    → Event Grid (push, at-least-once, low latency)
```

---

## Framework 9 — Compute Service Selection

```
Is it HPC / large-scale parallel workload (rendering, simulation, batch jobs)?
├── YES → Azure Batch (no time limit, managed nodes, auto-scale)
└── NO
    ↓
    Is execution time ≤ 10 min (default) or ≤ 60 min (max) and infrequent?
    ├── YES + HTTP trigger + minimise cost → Azure Functions (Consumption plan)
    └── NO
        ↓
        Is it containerised without orchestration?
        ├── YES + shared storage across containers → ACI + Azure Files volume
        ├── YES + short-lived, isolated → ACI
        └── NO
            ↓
            Is it microservices / container orchestration at scale?
            ├── YES → AKS
            └── NO
                ↓
                Web app or REST API (no containers)?
                ├── YES → App Service
                └── Full OS control / lift-and-shift → Virtual Machine
```

---

## Framework 10 — Availability / Business Continuity Scope

```
What failure must the solution survive?
│
├── Hardware component / rack / server failure
│   (datacenter failure acceptable)
│   → Availability Set  [SLA 99.95%]
│     Fault domains: up to 3 (separate racks/power)
│     Update domains: 5–20 (rolling patching)
│
├── Datacenter / power outage affecting a physical location
│   (regional failure acceptable)
│   → Availability Zone  [SLA 99.99%]
│     3 physically separate datacenters within region
│     Cross-zone latency: <2ms
│
└── Regional failure (entire Azure region down)
    → Multi-region architecture
      ├── IaaS: Azure Site Recovery (RPO ~30s, RTO minutes)
      └── PaaS: SQL geo-replication / auto-failover groups
               Cosmos DB multi-region writes
               Azure Storage RA-GRS/RA-GZRS
```

---

## 🔢 Key Numbers Reference Card

> Memorise these. They show up as distractors or in calculation questions.

### Azure SQL

| Tier | Max Storage | Failover (in-region) | Readable Secondary |
|------|-------------|---------------------|-------------------|
| General Purpose | **4 TB** | Slowest (remote storage) | ❌ |
| Business Critical | **~16 TB** | **~20–30 seconds** | ✅ |
| Hyperscale | **128 TB** | Seconds (HA replica) | ✅ |
| SQL MI (GP) | **16 TB** | Standard | ❌ |
| SQL MI (BC) | **16 TB** | ~20–30 seconds | ✅ |

### Availability & SLAs

| Resource | SLA |
|----------|-----|
| Single VM (Premium SSD) | 99.9% |
| Availability Set (2+ VMs) | 99.95% |
| Availability Zones (2+ VMs, 2+ zones) | 99.99% |
| Azure Table Storage | 99.9% |
| Cosmos DB (single region) | 99.99% |
| Azure Files Standard | 99.9% |

### Disk IOPS

| Tier | Max IOPS |
|------|---------|
| Standard HDD | ~2,000 |
| Standard SSD | ~6,000 |
| Premium SSD | **~20,000** |
| Ultra Disk | **160,000** |

### Networking

| Resource | Key number |
|----------|-----------|
| ExpressRoute max bandwidth | **100 Gbps** |
| NAT Gateway max public IPs | **16** |
| AzureBastionSubnet minimum | **/27** |
| VNet peering | Non-transitive |
| RBAC assignments per subscription | **2,000** |
| RBAC assignments per management group | **500** |
| Management group max depth | **6 levels** |

### Functions (Consumption Plan)

| Attribute | Value |
|-----------|-------|
| Default timeout | **10 minutes** |
| Maximum timeout | **60 minutes** |
| Cold start | Yes |
| VNet integration | ❌ |

### Monitoring

| Resource | Retention |
|----------|---------|
| Azure Monitor Metrics (default) | **93 days** |
| Log Analytics (default) | **30 days** (up to 730) |
| Application Insights (default) | **90 days** |

### Messaging

| Service | Max message size | Storage per entity |
|---------|-----------------|-------------------|
| Service Bus Standard | 256 KB | 80 GB |
| Service Bus Premium | **100 MB** | **80 GB** |
| Storage Queue | 64 KB | Unlimited |
| Event Hubs | 1 MB (standard) | Configured retention |

### Business Continuity

| Resource | RPO | RTO |
|----------|-----|-----|
| ASR (Azure VM replication) | **~30 seconds** | Minutes |
| SQL BC in-region failover | **0** (sync) | **~20–30 seconds** |
| SQL active geo-replication | **~5 seconds** | ~30 seconds |
| Backup soft delete retention | **14 days** | N/A |
