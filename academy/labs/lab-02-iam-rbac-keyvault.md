# Lab 02 — IAM, RBAC, and Key Vault

## Goal

Create a documented identity baseline for an Azure workload — covering human access groups, a managed identity for a function app, a Key Vault with RBAC access control and audit logging, and a break-glass documentation record. Produce five artefacts that represent a production-ready IAM baseline.

---

## Scenario

The platform environment created in Lab 01 is ready. You are now defining who or what can access the Azure resources in this workload — before any application is deployed.

Workload context: a serverless integration workload consisting of an Azure Function App that reads secrets from Key Vault, writes output to a storage account, and is managed by a two-person team (yourself and one other).

You need to establish:

1. Group-based RBAC for human access — two groups with appropriate roles
2. A Key Vault with RBAC model, diagnostic settings, and correctly scoped access
3. A system-assigned managed identity on the Function App with access to Key Vault
4. A break-glass access procedure documented even though this is a lab environment
5. A service principal governance note covering the pattern for pipeline identity (without creating an actual pipeline)

---

## Prerequisites

- Lab 01 complete: resource group `rg-labenv-dev-aue-001`, Log Analytics workspace `log-labenv-dev-aue-001`
- Subscription Owner or User Access Administrator access (required for RBAC assignments)
- Entra ID permissions: ability to create groups (requires at least Groups Administrator or User Administrator in Entra ID, or the account must have Entra ID P1)
- Azure CLI installed or Azure Cloud Shell available

---

## Steps

### Step 1: Create Entra ID Security Groups

Create two security groups to manage human access to the lab workload.

| Group | Role to assign | Purpose |
|---|---|---|
| `grp-labenv-dev-contributors` | Contributor at resource group scope | Engineering team — can deploy and manage resources |
| `grp-labenv-dev-readers` | Reader at resource group scope | Monitoring and read-only access |

**Portal:** Entra ID → Groups → New group → Security → name → no members needed for lab

**CLI:**
```bash
az ad group create \
  --display-name grp-labenv-dev-contributors \
  --mail-nickname grp-labenv-dev-contributors

az ad group create \
  --display-name grp-labenv-dev-readers \
  --mail-nickname grp-labenv-dev-readers
```

Note the object IDs of both groups — you will use them for RBAC assignments.

**Evidence to capture:** Screenshot or CLI output showing both groups created with their Object IDs.

---

### Step 2: Assign RBAC Roles to Groups

Assign roles to the groups at resource group scope.

**Portal:** Resource group `rg-labenv-dev-aue-001` → Access control (IAM) → Add role assignment

Assignment 1:
- Role: Contributor
- Members: `grp-labenv-dev-contributors`
- Scope: `rg-labenv-dev-aue-001`

Assignment 2:
- Role: Reader
- Members: `grp-labenv-dev-readers`
- Scope: `rg-labenv-dev-aue-001`

**CLI:**
```bash
RG_ID=$(az group show --name rg-labenv-dev-aue-001 --query id -o tsv)
CONTRIBUTORS_OID=$(az ad group show --group grp-labenv-dev-contributors --query id -o tsv)
READERS_OID=$(az ad group show --group grp-labenv-dev-readers --query id -o tsv)

az role assignment create \
  --assignee-object-id $CONTRIBUTORS_OID \
  --assignee-principal-type Group \
  --role Contributor \
  --scope $RG_ID

az role assignment create \
  --assignee-object-id $READERS_OID \
  --assignee-principal-type Group \
  --role Reader \
  --scope $RG_ID
```

**Evidence to capture:** Screenshot of IAM → Role assignments blade showing both group assignments.

---

### Step 3: Create Key Vault with RBAC Model

Create a Key Vault configured with the RBAC permission model (not vault access policies).

Suggested name: `kv-labenv-dev-aue-001`

> Note: Key Vault names must be globally unique across Azure. If this name is taken, append a 3-digit random number.

**Portal:** Search `Key vaults` → Create

- Resource group: `rg-labenv-dev-aue-001`
- Name: `kv-labenv-dev-aue-001`
- Region: Australia East
- Pricing tier: Standard
- Access configuration tab → Permission model: **Azure role-based access control**
- Recovery: Soft delete enabled (default), Purge protection: enabled

Leave public network access enabled for this lab (Networking tab). In production, restrict to private endpoints or selected networks.

**CLI:**
```bash
az keyvault create \
  --name kv-labenv-dev-aue-001 \
  --resource-group rg-labenv-dev-aue-001 \
  --location australiaeast \
  --enable-rbac-authorization true \
  --enable-soft-delete true \
  --enable-purge-protection true \
  --sku standard
```

**Evidence to capture:** Screenshot of Key Vault overview showing name, region, and access configuration showing RBAC model enabled.

---

### Step 4: Configure Key Vault Diagnostic Settings

Route Key Vault audit logs to the Log Analytics workspace.

**Portal:** Key Vault → Diagnostics settings → Add diagnostic setting

