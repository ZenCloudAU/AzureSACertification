# Azure Technical Validation

**Purpose:** Verify that all Azure content in the course is technically accurate, current, and reflects real service behaviour and architecture best practice. Executed by the Senior Azure Solution Architect before each release.

**Senior SA:** [Name]
**Release:** [Phase 1 / Phase 2]
**Date executed:** [YYYY-MM-DD]
**Azure content last verified against:** Azure documentation as of [YYYY-MM]

---

## Validation Approach

For each item below, the Senior SA verifies accuracy by:
1. Cross-referencing against current Azure documentation (Microsoft Learn, Azure portal)
2. Confirming against real lab execution where applicable
3. Flagging any item that reflects deprecated behaviour, renamed services, or changed defaults

Items that cannot be verified at the time of release are flagged as `Unverified — defer` with a date for re-check.

---

## 1. Service Names and Portal Paths

| Item | Expected | Verified | Notes |
|---|---|---|---|
| "Azure Active Directory" referred to as "Microsoft Entra ID" throughout | Yes | | |
| Key Vault portal path confirmed current | portal.azure.com → Key vaults | | |
| Entra ID portal path confirmed current | entra.microsoft.com or portal.azure.com → Microsoft Entra ID | | |
| Defender for Cloud portal path confirmed current | portal.azure.com → Microsoft Defender for Cloud | | |
| Cost Management portal path confirmed current | portal.azure.com → Cost Management + Billing | | |
| Activity Log diagnostic settings path confirmed | Subscriptions → [sub] → Activity log → Diagnostic settings | | |

---

## 2. Module 01 — Platform Control Baseline

| Claim | Accurate | Notes / Correction |
|---|---|---|
| Management group hierarchy described matches ALZ reference | | |
| Resource group name limits: letters, numbers, hyphens, underscores, periods up to 90 chars | | |
| Storage account name constraints: lowercase letters and numbers, 3–24 chars | | |
| CAF naming abbreviations (rg, vnet, kv, st, func, log) | | |
| Azure Policy effects: Deny, Audit, Modify, DeployIfNotExists described accurately | | |
| Budget alert is retrospective — not a hard block | | |
| CanNotDelete lock: resource can be modified but not deleted | | |
| ReadOnly lock: resource cannot be modified or deleted | | |
| Defender for Cloud CSPM tier is free | | |
| Activity Log default retention: 90 days | | |
| Log Analytics workspace ingestion cost: ~$3.50 AUD/GB approximate | | |
| Australia East region abbreviation: aue | | |

---

## 3. Module 02 — IAM and Entra Architecture

| Claim | Accurate | Notes / Correction |
|---|---|---|
| RBAC is additive — broader scope wins | | |
| Owner: full control including role assignments | | |
| Contributor: full control except role assignments and Azure Policy | | |
| Key Vault Secrets User: cannot list secrets, can get by URI | | |
| Key Vault RBAC switch is one-way — cannot revert to vault access policies | | |
| Soft delete: enabled by default since 2021 (mandatory) | | |
| Purge protection: prevents permanent deletion during retention period | | |
| System-assigned managed identity: deleted with the resource | | |
| User-assigned managed identity: persists independently of any resource | | |
| Federated credentials use OIDC | | |
| RBAC propagation delay: can take up to 10 minutes | | |
| Entra ID Audit/Sign-in logs: 7 days free tier, 30 days P1/P2 | | |
| PIM requires Entra ID P2 | | |
| Conditional Access requires Entra ID P1 | | |
| Key Vault `az keyvault update --enable-rbac-authorization true` is the correct CLI flag | | |

---

## 4. Lab 01 — Platform Control Baseline

| Claim / Step | Accurate | Notes / Correction |
|---|---|---|
| `az group create` syntax with --tags is correct | | |
| `az lock create --lock-type CanNotDelete` syntax is correct | | |
| `az monitor log-analytics workspace create` syntax is correct | | |
| Activity Log diagnostic settings CLI syntax is correct | | |
| `az consumption budget create` syntax and parameters are current | | |
| Budget CLI parameters (--notification-key, --notification-operator, --notification-threshold) are current | | |
| Defender for Cloud is accessible at portal.azure.com without additional configuration | | |

---

## 5. Lab 02 — IAM, RBAC, and Key Vault

| Claim / Step | Accurate | Notes / Correction |
|---|---|---|
| `az ad group create --mail-nickname` is required parameter | | |
| `az role assignment create --assignee-principal-type Group` is required for groups | | |
| `az keyvault create --enable-rbac-authorization true` enables RBAC model | | |
| `az keyvault create --enable-purge-protection true` syntax is correct | | |
| Key Vault diagnostic settings categories: AuditEvent, AzurePolicyEvaluationDetails are current | | |
| `az keyvault secret set` syntax is correct | | |
| `az functionapp create --assign-identity [system]` syntax is correct | | |
| `az functionapp identity show --query principalId` returns the managed identity object ID | | |
| Function App Consumption plan is free for first 1M executions | | |
| Key Vault IMDS authentication via DefaultAzureCredential is the correct pattern | | |
| KQL query for SecretGet audit in AzureDiagnostics is syntactically correct | | |

---

## 6. Artefacts

| Item | Accurate | Notes / Correction |
|---|---|---|
| Key Vault Secrets User role described correctly in iam-rbac-matrix | | |
| Managed identity RBAC propagation delay noted (up to 10 minutes) | | |
| Service principal federated credential OIDC pattern described accurately | | |
| Break-glass account guidance matches Microsoft's documented recommendations | | |
| Key Vault soft delete recovery procedure is accurate | | |
| Secret rotation "latest version URI" pattern (omitting version) is accurate | | |
| Break-glass KQL query against SigninLogs is syntactically correct | | |

---

## 7. Deprecated or Renamed Content

| Item | Status | Correction Required |
|---|---|---|
| Any reference to "Azure Active Directory" without Entra ID notation | Check | |
| ARM template references (should note Bicep as preferred) | Check | |
| Any "AAD" abbreviation | Check | |
| Classic portal references | Check | |

---

## Azure Technical Validation Result

| Section | Status | Notes |
|---|---|---|
| Service names and portal paths | | |
| Module 01 claims | | |
| Module 02 claims | | |
| Lab 01 steps | | |
| Lab 02 steps | | |
| Artefacts | | |
| Deprecated content | | |

**Overall technical validation status:** [ ] Pass — all claims verified accurate / [ ] Revise — corrections required (list below) / [ ] Conditional — unverified items deferred

**Senior SA sign-off:** [Name] — [Date]

---

## Corrections Required

| File | Line / Section | Issue | Required Correction |
|---|---|---|---|
| | | | |

---

## Unverified Items (Deferred)

| Item | Why unverified | Re-check date |
|---|---|---|
| | | |

---

## Version Note

> Azure services change. Content verified as of [YYYY-MM]. Any module section describing specific portal paths, CLI syntax, or pricing should be re-verified within 6 months of the last review date. Flag sections with `> Note: verify against current Azure documentation — last reviewed [month year].` if there is specific reason to expect drift.
