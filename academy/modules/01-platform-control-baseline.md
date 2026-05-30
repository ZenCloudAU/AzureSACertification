# Module 01 — Azure Platform Control Baseline

## Purpose

Establish the mandatory controls that must exist before any Azure workload is deployed. An architect who cannot articulate the platform control baseline cannot govern what engineers build on top of it.

---

## Architect Briefing

Every Azure environment — whether a single subscription for a side project or a 500-subscription enterprise landing zone — has a platform control layer that determines whether what gets built on top of it is safe, auditable, and cost-controlled.

The mistake returning architects make is skipping straight to workload architecture. The controls that govern the platform are not an afterthought — they are the foundation. A workload deployed into an uncontrolled subscription is a governance liability before a single line of application code is written.

The current Azure platform control model is built around:

- **Management Groups** — the policy and governance hierarchy above subscriptions
- **Subscriptions** — the billing and isolation boundary
- **Resource Groups** — the deployment and lifecycle boundary
- **Azure Policy** — the enforcement layer for naming, tagging, SKU restrictions, and compliance
- **Azure Cost Management + Budgets** — the financial control and alert layer
- **Resource Locks** — the protection layer for production resources
- **Microsoft Defender for Cloud** — the posture management and threat detection layer
- **Diagnostic Settings** — the audit and observability baseline

If you last worked with Azure before the **Azure Landing Zone (ALZ)** reference architecture became the enterprise standard, expect to see a significant shift in how these controls are applied at scale. The Cloud Adoption Framework's landing zone model organises subscriptions into platform, connectivity, identity, and management subscriptions, with application workloads in separate landing zone subscriptions.

For this lab, you are working in a single subscription — a common entry point for re-entry practice and small engagements.

---

## Engineering View

### Subscription Design

A subscription is both a billing boundary and an isolation boundary. Key decisions at subscription design time:

| Decision | Options | Architect Consideration |
|---|---|---|
| How many subscriptions? | One per workload, one per environment, one for all | Blast radius, cost visibility, policy scope, limit quotas |
| Subscription naming | Free-form | Standardise: `[org]-[env]-[workload]-[region]` |
| Management Group placement | Root, platform, landing zone, sandbox | Determines which policies inherit |
| Who owns the subscription? | Individual, team, service account | Determines billing and access escalation path |

### Management Groups

Management groups sit above subscriptions. Policies and RBAC assigned at the management group level inherit down. The standard ALZ hierarchy:

```
Tenant Root Group
  └─ [Org] Management Group
       ├─ Platform
       │    ├─ Identity
       │    ├─ Connectivity
       │    └─ Management
       ├─ Landing Zones
       │    ├─ Corp
       │    └─ Online
       └─ Sandbox
```

For a single-subscription lab: place the subscription under a Sandbox management group if one exists, or work directly in the subscription scope.

### Resource Groups

Resource groups are the lifecycle boundary. Architect rule: resources that are deployed, managed, and deleted together belong in the same resource group.

Resist the urge to organise resource groups by service type. Organise by workload and lifecycle. A resource group per tier (network-rg, compute-rg, storage-rg) creates management overhead without governance benefit.

| Anti-pattern | Problem |
|---|---|
| One resource group for everything | No lifecycle isolation, no cost attribution, no controlled delete |
| Resource groups by service type | Breaks lifecycle alignment — you delete a VM but the disk is in another group |
| No resource group naming standard | Engineers name groups ad-hoc, making automation and cost attribution impossible |

### Naming Standards

Azure resource names must follow the constraints of each service. Resource group names allow letters, numbers, hyphens, underscores, and periods up to 90 characters. Storage account names allow only lowercase letters and numbers, 3–24 characters.

CAF-aligned naming pattern:

```
[resource-type]-[workload]-[environment]-[region]-[instance]

Examples:
rg-labenv-dev-aue-001          (resource group)
st-labenv-dev-aue-001          (storage account — st prefix, lowercase, no hyphens)
kv-labenv-dev-aue-001          (key vault)
vnet-labenv-dev-aue-001        (virtual network)
vm-labenv-dev-aue-001          (virtual machine)
```

Region abbreviations: `aue` = Australia East, `ause` = Australia Southeast, `eus` = East US.

### Tags

Tags are the metadata layer that enables cost attribution, automation, and governance filtering. A tag schema must be defined before any resources are deployed. Tags applied after the fact are expensive to retrofit.

Minimum mandatory tags for a lab or small engagement:

| Tag Key | Example Value | Purpose |
|---|---|---|
| `Environment` | `dev`, `staging`, `prod` | Environment classification |
| `Workload` | `labenv` | Workload identifier |
| `Owner` | `phil.myint@zencloud.com.au` | Accountable person for cost and access |
| `CostCentre` | `cc-001` | Cost attribution for billing |
| `CreatedDate` | `2025-01-15` | Lifecycle tracking |
| `ManagedBy` | `azure-portal`, `terraform`, `bicep` | Deployment method tracking |

