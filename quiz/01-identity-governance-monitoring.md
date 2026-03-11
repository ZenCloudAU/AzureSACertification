# 🧠 Quiz — Domain 1: Identity, Governance & Monitoring

15 practice questions. Work through each scenario before reading the answer.

---

**Q1.** Your organisation requires that Azure administrators elevate their access for sensitive operations and that all activations are time-limited and logged. Which feature should you recommend?

<details>
<summary>Answer</summary>

**Azure AD Privileged Identity Management (PIM)**

PIM provides just-in-time role activation with time limits, approval workflows, MFA on activation, and full activation audit logs. Requires Azure AD Premium P2.

Distractor eliminated: Conditional Access (P1) controls sign-in conditions but does not manage privileged role activation time-boxing.
</details>

---

**Q2.** A company needs to enforce that all newly created Azure Storage Accounts across 40 subscriptions must have HTTPS-only traffic enabled. Policy must be enforced at creation time — non-compliant resources must not be created. Which policy effect should you use?

<details>
<summary>Answer</summary>

**Deny**

The `Deny` effect is the only policy effect that actively prevents the creation of a non-compliant resource. Audit and AuditIfNotExists only log violations. DeployIfNotExists and Modify apply changes post-creation.

Assign the policy to the Management Group that contains all 40 subscriptions — it cascades automatically.
</details>

---

**Q3.** Your organisation has a hybrid identity environment using Azure AD Connect with Password Hash Sync. The on-premises Active Directory servers experience an extended outage. Which authentication scenario still works?

<details>
<summary>Answer</summary>

**Cloud sign-ins continue (PHS is resilient to on-premises outages)**

Password Hash Sync copies a hash of the hashed password to Azure AD. When on-premises AD is unavailable, Azure AD authenticates users directly from the synced hash. This makes PHS the most resilient hybrid identity method.

Pass-Through Authentication (PTA) would fail — it requires live connectivity to an on-premises agent. Federation (AD FS) would also fail.
</details>

---

**Q4.** A company deploys resources across 6 Azure subscriptions used by 3 business units. They want to apply a common set of RBAC roles and Azure Policies to all subscriptions with the least management overhead. What should they implement?

<details>
<summary>Answer</summary>

**Management Groups**

Management Groups provide a scope above subscriptions. RBAC and Policy assigned at a Management Group cascade to all child subscriptions, resource groups, and resources. Maximum depth: 6 levels below the tenant root group.

Assigning RBAC/Policy to each subscription individually has high overhead and does not scale.
</details>

---

**Q5.** An external consulting firm needs to access your Azure DevOps project and review resources in a specific resource group for 3 months. The consultants already have their own corporate Azure AD accounts. What is the recommended approach?

<details>
<summary>Answer</summary>

**Invite external users as Azure AD B2B guests and assign RBAC at the resource group scope**

B2B allows partners to use their own corporate identities. RBAC at resource group scope limits access to only what is needed (least privilege). After 3 months, remove the assignment or use Azure AD Access Reviews to automate expiry.

Do not create new cloud identities for external users — they already have managed identities in their own directory.
</details>

---

**Q6.** You need to collect performance metrics from 200 Azure VMs and query them with KQL to identify VMs where average CPU exceeded 80% in the last hour. Which Azure Monitor component should you use?

<details>
<summary>Answer</summary>

**Log Analytics Workspace (Azure Monitor Logs)**

Log Analytics stores structured log and performance data queryable via KQL. The `Perf` table captures guest OS counters when the Azure Monitor Agent (AMA) is installed on VMs.

Azure Monitor Metrics stores numerical time-series but the KQL query requirement points to Log Analytics. Metrics Explorer does not support KQL.
</details>

---

**Q7.** A web application team wants to monitor end-to-end request performance, detect failing dependencies, and track custom events from their ASP.NET Core app in real time. Which Azure Monitor feature should they use?

<details>
<summary>Answer</summary>

**Application Insights**

Application Insights is the APM (Application Performance Monitoring) component of Azure Monitor. It captures distributed traces, HTTP request durations, dependency failures (SQL, HTTP calls), exceptions, custom events, and live metrics. SDK or auto-instrumentation.

Log Analytics stores raw logs; Azure Monitor Metrics captures counters — neither provides end-to-end distributed tracing or dependency maps out of the box.
</details>

---

**Q8.** Developers at your company currently have Contributor access at the subscription level. Security audit findings require that developers can create and manage resources but must NOT be able to assign roles to others. What is the least-privilege role that satisfies this?

<details>
<summary>Answer</summary>

**Contributor**

