# 🧠 Quiz — Domain 2: Data Storage Solutions

15 practice questions. Work through each scenario before reading the answer.

---

**Q1.** A company is migrating a SQL Server database that uses SQL Server Agent jobs for nightly ETL operations. They want a fully managed PaaS solution. Which Azure SQL option should you recommend?

<details>
<summary>Answer</summary>

**Azure SQL Managed Instance**

SQL Server Agent is an instance-scoped feature not available in any tier of Azure SQL Database (General Purpose, Business Critical, Hyperscale). Only SQL Managed Instance supports SQL Server Agent.

This is the first elimination: any instance-scoped feature requirement removes all SQL DB tiers before storage or cost are even considered.
</details>

---

**Q2.** Your team needs to host a 45 TB relational database in Azure. The workload requires the fastest possible in-region failover. Which Azure SQL Database tier should you select?

<details>
<summary>Answer</summary>

**Hyperscale**

**Gate 1 — Storage:** 45 TB exceeds General Purpose (4 TB max) and Business Critical (~16 TB max). Only Hyperscale (128 TB max) passes.

**Gate 2 — Failover (among survivors):** Hyperscale with an HA replica provides failover in seconds.

Business Critical is the "fastest failover" distractor — but it is eliminated at Gate 1. Never skip the storage check.
</details>

---

**Q3.** A customer requires a NoSQL table-based API with 99.99% SLA for a production application. The workload is global with low-volume reads/writes. Which service do you recommend?

<details>
<summary>Answer</summary>

**Azure Cosmos DB for Table**

Azure Table Storage SLA is 99.9%. Cosmos DB for Table SLA is 99.99% (single region). All other attributes — schemeless, pay-per-use, table API — are shared by both services. The SLA requirement is the only differentiator on the exam.

Note: Cosmos DB for Table is drop-in compatible with Azure Table Storage SDKs.
</details>

---

**Q4.** A production VM running SAP HANA requires disk performance of 15,000 IOPS and 400 MB/s throughput. You must minimise cost. Which disk tier should you recommend?

<details>
<summary>Answer</summary>

**Premium SSD**

Premium SSD supports up to ~20,000 IOPS and ~900 MB/s throughput, which clears both the 15,000 IOPS and 400 MB/s thresholds.

Ultra Disk provides up to 160,000 IOPS and 4,000 MB/s — far beyond the stated requirements. Since the question asks to minimise cost, Ultra Disk is eliminated. Premium SSD is the lowest tier clearing all gates.
</details>

---

**Q5.** A media company needs to mount the same shared file share from 5 Linux-based Azure Container Instances simultaneously. High throughput is required. Which Azure Files tier and protocol should you use?

<details>
<summary>Answer</summary>

**Azure Files Premium with NFS 4.1**

NFS protocol is only supported on Azure Files Premium (FileStorage account). Standard Azure Files does not support NFS. For simultaneous multi-mount from ACI instances, Azure Files is required (Azure Disk is single-mount ReadWriteOnce). Premium provides the high IOPS and sub-millisecond latency needed.
</details>

---

**Q6.** Your company operates in the European Union. A compliance audit requires that all Azure Storage data must remain within the EU and must not replicate to any other region. Availability Zone resilience within the primary region is required. Which redundancy option should you select?

<details>
<summary>Answer</summary>

**ZRS (Zone-Redundant Storage)**

Regional compliance eliminates GRS, GZRS, RA-GRS, and RA-GZRS (all replicate to a paired secondary region outside the EU). Between LRS (single datacenter) and ZRS (3 availability zones), ZRS provides availability zone resilience as required.
</details>

---

**Q7.** A development team wants to encrypt their Azure Storage account with their own keys so they can revoke access at any time. Where must the encryption keys be stored?

<details>
<summary>Answer</summary>

**Azure Key Vault (or Azure Managed HSM)**

Customer-Managed Keys (CMK) require the key to be stored in Azure Key Vault or Managed HSM. The storage account uses a Managed Identity to retrieve the key from Key Vault at runtime.

Private Link and Service Endpoints control network access — they are not key management solutions. SAS tokens control access delegation — unrelated to encryption key storage.
</details>

---

**Q8.** A developer asks why they cannot rename a directory containing 10,000 blobs quickly in their Azure Storage account. You know the account was created without Hierarchical Namespace enabled. What explains this limitation and what is the correct service?

<details>
<summary>Answer</summary>

**Standard Blob Storage uses flat namespace — rename is an object-by-object operation. Enable HNS (ADLS Gen2) for atomic directory operations.**

Without HNS, a "directory rename" must copy and delete each blob individually — O(n) operations. ADLS Gen2 with HNS treats directories as first-class objects, so a rename is a single atomic metadata operation regardless of how many objects are inside.

