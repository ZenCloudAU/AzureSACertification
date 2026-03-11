# 🧠 Quiz — Domain 3: Business Continuity Solutions

15 practice questions. Work through each scenario before reading the answer.

---

**Q1.** A company runs 20 VMs in a single Azure region. The VMs must survive a rack-level hardware failure. A datacenter outage is acceptable. SLA must be at least 99.95%. Which availability option should you recommend?

<details>
<summary>Answer</summary>

**Availability Set**

Availability Sets distribute VMs across up to 3 fault domains (separate racks with independent power and networking) and up to 20 update domains. They provide SLA 99.95% and protect against hardware/rack failures within a single datacenter.

Availability Zones protect against full datacenter failures — higher than the stated tolerance. AZs also provide higher SLA (99.99%) but the question's acceptance of datacenter failure rules out the additional cost/complexity of zones.
</details>

---

**Q2.** A company's production application runs on 6 Azure VMs. During a scheduled Azure platform maintenance event last month, all VMs restarted simultaneously, causing an outage. What should you recommend to prevent this?

<details>
<summary>Answer</summary>

**Availability Set with multiple Update Domains**

Availability Sets distribute VMs across update domains (groups patched at separate times). Azure never patches more than one update domain simultaneously, ensuring rolling platform maintenance does not take all VMs offline at once.

Availability Zones address fault isolation, not coordinated platform update domains.
</details>

---

**Q3.** A financial services company requires that their Azure SQL Database workload fails over to a secondary Azure region automatically if the primary region becomes unavailable, using a single readable connection string. What should you implement?

<details>
<summary>Answer</summary>

**Auto-Failover Groups**

Auto-failover groups provide a single read/write listener endpoint and a read-only listener endpoint. Both endpoints remain constant through failover — application connection strings do not change. Supports automatic failover (configurable grace period) or manual failover.

Active geo-replication requires application-side connection string changes to point at the secondary. Auto-failover groups abstract this via a consistent DNS name.
</details>

---

**Q4.** A company needs an RPO of less than 1 minute and an RTO of less than 5 minutes for an Azure VM workload. Which Azure service meets these requirements?

<details>
<summary>Answer</summary>

**Azure Site Recovery (ASR)**

ASR for Azure VM replication achieves RPO of approximately 30 seconds (well under 1 minute). RTO is determined by VM boot time after failover — typically a few minutes, meeting the <5 minute RTO.

Azure Backup has RPO measured in hours (backup frequency) — does not meet <1 minute RPO. Availability Sets protect against rack failures but do not provide cross-region DR.
</details>

---

**Q5.** A web application runs on 4 Azure VMs across 3 Availability Zones in East US 2. During a routine maintenance window, one zone experienced a partial power issue. What happened to the application?

<details>
<summary>Answer</summary>

**The application continued to serve traffic from the VMs in the unaffected zones.**

Availability Zones are physically separate datacenters within the region with independent power, cooling, and networking. A power issue in one zone does not affect VMs in other zones. The 4 VMs distributed across 3 zones means at least 2 zones (3+ VMs) continued operating.

This is the core value of Availability Zones vs Availability Sets. SLA: 99.99%.
</details>

---

**Q6.** You are designing a backup strategy for 50 Azure VMs, Azure Files shares, and a SQL Server running inside a VM. Which Azure Backup vault type should you use?

<details>
<summary>Answer</summary>

**Recovery Services Vault**

Recovery Services Vault supports: Azure VMs ✅ | Azure Files shares ✅ | SQL Server in VM ✅ | SAP HANA in VM ✅ | MABS/DPM ✅.

Backup Vault supports: Azure Managed Disks, Azure Blobs, Azure Database for PostgreSQL. It does not support Azure VMs or SQL Server in VM.
</details>

---

**Q7.** A team accidentally deleted the backup of a production VM two days ago. The vault is configured with soft delete enabled. Can the backup data be recovered?

<details>
<summary>Answer</summary>

**Yes — soft delete retains deleted backup data for 14 days.**

When soft delete is enabled (default on all new Recovery Services Vaults), deleted backup items enter a "soft deleted" state and are retained for 14 days before permanent deletion. Two days after deletion is well within the 14-day window.

To recover: navigate to the vault, find the soft-deleted item, click Undelete, then re-enable protection.
</details>

---

**Q8.** Your disaster recovery design requires that Azure VMs replicated to a secondary region can be tested for recovery without impacting the production environment. How does Azure Site Recovery support this?

<details>
<summary>Answer</summary>

**Test Failover — isolates recovery in a separate VNet with zero production impact**

