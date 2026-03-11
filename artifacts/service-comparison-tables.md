# 📊 Service Comparison Tables — AZ-305 Azure Solutions Architect Expert

Side-by-side matrices for every major service group tested on the exam. Reference before quizzes and during renewal.

---

## Table 1 — Load Balancing Full Matrix

| Service | OSI Layer | Scope | Protocol | WAF | Proxy | Single Hostname | SSL Offload | URL Routing | Use When |
|---------|-----------|-------|---------|-----|-------|----------------|-------------|-------------|---------|
| **Azure Load Balancer** | L4 (TCP/UDP) | Regional | Any | ❌ | ❌ | ❌ | ❌ | ❌ | Internal/external TCP/UDP, no HTTP inspection needed |
| **Application Gateway** | L7 (HTTP/S) | Regional | HTTP/S | ✅ | ✅ | ❌ (regional) | ✅ | ✅ | Single-region web app, URL routing, WAF |
| **Azure Front Door** | L7 (HTTP/S) | Global | HTTP/S | ✅ | ✅ | **✅ Anycast** | ✅ | ✅ | Global HTTP, single hostname, CDN, WAF |
| **Traffic Manager** | DNS | Global | **Any** | ❌ | ❌ | **❌ DNS redirect** | ❌ | ❌ | Global any-protocol, DNS-based routing |

**Exam decision:** Multi-region + HTTP + single hostname = **Front Door**. Multi-region + non-HTTP = **Traffic Manager**.

---

## Table 2 — Azure SQL Service Tiers

| Tier | Model | Max Storage | Failover Speed | Readable Secondary | SQL Agent | Use Case |
|------|-------|-------------|----------------|--------------------|-----------|---------|
| SQL DB — General Purpose | vCore / DTU | **4 TB** | Slowest | ❌ | ❌ | Standard workloads, cost-conscious |
| SQL DB — Business Critical | vCore | **~16 TB** | **~20–30s** | ✅ | ❌ | Mission-critical, in-memory OLTP |
| SQL DB — Hyperscale | vCore | **128 TB** | Seconds (HA replica) | ✅ | ❌ | Very large DB, fast backup/restore |
| SQL MI — General Purpose | vCore | **16 TB** | Standard | ❌ | **✅** | Instance features + standard HA |
| SQL MI — Business Critical | vCore | **16 TB** | **~20–30s** | ✅ | **✅** | Instance features + highest HA |

---

## Table 3 — Messaging Services

| Service | Pattern | Consumers | Message Size | Ordering | Dead-Letter | Replay | Best For |
|---------|---------|-----------|-------------|---------|-------------|--------|---------|
| **Storage Queue** | Queue (P2P) | Competing | 64 KB | No guarantee | ❌ | ❌ | Cheap, simple task queue |
| **Service Bus Queue** | Queue (P2P) | Competing | 256 KB (Std) / 100 MB (Prem) | Sessions | ✅ | ❌ | Reliable ordered processing |
| **Service Bus Topics** | Pub/Sub | **Each sub gets own copy** | 256 KB / 100 MB | Per subscription | ✅ | ❌ | Multiple independent consumers |
| **Event Hubs** | Streaming | Consumer groups (parallel) | 1 MB | Partition-level | ❌ | **✅ Replay** | High-volume telemetry, IoT, logs |
| **Event Grid** | Event routing | Push to handlers | 1 MB | No | ❌ | ❌ | Reactive microservices, serverless triggers |
| **IoT Hub** | Device messaging | Service + device | 256 KB | Per device | ✅ | ❌ | IoT device management + messaging |

---

## Table 4 — Storage Redundancy

| Option | Copies | Primary Zones | Secondary Region | Read Secondary | Annual Durability | Best For |
|--------|--------|--------------|-----------------|----------------|------------------|---------|
| LRS | 3 | 1 DC | ❌ | ❌ | 11 nines | Dev/test, cheapest |
| ZRS | 3 | **3 zones** | ❌ | ❌ | 12 nines | Regional compliance + zone HA |
| GRS | 6 | 1 DC | ✅ (read after failover) | ❌ | 16 nines | Geo-redundancy, lower cost |
| GZRS | 6 | **3 zones** | ✅ (read after failover) | ❌ | 16 nines | Zone HA + geo-redundancy |
| RA-GRS | 6 | 1 DC | ✅ **always** | ✅ | 16 nines | Read-heavy, needs secondary reads |
| RA-GZRS | 6 | **3 zones** | ✅ **always** | ✅ | 16 nines | Highest availability + reads |

**Regional compliance gate:** Eliminates GRS, GZRS, RA-GRS, RA-GZRS. Only LRS or ZRS remain.

---

## Table 5 — Compute Services