Note: HNS cannot be enabled on an existing account — a new ADLS Gen2 account must be created.
</details>

---

**Q9.** Your organisation needs to store 3 TB of sensor data per day for 90 days for analytics. The data is written once and read infrequently after 30 days. You must minimise storage costs. Which Blob access tier strategy should you implement?

<details>
<summary>Answer</summary>

**Hot tier at ingestion → lifecycle management policy to move to Cool tier after 30 days**

Hot tier is appropriate for active writes and early access. After 30 days, data is infrequently accessed — Cool tier reduces storage costs (at the cost of higher per-read pricing, which is acceptable for "infrequent" access patterns).

Archive tier has a 180-day minimum storage period and requires rehydration (up to 15 hours) before reading — unsuitable if data needs to be queryable within 90 days.
</details>

---

**Q10.** A company is migrating a SQL Server workload that uses cross-database queries, Service Broker, and linked servers to a third-party Oracle database. Which Azure SQL service supports all three features?

<details>
<summary>Answer</summary>

**Azure SQL Managed Instance**

All three are instance-scoped features: cross-database queries, Service Broker, and linked servers. None of these exist in any Azure SQL Database tier.

Note: Linked servers to third-party databases (Oracle, etc.) are supported on SQL MI.
</details>

---

**Q11.** You need to design a storage solution for a data analytics pipeline that reads data using the Hadoop Distributed File System (HDFS) API from Apache Spark clusters in Azure Databricks. Which storage service is required?

<details>
<summary>Answer</summary>

**Azure Data Lake Storage Gen2 (ADLS Gen2)**

ADLS Gen2 provides HDFS-compatible access via the ABFS (Azure Blob File System) driver. This allows Databricks/Spark workloads to read and write using standard HDFS URIs. Standard Blob Storage without HNS does not support HDFS semantics.

The three exam signals for ADLS Gen2: object storage + hierarchical namespace + HDFS/Spark compatibility.
</details>

---

**Q12.** An Azure SQL Database General Purpose workload is growing and will exceed 4 TB within 6 months. Your architect proposes migrating to a Business Critical tier. What is wrong with this recommendation?

<details>
<summary>Answer</summary>

**Business Critical also has a storage ceiling (~16 TB) and does not solve the long-term growth problem. Hyperscale (128 TB max) is the correct tier for a storage-driven migration.**

BC is associated with performance and HA improvements over GP, not a dramatically higher storage ceiling relative to the growth trajectory. For data exceeding the per-DB storage ceiling at any tier, Hyperscale (128 TB) is the appropriate target.

Sharding is an alternative if the workload can be partitioned horizontally.
</details>

---

**Q13.** A company stores millions of small objects in Azure Blob Storage (Standard GPv2). They want to geo-replicate data across regions AND maintain zone-level resilience in the primary region. What is the correct redundancy option?

<details>
<summary>Answer</summary>

**GZRS (Geo-Zone-Redundant Storage)**

GZRS = ZRS in primary region (3 zones) + GRS to secondary region. This provides zone-level resilience in the primary AND geo-redundancy — both stated requirements.

ZRS alone has no secondary region. GRS has geo-redundancy but only LRS (single DC) in the primary. GZRS satisfies both.
</details>

---

**Q14.** What is the difference between Azure Private Endpoint and Azure Service Endpoint when restricting access to an Azure Storage account?

<details>
<summary>Answer</summary>

**Private Endpoint:** Creates a private NIC with a private IP in your VNet. The storage account can be made completely inaccessible from the public internet. Traffic never leaves the VNet. Resolved via Private DNS.

**Service Endpoint:** Extends the VNet's identity to the storage account. The storage account's public IP still exists but firewall rules can restrict access to specific VNets. Traffic exits the VNet and traverses the Azure backbone, but the storage account public IP is reachable from other locations unless firewall rules block it.

For complete private isolation → Private Endpoint. For network-restricted VNet access at lower cost → Service Endpoint.
</details>

---

**Q15.** Your company needs to archive financial records in Azure Blob Storage for 7 years to meet regulatory requirements. Records will not be accessed during the archive period but must not be deleted or modified. Which combination of features should you implement?

<details>
<summary>Answer</summary>

**Archive access tier + WORM (Write Once Read Many) Immutability Policy**

- **Archive tier:** Dramatically reduces storage cost for long-term data that does not need to be accessed (offline, rehydration required to read).
- **Immutability Policy (time-based retention):** Locks blobs for a specified retention period — no delete or overwrite is possible, even by admins. Meets compliance requirements for financial records.

Soft delete alone only provides a 14-day recovery window and does not prevent modification. Lifecycle management automates tiering but does not prevent deletion without an immutability policy.
</details>

---

*Domain 2 complete. Review traps in `resources/exam-traps.md` (Traps 1, 2, 3, 4, 5, 15, 16, 18).*
