# 🧠 Quiz — Domain 4: Infrastructure Solutions

15 practice questions. Work through each scenario before reading the answer.

---

**Q1.** A company needs to process 10,000 independent simulation jobs overnight. Each job takes 20–90 minutes. The infrastructure must auto-provision and auto-deprovision compute nodes and require minimal administrative effort. Which compute service should you recommend?

<details>
<summary>Answer</summary>

**Azure Batch**

Azure Batch is purpose-built for large-scale parallel and HPC workloads. It automatically provisions compute nodes, schedules tasks across nodes, retries failures, and deprovisions nodes when jobs complete — minimal admin overhead.

Azure Functions (Consumption) has a 60-minute maximum execution time limit, which eliminates it for 90-minute jobs. VMs would require full manual management. AKS is for containerised workloads with persistent clusters.
</details>

---

**Q2.** An event-driven application needs to process HTTP requests infrequently (a few hundred per day). Execution time is under 3 minutes per request. Cost must be minimised — idle time should not be billed. What is the best compute option?

<details>
<summary>Answer</summary>

**Azure Functions — Consumption Plan**

Consumption plan bills per execution (per 1 million executions + execution time in GB-seconds). Zero cost during idle periods. HTTP trigger is natively supported. Execution under 3 minutes is well within the 60-minute maximum.

Premium plan charges a monthly reserved instance cost regardless of invocations. App Service (Dedicated) charges by the hour. Both incur costs during idle time.
</details>

---

**Q3.** Two containers in an Azure Container Instance group need to share a persistent volume that both can read and write simultaneously. Which volume type should you use?

<details>
<summary>Answer</summary>

**Azure Files (SMB share)**

Azure Files volumes mounted in ACI groups support simultaneous ReadWriteMany — multiple containers in the same group can mount the same share. Azure Disk is ReadWriteOnce (single container only). emptyDir is ephemeral and lost on restart. gitRepo is read-only.
</details>

---

**Q4.** A global e-commerce company hosts its web application in multiple Azure regions. Users report that their session cookies are not maintained when requests route to different regions. The solution must use a single hostname and must be L7-aware. What should you implement?

<details>
<summary>Answer</summary>

**Azure Front Door with session affinity enabled**

Azure Front Door operates at L7 (HTTP/HTTPS), supports a single global hostname (anycast), and provides session affinity (cookie-based). Session affinity ensures subsequent requests from the same user go to the same backend pool origin.

Traffic Manager is DNS-based with no L7 awareness — it cannot implement session affinity. Application Gateway is regional (single region).
</details>

---

**Q5.** A company has a hub-and-spoke VNet topology. All traffic from spoke VNets to the internet must pass through an Azure Firewall deployed in the hub VNet. Spoke VNets are peered to the hub. What configuration forces this traffic routing?

<details>
<summary>Answer</summary>

**User Defined Routes (UDR) on each spoke VNet subnet with next hop = Azure Firewall private IP**

By default, VNet peering routes inter-VNet traffic directly — it does not flow through hub resources. UDRs override the default routing table. A route for `0.0.0.0/0` (default route) pointing to the firewall's private IP forces all outbound internet traffic from spoke subnets through the hub firewall.

Also enable IP forwarding on the Azure Firewall NIC (for NVA scenarios). NSGs control port filtering but cannot change routing paths.
</details>

---

**Q6.** A team needs to allow Azure VM administrators to RDP into production VMs without exposing any public IP addresses or opening RDP port 3389 in NSG rules. What is the recommended approach?

<details>
<summary>Answer</summary>

**Azure Bastion**

Azure Bastion provides browser-based RDP/SSH over HTTPS (port 443) from the Azure portal. VMs need no public IP and no port 3389 in the NSG. Bastion is deployed in the `AzureBastionSubnet` (/27 minimum) within the VNet.

JIT VM Access (Defender for Cloud) is a complementary control but still requires port 3389 open temporarily. Bastion eliminates the need for RDP exposure entirely.
</details>

---

**Q7.** An on-premises data centre connects to Azure via an ExpressRoute circuit. The security team requires all data in transit to be encrypted. What should you implement?

<details>
<summary>Answer</summary>

**IPSec over ExpressRoute (site-to-site VPN tunnel over the ExpressRoute circuit)**

ExpressRoute provides a private connection but does NOT encrypt traffic by default. To add encryption, configure an IPSec VPN tunnel that runs over the ExpressRoute private peering path. This provides both the low latency of a private circuit AND encryption.

A standard VPN Gateway alone uses the public internet. MACsec is available for ExpressRoute Direct connections at the physical layer.
</details>

---

**Q8.** A company has 50 branch offices worldwide that need secure connectivity to Azure and to each other via the Microsoft backbone. The network team wants automated connectivity provisioning and centralised security management via Azure Firewall Manager. What Azure service provides this?

<details>
<summary>Answer</summary>

**Azure Virtual WAN Standard**

Virtual WAN Standard provides: automated S2S VPN to branches, inter-hub routing via Microsoft backbone for branch-to-branch traffic, and Azure Firewall Manager integration (Secured Virtual Hub). Basic tier only supports S2S VPN and lacks inter-hub routing and Firewall Manager.

