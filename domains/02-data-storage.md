# 🗄️ Domain 2 — Design Data Storage Solutions

**Exam weight: 20–25%**
**Reference:** https://learn.microsoft.com/en-us/training/paths/design-data-storage-solutions/

---

## Relational Data — Azure SQL

### Azure SQL Database vs SQL Managed Instance

The most important split in this domain. One question to ask first: **does the workload need instance-scoped features?**

| Instance-scoped feature | SQL DB | SQL MI |
|------------------------|--------|--------|
| SQL Server Agent | ❌ | ✅ |
| CLR (Common Language Runtime) | ❌ | ✅ |
| Linked servers | ❌ | ✅ |
| Service Broker | ❌ | ✅ |
| Cross-database queries | ❌ | ✅ |
| Database Mail | ❌ | ✅ |
| Windows Authentication | ❌ | ✅ |

**Architect rule:** ANY instance-scoped feature → answer is always **SQL Managed Instance**. All Azure SQL Database tiers are eliminated regardless of other attributes.

### Azure SQL Database Service Tiers

| Tier | Max Storage | Failover Speed | Readable Secondary | Use Case |
|------|-------------|----------------|--------------------|---------|
| General Purpose (GP) | **4 TB** | Slowest (remote storage) | ❌ | Standard workloads |
| Business Critical (BC) | ~16 TB | **Fastest ~20–30s** (Always On AG) | ✅ | Mission-critical, sub-second reads |
| Hyperscale | **128 TB** | Fast with HA replica (seconds) | ✅ (HA replica) | Very large DBs, fast backups |

### Two-Gate Decision Model (critical exam pattern)

When a question gives **both** a storage size AND a performance/speed attribute:

1. **Gate 1 — Hard constraint:** Apply storage ceiling. Eliminate tiers that cannot physically host the database.
2. **Gate 2 — Soft attribute:** Among survivors, select the best performer.

```
Example: "40 TB database, fastest failover"

Gate 1 (storage):
  GP:  max 4 TB  ← ELIMINATED
  BC:  max ~16 TB ← ELIMINATED
  HS:  max 128 TB ← PASSES

Gate 2 (failover among survivors):
  Hyperscale with HA replica = seconds ← ANSWER
```

**Trap:** Business Critical is deeply associated with "fastest failover" in Azure SQL. The exam exploits this. Always check storage gate first.

### Scaling Patterns

| Pattern | Solves | Does NOT solve |
|---------|--------|----------------|
| **Sharding** | Exceed per-DB storage ceiling | Cost efficiency, read throughput alone |
| Elastic pools | Cost efficiency across variable workloads | Per-DB storage ceiling |
| Read scale-out | Read throughput | Storage capacity |
| Hyperscale tier | Single-DB storage up to 128 TB | Need to partition horizontally |

**"Exceed maximum storage capacity of a single database" = sharding** — horizontal partitioning across multiple independent databases by shard key.

### SQL Managed Instance Tiers

| Tier | Storage | HA model | Use case |
|------|---------|---------|---------|
| General Purpose | Up to 16 TB | Remote storage, single replica | Instance features + standard HA |
| Business Critical | Up to 16 TB | Local SSD, Always On AG, 3 replicas | Instance features + highest HA |

---

## Non-Relational Data

### Azure Table Storage vs Cosmos DB for Table

| Feature | Azure Table Storage | Cosmos DB for Table |
|---------|--------------------|--------------------|
| **SLA** | **99.9%** | **99.99%** (single region) |
| Schemeless | ✅ | ✅ |
| Consumption pricing | ✅ | ✅ |
| Secondary replicas | ✅ (GRS/GZRS) | ✅ (multi-region) |
| Global distribution | ❌ | ✅ |
| Latency guarantee | None | <10ms reads / <15ms writes |
| Automatic indexing | Partial | ✅ all properties |

**Exam signal:** All four MCQ options for this comparison offer schemeless, consumption pricing, and replicas — they're shared features. **The SLA gap is the only differentiator.** 99.99% SLA required → Cosmos DB for Table.

### Azure Disk Storage Tiers

| Tier | Max IOPS | Max Throughput | Latency | Use Case |
|------|----------|----------------|---------|---------|
| Standard HDD | ~2,000 | ~500 MB/s | Variable (ms) | Dev/test, cold backup |
| Standard SSD | ~6,000 | ~750 MB/s | Single-digit ms | Lightly loaded production |
| **Premium SSD** | **~20,000** | **~900 MB/s** | <1 ms | Production, high-throughput |
| Ultra Disk | 160,000 | 4,000 MB/s | Sub-ms | Mission-critical, SAP HANA |