| Service | Workload Type | Time Limit | VNet Integration | Container | Admin Overhead | Cost Model |
|---------|--------------|-----------|-----------------|-----------|---------------|------------|
| **Virtual Machine** | Any (full control) | None | ✅ | ❌ | Highest | Hourly |
| **App Service** | Web app, REST API | None | ✅ (Premium) | Optional | Low | Plan/hour |
| **Azure Functions (Consumption)** | Event-driven, short tasks | **60 min max** | ❌ | ❌ | Minimal | Per execution |
| **Azure Functions (Premium)** | Event-driven, consistent | Unlimited | ✅ | ❌ | Minimal | Monthly |
| **ACI** | Single container, short-lived | None | ✅ | ✅ | Low | Per second |
| **AKS** | Container orchestration | None | ✅ | ✅ | High | Node pool/hour |
| **Azure Batch** | HPC, parallel, large-scale | **None** | ✅ | Optional | Minimal (managed) | Per VM/second |
| **Azure Databricks** | Spark ETL, ML, streaming | None | ✅ | ❌ | Medium | DBU/hour |

---

## Table 6 — On-Premises to Azure Connectivity

| Method | Path | Encrypted | Latency | Max Bandwidth | Best For |
|--------|------|-----------|---------|--------------|---------|
| **P2S VPN** | Public internet | ✅ (IKEv2/SSTP) | Variable | ~10 Gbps | Remote individual users |
| **S2S VPN** | Public internet | ✅ (IPSec) | Variable | ~10 Gbps | Branch to Azure (smaller) |
| **ExpressRoute** | **Private circuit** | **❌ No** | **Lowest, consistent** | **100 Gbps** | Enterprise, latency-sensitive |
| **ER + IPSec** | Private circuit | ✅ | Lowest | 100 Gbps | Encrypted + low latency |
| **Virtual WAN Std** | MS backbone | ✅ (per tunnel) | Low | 100 Gbps | Global multi-branch automated |

---

## Table 7 — HA Architecture Comparison

| Architecture | Failure Scope | SLA | Cost | RTO |
|-------------|--------------|-----|------|-----|
| Single VM (Premium SSD) | No planned | 99.9% | Lowest | Hours (manual) |
| Availability Set | Rack / hardware | 99.95% | Low | Near-zero |
| Availability Zone | Datacenter | 99.99% | Medium | ~Seconds |
| Multi-region active-passive (ASR) | Region | ~99.99%+ | High | Minutes |
| Multi-region active-active | Region | ~99.99%+ | Highest | ~Seconds |

---

## Table 8 — Non-Relational Storage

| Service | Model | SLA | Global Dist | Auto-index | HNS/HDFS | Best For |
|---------|-------|-----|-------------|-----------|----------|---------|
| Azure Table Storage | Key-value | **99.9%** | ❌ | Partial | ❌ | Cheap structured NoSQL |
| Cosmos DB for Table | Key-value | **99.99%** | ✅ | ✅ | ❌ | High-SLA structured NoSQL |
| Cosmos DB Core (SQL) | Document | 99.99% | ✅ | ✅ | ❌ | JSON documents, flexible queries |
| Cosmos DB MongoDB | Document | 99.99% | ✅ | ✅ | ❌ | MongoDB-compatible migration |
| Cosmos DB Cassandra | Wide-column | 99.99% | ✅ | ✅ | ❌ | Cassandra-compatible migration |
| Cosmos DB Gremlin | Graph | 99.99% | ✅ | ✅ | ❌ | Graph/relationship traversal |
| Azure Blob Storage | Object | 99.9% | ❌ | ❌ | ❌ | Unstructured files, images, media |
| ADLS Gen2 | Object + HNS | 99.9% | ❌ | ❌ | **✅** | Big data, Spark/Hadoop analytics |

---

## Table 9 — Azure Files: Standard vs Premium

| Feature | Standard (GPv2) | Premium (FileStorage) |
|---------|----------------|-----------------------|
| SMB 2.1 / 3.0 | ✅ | ✅ |
| **NFS 4.1** | **❌** | **✅ Only on Premium** |
| LRS / ZRS | ✅ | ✅ |
| GRS / RA-GRS | ✅ | ❌ |
| **GZRS / RA-GZRS** | **✅** | **❌** |
| Max share size | 100 TiB | 100 TiB |
| IOPS | ~1,000–10,000 | Up to 100,000 |
| Latency | Single-digit ms | Sub-ms |
| Large file shares (100 TiB) | Opt-in | Default |
| Private Endpoint | ✅ | ✅ |

---

## Table 10 — Analytics Services

| Service | Engine | Query Language | Latency Mode | Managed | Best For |
|---------|--------|---------------|-------------|---------|---------|
| **Azure Databricks** | **Apache Spark** | Python/Scala/SQL/R | Batch + Streaming | ✅ Managed | Big data ETL, ML, Spark streaming |
| **Stream Analytics** | Microsoft DSL | **SQL-based** | **Real-time (<1s)** | ✅ Managed | IoT, telemetry, continuous queries |
| **Azure Synapse** | SQL + Spark | T-SQL + Spark | Batch | ✅ Managed | Unified analytics platform (DW + Spark + ADF) |
| **HDInsight** | Hadoop/Spark/Kafka | Multiple | Batch | Partial | Custom open-source, Hadoop ecosystem |
| **Data Explorer (ADX)** | Kusto | **KQL** | Near-real-time | ✅ Managed | Log analytics, time-series, exploration |
| **Analysis Services** | SSAS Tabular | DAX / MDX | Batch (refresh) | ✅ Managed | BI semantic model, Power BI layer |