- Name: `diag-kv-labenv-dev`
- Logs: AuditEvent, AzurePolicyEvaluationDetails
- Metrics: AllMetrics
- Destination: Send to Log Analytics workspace → `log-labenv-dev-aue-001`

**CLI:**
```bash
KV_ID=$(az keyvault show --name kv-labenv-dev-aue-001 --query id -o tsv)
WORKSPACE_ID=$(az monitor log-analytics workspace show \
  --resource-group rg-labenv-dev-aue-001 \
  --workspace-name log-labenv-dev-aue-001 \
  --query id -o tsv)

az monitor diagnostic-settings create \
  --name diag-kv-labenv-dev \
  --resource $KV_ID \
  --workspace $WORKSPACE_ID \
  --logs '[
    {"category": "AuditEvent", "enabled": true},
    {"category": "AzurePolicyEvaluationDetails", "enabled": true}
  ]' \
  --metrics '[{"category": "AllMetrics", "enabled": true}]'
```

**Evidence to capture:** Screenshot of Key Vault Diagnostics settings showing AuditEvent logs routing to Log Analytics workspace.

---

### Step 5: Create a Test Secret in Key Vault

Add a test secret to confirm the Key Vault is accessible.

**Portal:** Key Vault → Secrets → Generate/Import → Name: `test-secret-01` → Value: `lab-test-value-not-production` → Create

> Note: If you receive a permissions error, you need to assign yourself the Key Vault Administrator role first (Step 6 covers role assignment — complete Step 6 first, then return here).

**CLI:**
```bash
az keyvault secret set \
  --vault-name kv-labenv-dev-aue-001 \
  --name test-secret-01 \
  --value "lab-test-value-not-production"
```

**Evidence to capture:** Screenshot of Key Vault Secrets blade showing the test secret created.

---

### Step 6: Assign Key Vault Roles

Assign roles on the Key Vault to your groups and yourself.

| Principal | Role | Reason |
|---|---|---|
| Your user account | Key Vault Administrator | Manage the vault during the lab |
| `grp-labenv-dev-contributors` | Key Vault Secrets User | Read secrets (application access pattern) |
| `grp-labenv-dev-readers` | Key Vault Reader | View vault metadata, not secrets |

**Portal:** Key Vault → Access control (IAM) → Add role assignment (repeat for each assignment)

**CLI:**
```bash
KV_ID=$(az keyvault show --name kv-labenv-dev-aue-001 --query id -o tsv)
MY_OID=$(az ad signed-in-user show --query id -o tsv)
CONTRIBUTORS_OID=$(az ad group show --group grp-labenv-dev-contributors --query id -o tsv)
READERS_OID=$(az ad group show --group grp-labenv-dev-readers --query id -o tsv)

az role assignment create \
  --assignee-object-id $MY_OID \
  --assignee-principal-type User \
  --role "Key Vault Administrator" \
  --scope $KV_ID

az role assignment create \
  --assignee-object-id $CONTRIBUTORS_OID \
  --assignee-principal-type Group \
  --role "Key Vault Secrets User" \
  --scope $KV_ID

az role assignment create \
  --assignee-object-id $READERS_OID \
  --assignee-principal-type Group \
  --role "Key Vault Reader" \
  --scope $KV_ID
```

**Evidence to capture:** Screenshot of Key Vault IAM → Role assignments showing all three assignments.

---

### Step 7: Create a Function App with System-Assigned Managed Identity

Create an Azure Function App and enable its system-assigned managed identity.

Suggested name: `func-labenv-dev-aue-001`

**Portal:** Search `Function App` → Create

- Resource group: `rg-labenv-dev-aue-001`
- Function App name: `func-labenv-dev-aue-001`
- Runtime: Python 3.11 or .NET 8 (either works for identity testing)
- Region: Australia East
- Hosting plan: Consumption (serverless)

After creation: Function App → Settings → Identity → System assigned → Status: On → Save → Copy the Object ID displayed

**CLI:**
```bash
az functionapp create \
  --name func-labenv-dev-aue-001 \
  --resource-group rg-labenv-dev-aue-001 \
  --storage-account stlabenvdevaue001 \
  --consumption-plan-location australiaeast \
  --runtime python \
  --runtime-version 3.11 \
  --os-type Linux \
  --assign-identity [system]
```

> The Function App requires a storage account. Create one first if you have not already: `az storage account create --name stlabenvdevaue001 --resource-group rg-labenv-dev-aue-001 --location australiaeast --sku Standard_LRS`

**Evidence to capture:** Screenshot of Function App Identity blade showing System assigned status: On and the Object (principal) ID.

---

### Step 8: Assign Key Vault Secrets User to Managed Identity

Assign the managed identity to the Key Vault so the Function App can read secrets without a credential.

**Portal:** Key Vault → Access control (IAM) → Add role assignment → Key Vault Secrets User → Managed identity → select Function App identity