**Decision model:** Find the **lowest tier** that clears ALL stated IOPS and throughput thresholds → cost minimisation drives the rest.

### Azure Files — Standard vs Premium

| Feature | Standard (GPv2) | Premium (FileStorage) |
|---------|----------------|-----------------------|
| SMB protocol | ✅ | ✅ |
| **NFS protocol** | ❌ | **✅ Premium only** |
| Private endpoints | ✅ | ✅ |
| LRS / ZRS | ✅ | ✅ |
| GRS | ✅ | ❌ |
| **GZRS** | **✅** | **❌ Not supported** |
| Max share size | 100 TiB (soft) | 100 TiB |
| IOPS/throughput | Lower | Higher |

**Key exam traps:**
- NFS requires premium → standard eliminated
- GZRS requires standard → premium eliminated
- Premium trades geo-redundancy for performance (not "more features")

### Azure Storage Redundancy

| Option | Copies | Zones covered | Regions | SLA |
|--------|--------|--------------|---------|-----|
| LRS | 3 | 1 zone, 1 datacenter | 1 | 99.9% |
| ZRS | 3 | 3 zones | 1 | 99.9% |
| GRS | 6 | 1 zone primary + 1 secondary | 2 | 99.9% |
| GZRS | 6 | 3 zones primary + 1 secondary | 2 | 99.9% |
| RA-GRS | 6 | 1 zone primary + read secondary | 2 | 99.99% read |
| RA-GZRS | 6 | 3 zones primary + read secondary | 2 | 99.99% read |

**Fault tolerance hierarchy:** LRS < ZRS < GRS < GZRS

**Regional compliance gate (exam pattern):** Policy requires data remain in a single region → GRS and GZRS are immediately eliminated (both replicate to secondary region). Among remaining options (LRS, ZRS), choose ZRS for highest fault tolerance.

### Customer-Managed Keys (CMK)

Default: Microsoft-managed keys (transparent, automatic). CMK gives customer control of the encryption key.

**CMK requires:** Azure Key Vault (or Managed HSM) to store and manage the key + Managed Identity on the storage account.

| | Azure Key Vault | Managed HSM |
|-|----------------|-------------|
| FIPS level | 140-2 Level 2 | **140-2 Level 3** |
| Use case | Standard CMK | Regulated industries, highest assurance |

**Distractors and why they're wrong:**
- Private Link → network access control, not encryption
- Service Endpoints → network security, not key management
- SAS tokens → access delegation, not encryption

### Azure Data Lake Storage Gen2

**ADLS Gen2 = Azure Blob Storage + Hierarchical Namespace (HNS)**

HNS enables:
- Atomic directory operations (rename, delete at directory level — not object-by-object)
- POSIX-compliant ACLs at file and directory level
- **HDFS-compatible access via the ABFS driver** (Azure Blob File System)
- Petabyte scale with directory-level permission model

**Three exam signals for ADLS Gen2:** JSON/CSV object storage + hierarchical namespace + HDFS compatibility.

⚠️ HNS is set at account creation — **cannot be disabled afterward**.

**Medallion architecture (foundation):**
```
Ingestion → ADLS Gen2 Bronze/raw  (cheapest, schema-on-read)
          → Databricks/Synapse Silver/curated
          → SQL/Synapse/Power BI Gold/serving
```

---

## Key Numbers

| Fact | Value |
|------|-------|
| SQL DB General Purpose max storage | **4 TB** |
| SQL DB Business Critical max storage | **~16 TB** |
| SQL DB Hyperscale max storage | **128 TB** |
| SQL MI max storage | **16 TB** |
| Business Critical failover | **~20–30 seconds** |
| Hyperscale failover (HA replica) | **seconds** |
| Azure Table Storage SLA | **99.9%** |
| Cosmos DB for Table SLA | **99.99%** (single region) |
| Premium SSD max IOPS | **~20,000** per disk |
| Ultra Disk max IOPS | **160,000** per disk |
| NFS on Azure Files | **Premium only** |
| GZRS on Azure Files | **Standard only** |
| ADLS Gen2 HNS | **Cannot be disabled** post-creation |
