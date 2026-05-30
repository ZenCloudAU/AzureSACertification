# Naming and Tagging Standard

**Purpose:** Define the resource naming convention and tag schema for this Azure environment. All resources must comply with this standard before deployment.

**Artefact owner:** [Name / role]
**Environment:** [Subscription name / ID]
**Date created:** [YYYY-MM-DD]
**Date last reviewed:** [YYYY-MM-DD]

---

## Naming Convention

### Pattern

```
[resource-type]-[workload]-[environment]-[region]-[instance]
```

All lowercase. Hyphens as separators. No spaces. No underscores (except where required by service constraints, e.g., storage accounts).

### Resource Type Abbreviations

| Resource Type | Abbreviation | Example |
|---|---|---|
| Resource Group | `rg` | `rg-labenv-dev-aue-001` |
| Virtual Network | `vnet` | `vnet-labenv-dev-aue-001` |
| Subnet | `snet` | `snet-labenv-dev-aue-001` |
| Network Security Group | `nsg` | `nsg-labenv-dev-aue-001` |
| Public IP Address | `pip` | `pip-labenv-dev-aue-001` |
| Load Balancer | `lb` | `lb-labenv-dev-aue-001` |
| Virtual Machine | `vm` | `vm-labenv-dev-aue-001` |
| Storage Account | `st` | `stlabenvdevaue001` (no hyphens — SA constraint) |
| Key Vault | `kv` | `kv-labenv-dev-aue-001` |
| Function App | `func` | `func-labenv-dev-aue-001` |
| App Service Plan | `asp` | `asp-labenv-dev-aue-001` |
| Logic App | `logic` | `logic-labenv-dev-aue-001` |
| Service Bus Namespace | `sb` | `sb-labenv-dev-aue-001` |
| Log Analytics Workspace | `log` | `log-labenv-dev-aue-001` |
| Application Insights | `appi` | `appi-labenv-dev-aue-001` |
| Managed Identity (user-assigned) | `id` | `id-labenv-dev-aue-001` |
| Budget | `budget` | `budget-labenv-dev-monthly` |
| Action Group | `ag` | `ag-labenv-cost-alert` |
| Diagnostic Setting | `diag` | `diag-kv-labenv-dev` |
| Resource Lock | `lock` | `lock-rg-labenv-prod` |

### Environment Abbreviations

| Environment | Abbreviation |
|---|---|
| Development | `dev` |
| Test | `test` |
| Staging | `stg` |
| Production | `prod` |
| Sandbox / Lab | `dev` (use dev for lab environments) |

### Region Abbreviations

| Azure Region | Abbreviation |
|---|---|
| Australia East | `aue` |
| Australia Southeast | `ause` |
| East US | `eus` |
| East US 2 | `eus2` |
| West Europe | `weu` |
| Southeast Asia | `sea` |

### Workload Abbreviation

The workload identifier is a short (3–8 character) lowercase abbreviation specific to the engagement or application.

For this lab environment: `labenv`

For a real engagement: define and record here before any resources are created.

---

## This Environment's Names

| Resource | Name |
|---|---|
| Resource Group | `rg-labenv-dev-aue-001` |
| Key Vault | `kv-labenv-dev-aue-001` |
| Log Analytics Workspace | `log-labenv-dev-aue-001` |
| Function App | `func-labenv-dev-aue-001` |
| Storage Account (for Function App) | `stlabenvdevaue001` |
| Budget | `budget-labenv-dev-monthly` |
| Action Group | `ag-labenv-cost-alert` |
| [Add additional resources here] | |

---

## Tag Schema

### Mandatory Tags

All resource groups and all resources within them must have these tags:

| Tag Key | Format | Example Value | Purpose |
|---|---|---|---|
| `Environment` | lowercase string | `dev`, `prod`, `stg` | Environment classification for filtering and policy |
| `Workload` | lowercase string | `labenv` | Workload or application identifier |
| `Owner` | email address | `phil.myint@zencloud.com.au` | Accountable person for cost and access escalation |
| `CostCentre` | string | `cc-001` | Cost attribution for billing reports |
| `CreatedDate` | `YYYY-MM-DD` | `2025-01-15` | Lifecycle tracking and cleanup scheduling |
| `ManagedBy` | string | `azure-portal`, `terraform`, `bicep` | Deployment method — supports drift detection |

### Recommended Tags

| Tag Key | Format | Example Value | Purpose |
|---|---|---|---|
| `Project` | string | `azure-re-entry-lab` | Project or engagement name |
| `ReviewDate` | `YYYY-MM-DD` | `2025-07-15` | Next scheduled resource review |
| `DataClassification` | string | `internal`, `public`, `restricted` | Data sensitivity level |
| `Backup` | `yes` / `no` | `no` | Whether the resource is included in backup policy |

### This Environment's Tag Values

| Tag | Value |
|---|---|
| Environment | `dev` |
| Workload | `labenv` |
| Owner | [your email] |
| CostCentre | [your cost centre] |
| CreatedDate | [lab creation date] |
| ManagedBy | `azure-portal` |

---

## Enforcement

### Azure Policy — Tag Enforcement

The following policies are assigned to enforce this tag schema:

| Policy | Assignment Scope | Effect |
|---|---|---|
| Require tag and its value on resource groups — Environment | Subscription | Deny |
| Require tag and its value on resource groups — Workload | Subscription | Deny |
| Require tag and its value on resource groups — Owner | Subscription | Deny |
| Inherit tag from resource group — Environment | Subscription | Modify |
| Inherit tag from resource group — Owner | Subscription | Modify |

> Note: Deny effect prevents creation. Modify effect adds the tag automatically if missing. For lab environments, Audit effect may be preferred over Deny.

### Remediation

For existing non-compliant resources, a remediation task must be created in the Azure Policy compliance blade after policy assignment. New policies do not retroactively fix existing resources.

---

## Governance Notes

**Design decision:** Hyphens are used as separators throughout, with storage accounts as the documented exception. This matches CAF recommendations and avoids the naming confusion that occurs when teams mix conventions across resource types.

**Risk accepted:** Storage account names are globally unique across all Azure — name collisions are possible. The `001` instance suffix reduces collision probability. If a collision occurs, increment the instance number.

**Review schedule:** This standard should be reviewed at the start of each new engagement or when a new resource type is introduced. Tag compliance should be verified quarterly via Azure Policy compliance reporting.
