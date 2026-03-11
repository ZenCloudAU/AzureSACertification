# ⚡ Domain 3 — Design Business Continuity Solutions

**Exam weight: 15–20%**
**Reference:** https://learn.microsoft.com/en-us/training/paths/design-business-continuity-solutions/

---

## HA Scope Decision Tree

```
What failure must the solution survive?
│
├── Hardware component failure (rack, server, NIC)
│   Datacenter failure is acceptable
│   → Availability Set  (SLA 99.95%)
│
├── Datacenter failure / power outage affecting a physical location
│   Regional failure is acceptable
│   → Availability Zone  (SLA 99.99%)
│
└── Regional failure (entire Azure region)
    → Multi-region architecture + Azure Site Recovery
      or geo-redundant PaaS (SQL geo-replication, Cosmos multi-region)
```

**Exam keyword mapping:**
- "power outage", "physical location", "datacenter failure" = **Availability Zone**
- "hardware component", "rack failure", "datacenter failure acceptable" = **Availability Set**
- "regional failure", "failover to secondary region" = **Azure Site Recovery**

---

## Availability Sets vs Availability Zones

| | Availability Set | Availability Zone |
|---|-----------------|------------------|
| **SLA** | **99.95%** | **99.99%** |
| Scope | Single datacenter | Separate physical datacenters |
| Failure protection | Hardware/rack | Datacenter (power, cooling, network) |
| Fault domains | Up to 3 (separate racks/power) | Physical AZ boundary |
| Update domains | 5–20 (rolling update groups) | N/A |
| Minimum zones/DCs | N/A | **3 zones per region** minimum |
| Cross-zone latency | N/A | <2 ms within same region |
| Compatible with Ultra Disk | ❌ | ✅ |

### Availability Set internals

- **Fault domain:** A rack with independent power and networking. Up to 3 FDs.
- **Update domain:** A group of VMs that get patched at the same time. Up to 20 UDs; no two UDs patched simultaneously.
- All VMs stay within the **same datacenter** — does not protect against datacenter outages.

### Availability Zone internals

- Each zone = a **separate physical datacenter** within the region with independent power, cooling, and networking.
- Minimum **3 zones** per supported region.
- Zone-redundant services (Storage ZRS, SQL BC, etc.) spread replicas across all 3 zones automatically.

---

## RTO and RPO

| Term | Definition | What drives the requirement |
|------|-----------|----------------------------|
| **RTO** | Recovery Time Objective — max acceptable downtime | Drives failover speed and architecture tier |
| **RPO** | Recovery Point Objective — max acceptable data loss | Drives replication frequency and mode |

### Architecture Tiers by RTO/RPO

| Architecture | RTO | RPO | Cost tier |
|-------------|-----|-----|----------|
| Single VM (no HA) | Hours (manual) | Last backup | Lowest |
| Availability Set | Near-zero (LB) | 0 (stateless) | Low |
| Availability Zones | ~seconds | 0 (synchronous) | Medium |
| ASR active-passive | Minutes | ~30 seconds | Medium |
| Active-active multi-region | ~seconds | 0 | Highest |

**Exam trap:** When a scenario states both "RTO < 15 min" AND "minimise costs," the correct answer is the *cheapest architecture that still satisfies the RTO minimum* — not the highest-resilience option.

---

## Azure Site Recovery (ASR)

Orchestrated VM (and physical server) replication and failover to a secondary Azure region or datacenter.

| Attribute | Detail |
|-----------|--------|
| Supported sources | Azure VMs, VMware, Hyper-V, physical servers |
| Replication | Continuous block-level (Azure-to-Azure), or via Azure Site Recovery provider |
| **RPO (Azure VMs)** | **~30 seconds** |
| RTO | Minutes (VM boot time + DNS) |
| Test failover | Isolated VNet — zero production impact |
| Planned failover | No data loss; orchestrated |
| Unplanned failover | Emergency; potential data loss |

### Failover types

- **Test failover:** Validate recovery plan, isolated network, no production disruption
- **Planned failover:** Controlled, zero data loss (replicate fully before switching over)
- **Unplanned failover (failover):** Emergency — small data loss possible

---

## Azure Backup

### Vault Types

| Vault Type | Supported Workloads |
|-----------|---------------------|
| **Recovery Services Vault** | Azure VMs, SQL in VMs, Azure Files, SAP HANA, MABS, DPM |
| **Backup Vault** | Azure Managed Disks, Azure Blobs, Azure Database for PostgreSQL |

### Soft Delete

- Deleted backup data is **retained for 14 days** (VMs) before permanent deletion
- Protects against accidental deletion and ransomware
- Soft delete is enabled by default on all Recovery Services Vaults (2020+)

### Backup Policies

- **Backup frequency:** Hourly (enhanced policy), daily, weekly
- **Retention:** Daily (up to 9,999 days), weekly, monthly, yearly
- **Instant restore:** Keep snapshot locally for 1–5 days for fast recovery (no vault download)

---

## PaaS High Availability

### Azure SQL Database

| HA option | RPO | RTO | Notes |
|-----------|-----|-----|-------|
| Business Critical (in-region) | 0 | **~20–30s** | Always On AG, built-in readable secondary |
| Active geo-replication | **~5s** | ~30s | Up to 4 readable secondaries, any region |
| Auto-failover groups | ~5s | ~30s | DNS-based endpoint, automatic or manual failover |

### Cosmos DB

- **Multi-region writes:** RPO = 0, RTO defined by SLA (~15 min SLA guarantee)
- **Single-region write + multi-region read:** RPO bounded staleness for writes (~5s), reads RPO 0

### Azure Storage (RA-GRS / RA-GZRS)

- Read access to secondary region at all times (RA prefix)
- Failover from primary to secondary is Microsoft-initiated (unless customer-initiated failover enabled)
- After failover, secondary becomes new primary; data may lag by ~replication time

---

## Key Numbers

| Fact | Value |
|------|-------|
| Availability Set SLA | **99.95%** |
| Availability Zone SLA | **99.99%** |
| Single VM (Premium SSD) SLA | **99.9%** |
| ASR RPO (Azure VMs) | **~30 seconds** |
| Fault domains per Availability Set | **Up to 3** |
| Update domains per Availability Set | **5–20** |
| Minimum AZs per supported region | **3** |
| Soft delete retention (VM backup) | **14 days** |
| SQL BC in-region failover | **~20–30 seconds** |
| SQL active geo-replication RPO | **~5 seconds** |