**CLI:**
```bash
FUNC_MI_OID=$(az functionapp identity show \
  --name func-labenv-dev-aue-001 \
  --resource-group rg-labenv-dev-aue-001 \
  --query principalId -o tsv)

KV_ID=$(az keyvault show --name kv-labenv-dev-aue-001 --query id -o tsv)

az role assignment create \
  --assignee-object-id $FUNC_MI_OID \
  --assignee-principal-type ServicePrincipal \
  --role "Key Vault Secrets User" \
  --scope $KV_ID
```

**Evidence to capture:** Screenshot of Key Vault IAM showing the managed identity role assignment for the Function App.

---

## Evidence to Capture

| # | Item | Method |
|---|---|---|
| 1 | Two Entra ID security groups with Object IDs | Screenshot or CLI output |
| 2 | RBAC assignments on resource group (Contributor and Reader) | Screenshot of IAM blade |
| 3 | Key Vault created with RBAC model and purge protection | Screenshot of overview + access configuration |
| 4 | Key Vault diagnostic settings routing AuditEvent to Log Analytics | Screenshot of diagnostics |
| 5 | Test secret created in Key Vault | Screenshot of Secrets blade |
| 6 | Key Vault role assignments (Administrator, Secrets User, Reader) | Screenshot of IAM blade |
| 7 | Function App with system-assigned managed identity enabled | Screenshot of Identity blade |
| 8 | Managed identity assigned Key Vault Secrets User role | Screenshot of Key Vault IAM |

---

## Artefact to Produce

Complete these five artefacts:

1. **[iam-rbac-matrix.md](../artefacts/iam-rbac-matrix.md)** — record every identity, role, scope, and control in the RBAC matrix template
2. **[managed-identity-pattern.md](../artefacts/managed-identity-pattern.md)** — document the managed identity design decision for this Function App
3. **[service-principal-governance-standard.md](../artefacts/service-principal-governance-standard.md)** — record the governance standard for service principals (even if none are created in this lab — document the pattern for a future CI/CD pipeline identity)
4. **[key-vault-access-pattern.md](../artefacts/key-vault-access-pattern.md)** — document the Key Vault access design: model, roles, diagnostics, and access decisions
5. **[break-glass-access-procedure.md](../artefacts/break-glass-access-procedure.md)** — document the break-glass access procedure for this Azure environment

---

## Cost and Clean-Up

**Estimated cost:** Function App on Consumption plan is free for the first 1M executions (lab will not come close). Storage account ~$0.02/month. Key Vault ~$0.50/month. Log Analytics ingestion negligible for lab logs.

**Total estimated cost:** under $1 AUD/month while the lab resources exist.

**Clean-up (when lab is complete and artefacts are saved):**

```bash
# Delete all lab resources by deleting the resource group
az group delete --name rg-labenv-dev-aue-001 --yes --no-wait

# Delete the Key Vault (note: soft delete means it still exists for the retention period)
# Force-purge only if purge protection is NOT enabled (it is enabled in this lab, so cannot purge within retention period)

# Delete Entra ID groups
az ad group delete --group grp-labenv-dev-contributors
az ad group delete --group grp-labenv-dev-readers

# Delete the diagnostic settings on the subscription (if not already removed)
az monitor diagnostic-settings delete \
  --name diag-activitylog-labenv \
  --resource /subscriptions/$(az account show --query id -o tsv)
```

> The Key Vault will remain in soft-deleted state for the configured retention period (7–90 days) due to purge protection. This is expected and correct behaviour. The vault is not billed during soft-delete.

---

## Review Questions

1. You assigned Key Vault Secrets User to the managed identity instead of Key Vault Contributor. What is the practical difference and why does this matter for production?

2. The Function App managed identity can read secrets. A developer asks if they can also use the same managed identity to write secrets and rotate keys. What do you say, and what would the correct access pattern be?

3. Your team has no PIM licensing. You are using permanent group-based Contributor access. What three mitigations do you put in place to reduce the risk of over-privileged standing access?

4. You need to rotate the `test-secret-01` secret. Walk through the rotation procedure and explain what the application needs to do to pick up the new version without redeployment.

---

## Completion Checklist

- [ ] Two Entra ID security groups created with Object IDs recorded
- [ ] Contributor role assigned to contributors group at resource group scope
- [ ] Reader role assigned to readers group at resource group scope
- [ ] Key Vault created with RBAC model enabled and purge protection on
- [ ] Key Vault diagnostic settings routing AuditEvent to Log Analytics workspace
- [ ] Test secret created and visible in Key Vault Secrets blade
- [ ] Key Vault Administrator, Secrets User, and Reader roles assigned correctly
- [ ] Function App created with system-assigned managed identity enabled
- [ ] Managed identity assigned Key Vault Secrets User role on the Key Vault
- [ ] All eight evidence items captured
- [ ] iam-rbac-matrix.md completed with all identities, roles, and scopes
- [ ] managed-identity-pattern.md completed with design decision for this Function App
- [ ] service-principal-governance-standard.md completed with pipeline identity pattern
- [ ] key-vault-access-pattern.md completed with access design
- [ ] break-glass-access-procedure.md completed with emergency access procedure
- [ ] All four review questions answered in writing