Azure Policy can enforce tag presence and values. Without a policy, tags drift and become useless within weeks.

### Azure Policy

Azure Policy evaluates resource configurations against defined rules. It can audit, deny, or automatically remediate. Key policy effects:

| Effect | Behaviour |
|---|---|
| `Deny` | Blocks non-compliant resource creation or update |
| `Audit` | Logs non-compliance but does not block |
| `Modify` | Adds or changes properties on resources (e.g., adds a missing tag) |
| `DeployIfNotExists` | Deploys a related resource if it does not exist (e.g., deploys diagnostic settings) |

The Azure Policy built-in initiative `Azure Security Benchmark` covers 200+ controls aligned to CIS and NIST. Enable it and review the compliance score before building anything.

Useful built-in policies for a platform baseline:

- Require a tag and its value on resource groups
- Allowed locations (restrict deployments to approved regions)
- Allowed virtual machine SKUs
- Require diagnostic settings on Key Vault, storage accounts, and network security groups

### Budgets and Cost Management

Azure Cost Management + Billing provides:

- **Budgets:** Set a spend threshold for a subscription or resource group. Configure action groups to alert at 80% and 100% of budget.
- **Cost analysis:** Filter by resource group, tag, service type, or time period.
- **Advisor:** Recommendations for cost optimisation, including unused resources and right-sizing opportunities.

For a lab subscription, set a monthly budget of $50–$100 AUD. Alert at 80% (warning) and 100% (critical). Configure the alert to email the subscription owner.

Budget alert architecture:

```
Budget (scope: subscription or resource group)
  └─ Alert condition: Actual cost ≥ 80%
       └─ Action Group
            └─ Email: [owner@domain.com]
            └─ SMS (optional)
```

### Resource Locks

Resource locks prevent accidental deletion or modification. Two types:

| Lock Type | Effect |
|---|---|
| `CanNotDelete` | Resource can be read and modified, but cannot be deleted |
| `ReadOnly` | Resource can only be read — no modifications or deletion |

Apply `CanNotDelete` locks to production resource groups and critical infrastructure resources. `ReadOnly` locks are rarely used — they break many operations including deployments.

Lock inheritance: a lock on a resource group applies to all resources within it. A lock on a resource applies only to that resource.

### Microsoft Defender for Cloud

Defender for Cloud provides:

- **Secure Score** — a percentage representing how well your environment follows security best practices
- **Security recommendations** — specific, actionable items to improve posture
- **Regulatory compliance** — maps your configuration to compliance standards (CIS, PCI-DSS, NIST)
- **Workload protections** — enhanced threat detection for specific services (servers, containers, databases, storage)

Enable the foundational Defender for Cloud CSPM (Cloud Security Posture Management) tier on all subscriptions. The enhanced workload protections are paid — evaluate based on workload risk.

A Secure Score below 60% in a new subscription indicates the platform baseline is not established. The recommendations pane tells you exactly what to fix and in what order.

### Diagnostic Settings

Every Azure resource should have diagnostic settings configured to send logs to a Log Analytics workspace. This is the audit trail and the observability foundation.

Key resources that require diagnostic settings from day one:

- Key Vault (audit logs, request logs)
- Azure Active Directory / Entra ID (sign-in logs, audit logs)
- Network Security Groups (flow logs)
- Storage accounts (blob read/write/delete logs)
- Azure Subscriptions (Azure Activity Log)

The Activity Log captures all management-plane operations — who deployed what, when. It is retained for 90 days by default. Archive to a Log Analytics workspace or storage account for longer retention.

---

## Azure Service Map

| Service | Role in Platform Baseline | Key Setting |
|---|---|---|
| Azure Management Groups | Policy and RBAC inheritance hierarchy | Assignment scope |
| Azure Subscriptions | Billing and isolation boundary | Owner, directory (tenant) |
| Azure Resource Groups | Lifecycle and access boundary | Naming, tags, locks |
| Azure Policy | Configuration enforcement | Effect: Deny, Audit, Modify |
| Azure Cost Management | Budget and spend visibility | Budget + action group |
| Azure Advisor | Optimisation recommendations | Cost, security, reliability, performance |
| Resource Locks | Accidental deletion protection | CanNotDelete on prod groups |
| Microsoft Defender for Cloud | Posture score and security recommendations | Enable CSPM tier |
| Log Analytics Workspace | Centralised log destination | Diagnostic settings on all key resources |
| Azure Activity Log | Management-plane audit trail | Archive to workspace or storage |

---

## Common Failure Modes

**No tag policy enforced.** Engineers apply tags inconsistently. Within 3 months, cost attribution is impossible and automated governance filtering breaks.