ASR Test Failover spins up recovered VMs in an isolated test VNet in the secondary region. Production replication continues uninterrupted. The test environment can be validated and then cleaned up. No production traffic is affected.

Planned Failover is a production switch (controlled, zero data loss). Unplanned Failover (Failover) is emergency DR. Test Failover is the correct answer for "test without impacting production."
</details>

---

**Q9.** A manufacturing company defines their RTO as 4 hours and RPO as 24 hours for a non-critical ERP system. The workload runs on a single Azure VM. What is the most cost-effective design meeting these requirements?

<details>
<summary>Answer</summary>

**Azure Backup with daily backup schedule (24-hour RPO) + documented manual recovery procedure (4-hour RTO)**

A daily backup provides RPO of 24 hours. Manual VM restore from a Recovery Services Vault typically completes in 1–3 hours, meeting the 4-hour RTO.

Azure Site Recovery (continuous replication, RPO ~30s) is significantly more expensive and exceeds what the defined requirements call for. Over-engineering increases cost without business benefit.
</details>

---

**Q10.** A company runs a 3-tier web application on Azure VMs (web, app, SQL tiers). They want to automate the sequenced failover of all tiers in the correct order to the secondary region. What ASR feature enables this?

<details>
<summary>Answer</summary>

**Recovery Plans (ASR)**

ASR Recovery Plans allow you to group VMs into ordered failover groups, add pre/post failover scripts (Azure Automation Runbooks), and define dependencies between tiers. You can test and execute a full multi-tier application failover in the correct sequence with a single action.

Without Recovery Plans, each VM must be failed over individually without orchestration.
</details>

---

**Q11.** An Azure SQL Database Business Critical tier workload needs to meet an RPO of 0 seconds for in-region failures. What HA feature of Business Critical satisfies this?

<details>
<summary>Answer</summary>

**Built-in Always On Availability Group (synchronous replication)**

Business Critical uses a local SSD-based Always On AG with 3–4 replicas (synchronous commit). In-region failover has RPO = 0 (no data loss) because all writes are committed synchronously to all replicas before acknowledging to the client.

RTO is approximately 20–30 seconds for automatic failover.
</details>

---

**Q12.** You are designing HA for a stateless web application deployed on Azure App Service. The application must remain available if an entire Azure datacenter fails. What should you recommend?

<details>
<summary>Answer</summary>

**Deploy App Service plans in multiple Availability Zones (Zone Redundant deployment)**

App Service supports zone-redundant deployment for plans with 3+ instances across availability zones in supported regions. This ensures availability if a single datacenter (zone) fails.

For stateless apps, zone-redundant App Service + Azure Front Door or Application Gateway provides both HA and load distribution.
</details>

---

**Q13.** A company uses Azure Backup to protect VMs. They want to ensure that backup data cannot be deleted by a ransomware attack that compromises admin credentials. What Azure Backup feature prevents this?

<details>
<summary>Answer</summary>

**Soft Delete (enabled by default) + Multi-User Authorization (MUA) / Resource Guard**

- **Soft delete:** Deleted backup data is retained for 14 days before permanent deletion. Even if an attacker deletes backups, they remain recoverable.
- **Multi-User Authorization (Resource Guard):** Requires a second authorised user from a different tenant to approve critical operations (delete backup, disable soft delete). Even a compromised global admin cannot bypass this alone.

These two layers together provide robust protection against ransomware-triggered backup deletion.
</details>

---

**Q14.** A business requires that an Azure Cosmos DB account serving a globally distributed application continues to accept writes even if the primary region becomes unavailable. What configuration is required?

<details>
<summary>Answer</summary>

**Multi-region writes (multi-master) enabled**

Cosmos DB multi-region writes allow any configured region to accept write operations simultaneously. If one region fails, writes automatically route to another active write region. RPO approaches 0 for write operations.

Single-region writes (with read replicas) would reject or delay writes if the write region fails until failover completes.
</details>

---

**Q15.** A company restores an Azure VM from a Recovery Services Vault backup taken 6 hours ago. What data is permanently lost?

<details>
<summary>Answer</summary>

**All changes made to the VM in the 6 hours between the backup and the failure — this is the RPO gap.**

Backup-based recovery has RPO = time since last backup. Any writes, configuration changes, or transactions after the backup point are not captured and cannot be recovered from this backup.

This is why workloads with low RPO requirements use continuous replication (ASR, SQL geo-replication) rather than periodic backups.
</details>

---

*Domain 3 complete. Review traps in `resources/exam-traps.md` (Traps 6, 9, 25).*
