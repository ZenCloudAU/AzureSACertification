# Lab 01 — Platform Control Baseline

## Goal

Establish a safe, governed Azure practice environment that models the controls required before any real workload is deployed. Produce three artefacts that could be handed to an engineer to govern a new Azure engagement.

---

## Scenario

You have been asked to set up the Azure practice environment for the Azure Architect Re-Entry Lab. Before any service is deployed, the environment must have:

- A clearly named, tagged resource group
- A budget alert to prevent uncontrolled spend
- A naming and tagging standard documented for the engagement
- A platform control checklist that verifies the environment is ready for workload deployment
- Diagnostic settings confirmed for the subscription Activity Log

You are not deploying a workload yet. You are making the environment safe and governable first.

---

## Prerequisites

- An Azure subscription (personal pay-as-you-go, Visual Studio, or free trial)
- Subscription Owner or Contributor + User Access Administrator access
- Azure CLI installed (`az --version`) or access to Azure Cloud Shell
- A Log Analytics workspace — create one during this lab if one does not exist

---

## Steps

### Step 1: Create a Resource Group

Using the Azure portal or CLI, create a resource group that follows the naming standard you will define in the artefact.

Suggested name: `rg-labenv-dev-aue-001`

**Portal:** Search `Resource groups` → Create → select your subscription → enter name and region → add tags (see Step 2)

**CLI:**
```bash
az group create \
  --name rg-labenv-dev-aue-001 \
  --location australiaeast \
  --tags Environment=dev Workload=labenv Owner=your@email.com CostCentre=cc-001 CreatedDate=2025-01-15 ManagedBy=azure-portal
```

**Evidence to capture:** Screenshot of the created resource group showing name, region, and tags. Or CLI output.

---

### Step 2: Define and Apply Tags

Open the resource group → Tags blade. Apply the minimum required tags from your naming-and-tagging-standard artefact. Confirm all tags are applied.

Minimum tags:

