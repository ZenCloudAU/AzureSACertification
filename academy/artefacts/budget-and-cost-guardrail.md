# Budget and Cost Guardrail

**Purpose:** Document the budget alert configuration and cost governance approach for this Azure environment. An architect who signs off on an Azure environment without a budget configuration has accepted unlimited spend risk.

**Artefact owner:** [Name / role]
**Environment:** [Subscription name / ID]
**Date created:** [YYYY-MM-DD]

---

## Budget Configuration

| Field | Value |
|---|---|
| Budget name | `budget-labenv-dev-monthly` |
| Scope | Subscription — [subscription name / ID] |
| Reset period | Monthly |
| Budget amount | [AUD / USD amount] |
| Start date | [YYYY-MM-01] |
| End date | [YYYY-12-31 or ongoing] |

---

## Alert Thresholds

| Alert | Condition | Threshold | Action Group |
|---|---|---|---|
| Warning | Actual cost ≥ 80% of budget | 80% | `ag-labenv-cost-alert` |
| Critical | Actual cost ≥ 100% of budget | 100% | `ag-labenv-cost-alert` |

---

## Action Group

| Field | Value |
|---|---|
| Action group name | `ag-labenv-cost-alert` |
| Short name | `labcost` |
| Resource group | `rg-labenv-dev-aue-001` |
| Email destination | [email address] |
| SMS destination | [phone number — optional] |

---

## Cost Attribution

### Tag-Based Attribution

Cost attribution relies on the tag schema defined in [naming-and-tagging-standard.md](naming-and-tagging-standard.md). The `CostCentre` and `Workload` tags enable filtering in Azure Cost Management.

To view cost by workload: Cost Management → Cost analysis → Group by: Tag → `Workload`

To view cost by owner: Cost Management → Cost analysis → Group by: Tag → `Owner`

### Resource Group-Based Attribution

Each resource group maps to a single workload lifecycle. Cost by resource group is visible in Cost Management → Cost analysis → Group by: Resource group.

---

## Cost Drivers in This Environment

| Resource | Estimated Monthly Cost | Notes |
|---|---|---|
| Log Analytics workspace | ~$0.01–$3 AUD | Depends on ingestion volume |
| Key Vault | ~$0.50 AUD | Standard operations pricing |
| Function App (Consumption) | ~$0 | Free tier covers lab usage |
| Storage account | ~$0.02 AUD | Minimal lab data |
| **Total estimate** | **~$4–$5 AUD/month** | While resources exist |

---

## Cost Governance Approach

### Pre-Deployment

Before any new resource is deployed:

1. Resource type's estimated monthly cost is known
2. Cost driver has been identified (compute hours, storage GB, operations)
3. Cost is within budget headroom
4. Clean-up plan is documented

### During Deployment

- Tags are applied before or at resource creation
- No open-ended resource creation without a defined lifecycle
- Azure Advisor is checked weekly for cost optimisation recommendations

### Post-Deployment

- Monthly budget alert response: investigate top cost drivers in Cost analysis
- At 80% alert: review what is running, confirm all resources are expected and needed
- At 100% alert: stop non-essential lab resources immediately and investigate

### Clean-Up Policy

Resources in the `dev` environment are reviewed monthly. Any resource without a clear owner tag or with a `CreatedDate` older than 90 days is a candidate for decommission.

---

## Azure Advisor Cost Recommendations

| Recommendation | Status | Action |
|---|---|---|
| [Record recommendations from Advisor at lab setup time] | | |
| | | |

---

## Cost Guardrail Response Procedure

**If budget alert at 80% fires:**

1. Open Cost Management → Cost analysis
2. Filter to current billing period
3. Group by resource group and service type
4. Identify top 3 cost drivers
5. Confirm all resources are expected and needed
6. If unexpected spend identified: investigate → stop resource → report

**If budget alert at 100% fires:**

1. Stop non-essential lab resources immediately
2. Run `az resource list --resource-group rg-labenv-dev-aue-001 -o table` to list all running resources
3. Identify any resource that should not be running
4. Delete or deallocate as appropriate
5. Review the budget amount — if regularly exceeding, adjust or reduce scope

---

## Governance Notes

**Design decision:** Budget scope is set at the subscription level rather than per resource group. A subscription-level budget provides the safety net. Resource group-level budgets can be added for per-team attribution but are not a substitute for the subscription-level guardrail.

**Risk accepted:** Budget alerts are retrospective — they alert after spend occurs, not before. There is no Azure-native mechanism to hard-block spend at a threshold (the budget cannot stop a resource from running). The alert triggers an action group, and a human must respond. Automated remediation via Azure Automation or Logic App can be added as an enhancement.

**Review schedule:** Budget amount should be reviewed at the start of each engagement. Clean-up should be performed at the end of each lab session.