**Budget with no action group.** An alert threshold set with no email or action group fires silently. The first sign of overspend is the invoice.

**Resource groups named ad-hoc.** Names like `test`, `myapp`, `temp`, `rg1` accumulate. Automation cannot target them, cost analysis cannot filter them, and no one knows what they contain after 6 months.

**No CanNotDelete lock on production resources.** A developer running `az group delete` on the wrong resource group destroys a production environment. Locks prevent this. They take 30 seconds to apply.

**Defender for Cloud enabled but recommendations ignored.** The Secure Score is not the goal — acting on recommendations is. A score of 45% with no remediation plan means the control layer has been acknowledged but not established.

**Diagnostic settings not configured.** When an incident occurs, the audit trail does not exist. Log Analytics must be configured proactively. Reactive log configuration after an incident is too late.

**No subscription design decision.** The team deploys everything into a single subscription without discussion. As the environment grows, cost attribution, access control, and policy enforcement become impossible to retrofit cleanly.

---

## Review Questions

1. A team wants to deploy a new workload into your Azure subscription. What five things must be in place before they deploy the first resource?

2. Your budget alert fires at 100% of the monthly limit. What investigation steps do you take, in order?

3. An engineer proposes organising resource groups by service type (compute-rg, storage-rg, network-rg). What is your response, and what alternative do you propose?

4. You inherit a subscription with 40 resource groups and no consistent naming or tags. What is your remediation approach, and what policy do you put in place immediately?

5. Microsoft Defender for Cloud shows a Secure Score of 52%. What does this tell you about the platform baseline, and what is your first action?

---

## Lab Task

Complete [Lab 01 — Platform Control Baseline](../labs/lab-01-platform-control-baseline.md).

The lab establishes a safe practice environment with correct naming, tags, budget, and diagnostic settings. Evidence is captured and compiled into three artefacts.

---

## Artefact Output

This module produces three artefacts:

1. [azure-platform-control-checklist.md](../artefacts/azure-platform-control-checklist.md) — pre-deployment control checklist for any Azure workload
2. [naming-and-tagging-standard.md](../artefacts/naming-and-tagging-standard.md) — your naming convention and tag schema
3. [budget-and-cost-guardrail.md](../artefacts/budget-and-cost-guardrail.md) — your budget alert configuration and cost governance approach

---

## Notes

- The CAF naming conventions are recommendations, not hard requirements. Your organisation's standard may differ. The important thing is that a standard exists, is documented, and is enforced.
- Azure Policy has a significant remediation lag for existing resources. New policies do not immediately flag or fix existing non-compliant resources — a remediation task must be triggered manually for existing resources.
- Management Groups require the tenant to have Azure AD Premium P1 or be part of an EA. For personal subscriptions, you may not have management group access. Scope your work to the subscription level in this case.
- The Azure Activity Log is free. Log Analytics workspace ingestion costs approximately $3.50 AUD/GB. For a lab, this is negligible. For production, budget accordingly.

---

## Hints

- The Azure Policy Compliance blade takes 15–30 minutes to update after a new policy is assigned. Do not expect immediate results.
- If you cannot see management groups in the portal, navigate to `portal.azure.com > Management groups` and ensure your account has the Management Group Reader role at the tenant root.
- When creating a budget, set the scope to the subscription, not a resource group. Resource group budgets are useful for per-team attribution but a subscription-level budget is your safety net.
- The `DeployIfNotExists` policy effect requires a managed identity on the policy assignment to perform deployments. Assign the Contributor role to the managed identity at the appropriate scope.

---

## Completion Criteria

**Foundation:** Answer all five review questions in writing. Name all ten services in the Azure service map and state the role of each.

**Practitioner:** Foundation criteria met. Lab 01 complete with all evidence captured. All three artefacts produced with content reflecting your actual lab configuration, not generic text.

**Senior Architect:** Practitioner criteria met. Senior SA review answered at rubric standard. Artefacts annotated with governance notes, design decisions, and risks accepted.

---

## Senior SA Review

1. You are reviewing an engineering team's Azure subscription before they go to production. They have a Secure Score of 67%, budget alerts configured, and resource groups named consistently. They have no Azure Policy assigned and no diagnostic settings on their Key Vault. What is your assessment, and what do you require before approving production go-live?

2. A client asks whether they need management groups for a 3-subscription Azure environment. What factors drive your recommendation, and what is the governance consequence of getting this wrong?

3. You discover that a production resource group has no CanNotDelete lock. The team says it has been in production for 6 months without a problem. How do you respond, and what does this tell you about the team's risk culture?

4. The finance team wants to attribute costs to individual projects in a shared subscription. The engineering team has not applied consistent tags. Design a remediation approach that works within Azure's constraints — including what is technically possible versus what requires manual effort.