| Tag Key | Your Value |
|---|---|
| Environment | dev |
| Workload | labenv |
| Owner | [your email] |
| CostCentre | [your cost centre] |
| CreatedDate | [today's date] |
| ManagedBy | azure-portal |

**Evidence to capture:** Screenshot of the Tags blade showing all applied tags.

---

### Step 3: Apply a CanNotDelete Lock

Apply a CanNotDelete lock to the resource group to prevent accidental deletion.

**Portal:** Resource group → Locks → Add → Name: `lock-rg-labenv-prod` → Lock type: Delete → Save

**CLI:**
```bash
az lock create \
  --name lock-rg-labenv-dev \
  --resource-group rg-labenv-dev-aue-001 \
  --lock-type CanNotDelete
```

**Evidence to capture:** Screenshot of the Locks blade showing the lock applied.

---

### Step 4: Create or Confirm Log Analytics Workspace

Create a Log Analytics workspace to receive diagnostic logs. If one already exists in your subscription, use it.

Suggested name: `log-labenv-dev-aue-001`

**Portal:** Search `Log Analytics workspaces` → Create → Resource group: `rg-labenv-dev-aue-001` → Name and region → Review + create

**CLI:**
```bash
az monitor log-analytics workspace create \
  --resource-group rg-labenv-dev-aue-001 \
  --workspace-name log-labenv-dev-aue-001 \
  --location australiaeast
```

**Evidence to capture:** Screenshot or CLI output showing workspace created.

---

### Step 5: Configure Subscription Activity Log Diagnostic Settings

Route the Azure Subscription Activity Log to your Log Analytics workspace. This captures all management-plane operations — who deployed what, when, from where.

**Portal:** Subscriptions → select your subscription → Activity log → Diagnostics settings → Add diagnostic setting

- Name: `diag-activitylog-labenv`
- Logs: Administrative, Security, Alert, Recommendation, Policy, Autoscale
- Destination: Send to Log Analytics workspace → select your workspace

**CLI:**
```bash
SUBSCRIPTION_ID=$(az account show --query id -o tsv)
WORKSPACE_ID=$(az monitor log-analytics workspace show \
  --resource-group rg-labenv-dev-aue-001 \
  --workspace-name log-labenv-dev-aue-001 \
  --query id -o tsv)

az monitor diagnostic-settings create \
  --name diag-activitylog-labenv \
  --resource /subscriptions/$SUBSCRIPTION_ID \
  --workspace $WORKSPACE_ID \
  --logs '[
    {"category": "Administrative", "enabled": true},
    {"category": "Security", "enabled": true},
    {"category": "Alert", "enabled": true},
    {"category": "Recommendation", "enabled": true},
    {"category": "Policy", "enabled": true},
    {"category": "Autoscale", "enabled": true}
  ]'
```

**Evidence to capture:** Screenshot of Diagnostics settings showing the setting configured and the workspace destination.

---

### Step 6: Create a Budget and Alert

Create a monthly budget for the subscription with alerts at 80% and 100%.

**Portal:** Cost Management + Billing → Budgets → Add

- Scope: your subscription
- Name: `budget-labenv-dev-monthly`
- Reset period: Monthly
- Amount: $50 AUD (adjust as appropriate)
- Alert conditions:
  - Alert 1: Actual cost, 80% of budget → Action Group (see below)
  - Alert 2: Actual cost, 100% of budget → Action Group

**Create an action group first:**

Portal: Monitor → Action groups → Create

- Name: `ag-labenv-cost-alert`
- Short name: `labcost`
- Notifications: Email/SMS → your email address

**CLI (budget with existing action group):**
```bash
ACTION_GROUP_ID=$(az monitor action-group show \
  --resource-group rg-labenv-dev-aue-001 \
  --name ag-labenv-cost-alert \
  --query id -o tsv)

az consumption budget create \
  --budget-name budget-labenv-dev-monthly \
  --amount 50 \
  --category Cost \
  --time-grain Monthly \
  --start-date "$(date +%Y-%m-01)" \
  --end-date "2026-12-31" \
  --notification-key 80percent \
  --notification-operator GreaterThan \
  --notification-threshold 80 \
  --contact-groups $ACTION_GROUP_ID
```

**Evidence to capture:** Screenshot of the configured budget showing alerts at 80% and 100% with the action group.

---

### Step 7: Check Microsoft Defender for Cloud Secure Score

Navigate to Defender for Cloud and record the current Secure Score.

**Portal:** Search `Microsoft Defender for Cloud` → Overview → note the Secure Score percentage

Open Recommendations. Review the top 5 recommendations. Note which category they fall under (identity, data, compute, networking).

**Evidence to capture:** Screenshot of Defender for Cloud overview showing Secure Score. Note top 5 recommendations.

---

### Step 8: Confirm Azure Policy Compliance

Navigate to Azure Policy. Check the current compliance state for your subscription.

**Portal:** Search `Policy` → Compliance → note the overall compliance percentage and any non-compliant policies

If compliance is 0% (no policies assigned), note this. You will assign policies in later modules.

**Evidence to capture:** Screenshot of Policy Compliance blade.

---

## Evidence to Capture

| # | Item | Method |
|---|---|---|
| 1 | Resource group created with correct name and region | Screenshot or CLI output |
| 2 | All six tags applied to resource group | Screenshot of Tags blade |
| 3 | CanNotDelete lock applied | Screenshot of Locks blade |
| 4 | Log Analytics workspace created | Screenshot or CLI output |
| 5 | Subscription Activity Log diagnostic settings configured | Screenshot of Diagnostics settings |
| 6 | Budget with action group and 80%/100% alerts | Screenshot of budget configuration |
| 7 | Defender for Cloud Secure Score noted | Screenshot of overview |
| 8 | Policy compliance state noted | Screenshot of compliance blade |

---

## Artefact to Produce

Complete these three artefacts using your lab evidence:

1. **[azure-platform-control-checklist.md](../artefacts/azure-platform-control-checklist.md)** — fill in the checklist from the template, marking each item as complete with the date and evidence reference
2. **[naming-and-tagging-standard.md](../artefacts/naming-and-tagging-standard.md)** — document your naming convention and tag schema as if handing it to an engineer for a new Azure engagement
3. **[budget-and-cost-guardrail.md](../artefacts/budget-and-cost-guardrail.md)** — document your budget alert configuration and cost governance approach

Artefacts must reflect your actual lab configuration. Generic text does not pass Practitioner or Senior Architect track.

---

## Cost and Clean-Up

**Estimated cost:** Log Analytics workspace ingestion is ~$3.50 AUD/GB. For a lab generating minimal logs, cost is negligible (cents per month). Budget alert, policy, and resource locks are free.

**Clean-up after lab completion:**

```bash
# Remove the resource group and all resources within it
az group delete --name rg-labenv-dev-aue-001 --yes --no-wait

# Remove the diagnostic settings on the subscription
az monitor diagnostic-settings delete \
  --name diag-activitylog-labenv \
  --resource /subscriptions/$(az account show --query id -o tsv)

# Remove the budget
az consumption budget delete --budget-name budget-labenv-dev-monthly
```

> Note: Do not delete the Log Analytics workspace if you plan to continue to Lab 02. The workspace is reused.

---

## Review Questions

1. You applied a CanNotDelete lock to the resource group. An engineer asks why a ReadOnly lock was not used instead. What is your answer?

2. Your budget alert fires at 80% on day 10 of the month. What do you investigate first?

3. Defender for Cloud shows a Secure Score of 45%. What does this tell you, and what three actions would you take first?

4. The Subscription Activity Log diagnostic settings send to Log Analytics. After three months, you notice the logs are not being retained past 30 days. What is the cause and how do you extend retention?

---

## Completion Checklist

- [ ] Resource group created with correct name and region
- [ ] Six tags applied and verified on resource group
- [ ] CanNotDelete lock applied to resource group
- [ ] Log Analytics workspace created and confirmed
- [ ] Subscription Activity Log diagnostic settings configured to workspace
- [ ] Budget created with 80% and 100% alert thresholds and action group
- [ ] Defender for Cloud Secure Score recorded
- [ ] Policy compliance state recorded
- [ ] All eight evidence items captured
- [ ] azure-platform-control-checklist.md completed with lab evidence
- [ ] naming-and-tagging-standard.md completed with your standard
- [ ] budget-and-cost-guardrail.md completed with your configuration
- [ ] All four review questions answered in writing