VPN Gateway alone requires manual configuration per branch and does not support Firewall Manager integration at scale.
</details>

---

**Q9.** An application experiences high database load due to repeated identical queries returning the same data (product catalogue, configuration settings). Query results change only once per hour. What should you implement to reduce database load?

<details>
<summary>Answer</summary>

**Azure Cache for Redis — cache-aside pattern**

Redis stores query results in memory. Application checks Redis first (cache hit → return instantly). On cache miss, query runs against the database, result is written to Redis with a 1-hour TTL, then returned. Subsequent identical queries are served from memory at sub-millisecond latency.

CDN serves static web content (images, CSS, JS) — not dynamic SQL results. ExpressRoute is a connectivity solution. Read replicas reduce read load but still execute full queries.
</details>

---

**Q10.** A company runs an event-driven architecture where multiple independent services (billing, notifications, audit) each need to receive a copy of every order event. Which Azure messaging service should you implement?

<details>
<summary>Answer</summary>

**Azure Service Bus Topics with Subscriptions**

Topics implement pub/sub: each subscription gets an independent, filtered copy of every message. Billing, notifications, and audit can each have their own subscription — they receive their own copy and process at their own pace. Messages are not consumed by one subscriber on behalf of others.

Service Bus Queue delivers each message to one consumer only. Event Grid is for reactive event routing but has different durability and filtering semantics. Event Hubs is for high-volume streaming, not guaranteed per-consumer delivery.
</details>

---

**Q11.** A company ingests 1 million telemetry events per second from IoT devices. Raw events must be stored durably for 7 days for replay and then archived to storage for long-term analysis. What is the correct architecture?

<details>
<summary>Answer</summary>

**Azure Event Hubs (with 7-day retention) + Event Hubs Capture → Azure Data Lake Storage Gen2**

Event Hubs is designed for high-volume streaming at this scale. Configurable retention (up to 7 days on Standard tier, 90 days on Premium/Dedicated) supports replay. Event Hubs Capture automatically archives incoming events to ADLS Gen2 (or Blob) in Apache Avro format at defined time/size intervals.

Service Bus is not designed for 1 million events/second. SQL Database would be prohibitively expensive and inappropriate for raw sensor ingestion.
</details>

---

**Q12.** A company publishes 30 APIs from App1 (hosted on 5 servers) and 40 APIs from App2 (hosted on 10 servers). They want to manage all APIs through a single gateway. How many APIs must be registered in Azure API Management?

<details>
<summary>Answer</summary>

**70 APIs**

APIM registers API endpoints, not servers or applications. App1 contributes 30 APIs. App2 contributes 40 APIs. Total = 70. The number of servers (5 or 10) is irrelevant to APIM — APIM cares about API operations, not where they are physically hosted.
</details>

---

**Q13.** An Azure Data Factory pipeline needs to copy data from an on-premises SQL Server database to Azure Data Lake Storage Gen2. The on-premises server cannot be accessed from the public internet. What ADF component must be deployed on-premises?

<details>
<summary>Answer</summary>

**Self-Hosted Integration Runtime (SHIR)**

The Self-Hosted Integration Runtime is a software agent installed on an on-premises machine (or VM inside an isolated network). It acts as a bridge, securely connecting on-premises data sources to Azure Data Factory without requiring inbound internet connectivity on the on-premises network.

Azure Integration Runtime (Auto-resolve) only works with publicly accessible cloud endpoints. Linked Service is a connection definition, not a runtime component.
</details>

---

**Q14.** A big data team needs to run large-scale ETL transformations using Apache Spark on Azure. The platform must be fully managed (no manual cluster infrastructure), support Python and SQL notebooks, and integrate with ADLS Gen2. What service should you recommend?

<details>
<summary>Answer</summary>

**Azure Databricks**

Databricks is the managed Apache Spark platform on Azure: auto-scaling clusters, collaborative Python/SQL/Scala/R notebooks, native ADLS Gen2 integration via the ABFS driver (or credential passthrough in Premium SKU), and MLflow for ML tracking.

Azure HDInsight can run Spark but requires more infrastructure management. Azure Stream Analytics is SQL-based for real-time processing, not Spark.
</details>

---

**Q15.** An organisation wants to configure NAT for a subnet hosting 200 Azure VMs that need predictable outbound internet access using a specific set of public IP addresses. Inbound connections from the internet to these VMs must not be permitted. What Azure service should you use?

<details>
<summary>Answer</summary>

**Azure NAT Gateway**

NAT Gateway provides managed outbound SNAT for subnets. Attach up to 16 public IP addresses to the gateway — all outbound connections from the subnet exit through this predictable pool. NAT Gateway is strictly outbound (SNAT) — it has no inbound path, so inbound connections are blocked by design.

A Load Balancer with outbound rules would also provide SNAT but introduces complexity. Public IPs on individual VMs bypass centralised control. NAT Gateway is the simplest, most scalable solution for this pattern.
</details>

---

*Domain 4 complete. Review traps in `resources/exam-traps.md` (Traps 2, 7, 8, 11, 12, 13, 14, 20, 21, 22, 23, 27, 30).*