The Contributor role can create and manage all resource types but explicitly cannot grant access to others (no `Microsoft.Authorization/*/Write` actions). This is already the least-privilege role that provides full resource management without role assignment rights.

Owner adds role management rights. A custom role would be no narrower than Contributor for this specific requirement.
</details>

---

**Q9.** You are designing governance for a new Azure landing zone. Business unit tags must be applied to every resource group at creation. Missing tags must be auto-remediated. Which Azure Policy effect handles this?

<details>
<summary>Answer</summary>

**Modify**

The `Modify` effect adds, updates, or removes tags on resources at create/update time. It can be combined with a remediation task to fix existing non-compliant resources.

`Append` can add properties but cannot modify existing tag values. `Deny` blocks creation but doesn't auto-add the tag. `DeployIfNotExists` deploys related resources — not for tag management.
</details>

---

**Q10.** An organisation needs to enforce MFA for all users accessing the Azure portal from locations outside the corporate network. Users on the corporate network should not be challenged. Which feature implements this?

<details>
<summary>Answer</summary>

**Conditional Access (Azure AD Premium P1)**

Conditional Access evaluates signals: user identity + named location (corporate IP ranges) + application (Azure portal). Policy: If user is outside named location AND accessing Azure portal → require MFA. Users inside named location → grant without MFA challenge.

Requires Azure AD Premium P1 minimum. Per-user MFA cannot differentiate by location.
</details>

---

**Q11.** Your company's security team needs to view all Azure Security alerts, Secure Score details, and regulatory compliance dashboards across 5 subscriptions, but must not be able to change any resources or policies. Which built-in role is appropriate?

<details>
<summary>Answer</summary>

**Security Reader (Microsoft Defender for Cloud)**

The Security Reader role provides read access to all Defender for Cloud data: alerts, recommendations, Secure Score, regulatory compliance, and workbooks. No write permissions.

Reader provides general Azure resource read access but not Security-specific views. Security Admin can dismiss alerts and modify policies.
</details>

---

**Q12.** You are setting up Azure Monitor Alerts for a critical Azure SQL Database. The alert must fire when DTU consumption exceeds 85% sustained over 5 minutes. Which alert type should you use?

<details>
<summary>Answer</summary>

**Metric Alert**

DTU consumption is a platform metric emitted by Azure SQL Database to Azure Monitor Metrics. Metric Alerts evaluate numeric thresholds with configurable aggregation windows (1 min, 5 min, 15 min, etc.).

Log-based alerts (Log Analytics) are used for KQL query results. Activity Log alerts are for control plane events (resource created/deleted). Neither is appropriate for a numeric metric threshold.
</details>

---

**Q13.** Your CTO asks you to design a governance structure that allows each business unit to manage their own Azure resources independently while still enforcing company-wide security policies. What is the recommended hierarchy?

<details>
<summary>Answer</summary>

**Root Management Group → Company-wide policies (Deny/Audit) + per-BU child Management Groups → BU-specific policies + subscriptions per BU**

This provides:
- Company-wide enforcement via policies at the root/intermediate MG (inherited by all BUs)
- BU autonomy via RBAC at their own MG scope (delegated contributor access)
- Subscription-level isolation for billing and resource ownership

Maximum MG depth: 6 levels below root. Keep to 2–3 levels for manageability.
</details>

---

**Q14.** A Log Analytics workspace retains data for 30 days by default. Compliance requirements demand audit logs be retained for 2 years. What is the maximum configurable retention period for a Log Analytics workspace?

<details>
<summary>Answer</summary>

**730 days (2 years)**

Log Analytics workspace retention is configurable from 30 days (default, free tier) up to 730 days at the workspace level. For longer retention (up to 7 years), data can be exported to Azure Storage with a Data Export rule.

2 years (730 days) falls within the configurable workspace retention range.
</details>

---

**Q15.** An application uses a Managed Identity to authenticate to Azure Key Vault and retrieve secrets at runtime. The application team asks if they need to rotate the Managed Identity credentials. What is your answer?

<details>
<summary>Answer</summary>

**No — Managed Identity credentials are automatically rotated by Azure. No developer or admin action is required.**

Managed Identity credentials (certificates backing the identity) are managed and automatically rotated by the Azure platform. The application never handles credentials directly. This is the core value proposition of Managed Identity over Service Principals with stored secrets.

The team only needs to ensure the Managed Identity has been granted appropriate Key Vault access via an access policy or RBAC role (Key Vault Secrets User).
</details>

---

*Domain 1 complete. Review traps in `resources/exam-traps.md` (Traps 6, 24, 25).*
