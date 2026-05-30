# Key Vault Access Pattern

**Purpose:** Document the Key Vault access design for this environment — access model, role assignments, network configuration, audit logging, and operational controls. Operations teams should be able to answer "who can access secrets?" from this document alone.

**Artefact owner:** [Name / role]
**Vault name:** `kv-labenv-dev-aue-001`
**Environment:** [Subscription name / ID]
**Date created:** [YYYY-MM-DD]
**Date last reviewed:** [YYYY-MM-DD]

---

## Vault Configuration

| Setting | Value | Reason |
|---|---|---|
| Vault name | `kv-labenv-dev-aue-001` | Per naming standard |
| Region | Australia East | Aligned with workload region |
| Resource group | `rg-labenv-dev-aue-001` | Per lifecycle boundary |
| SKU tier | Standard | HSM not required for this workload |
| Permission model | Azure RBAC | Current recommended model — not vault access policies |
| Soft delete | Enabled | 90-day recovery window — protects against accidental deletion |
| Purge protection | Enabled | Prevents permanent deletion during retention period |
| Public network access | Enabled (lab) / Disabled (production) | Lab: enabled for simplicity. Production: private endpoint required. |

---

## Access Model

**Model used:** Azure RBAC

The RBAC model is set at vault creation. Once set to RBAC, vault access policies cannot be re-enabled. All access control is managed via Azure RBAC role assignments on the Key Vault resource.

**Model comparison:**

| Aspect | RBAC Model | Vault Access Policies (legacy) |
|---|---|---|
| Audit tooling | Standard Azure Activity Log + RBAC audit | Custom vault audit only |
| Conditional Access integration | Yes — through Entra ID | No |
| Inheritance | Yes — from management group/subscription/resource group | No — vault-level only |
| Scope granularity | Secret, Key, Certificate level (limited) | Operation-level per object type |
| Recommendation | **New deployments** | Legacy — migrate when possible |

---

## Role Assignments

| Principal | Type | Role | Scope | Reason |
|---|---|---|---|---|
| [your@email.com] | User | Key Vault Administrator | This vault | Lab administration — temporary |
| `grp-labenv-dev-contributors` | Group | Key Vault Secrets User | This vault | Application access pattern — read secrets only |
| `grp-labenv-dev-readers` | Group | Key Vault Reader | This vault | View vault metadata — not secrets |
| `func-labenv-dev-aue-001` (system MI) | Managed identity | Key Vault Secrets User | This vault | Application runtime — read secrets, no credential stored |

**Role definitions:**

| Role | Can list secrets | Can get secret value | Can set/delete secrets | Can manage vault |
|---|---|---|---|---|
| Key Vault Administrator | Yes | Yes | Yes | Yes |
| Key Vault Secrets Officer | Yes | Yes | Yes | No |
| Key Vault Secrets User | No | Yes (by URI) | No | No |
| Key Vault Reader | Yes (names only) | No | No | No |

> Note: Key Vault Secrets User can retrieve a secret value by URI but cannot list secrets. The application must know the secret name or URI. This is the correct access pattern for application identities — they should not enumerate all secrets.

---

## Secrets Inventory

| Secret Name | Purpose | Consuming Application | Owner | Rotation Period | Last Rotated |
|---|---|---|---|---|---|
| `test-secret-01` | Lab test secret | None | [Name] | N/A (lab only) | [Date] |
| [Future: db-connection-string] | Database connection | Function App | [Name] | 90 days | |
| [Future: api-key-external-service] | Third-party API key | Function App | [Name] | Per vendor requirement | |

---

## Keys Inventory

> No keys stored in this vault currently. Keys are used for customer-managed encryption (CMK) scenarios.

| Key Name | Purpose | Key Type | Rotation Period |
|---|---|---|---|
| [None] | | | |

---

## Certificates Inventory

> No certificates stored in this vault currently.

| Certificate Name | Purpose | Issuer | Expiry | Auto-Renewal |
|---|---|---|---|---|
| [None] | | | | |

---

## Diagnostic Settings

| Setting | Value |
|---|---|
| Diagnostic setting name | `diag-kv-labenv-dev` |
| AuditEvent logs | Enabled |
| AzurePolicyEvaluationDetails logs | Enabled |
| AllMetrics | Enabled |
| Destination | Log Analytics workspace `log-labenv-dev-aue-001` |

**What AuditEvent captures:** Every read, write, delete, and list operation on secrets, keys, and certificates — including the identity that performed the operation.

**KQL query to check who read a secret in the last 7 days:**

```kql
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| where OperationName == "SecretGet"
| where TimeGenerated > ago(7d)
| project TimeGenerated, CallerIPAddress, identity_claim_upn_s, requestUri_s, resultType
| order by TimeGenerated desc
```

---

## Network Configuration

| Setting | Lab Value | Production Requirement |
|---|---|---|
| Public network access | Enabled | Disabled — private endpoint only |
| Firewall — allow trusted Microsoft services | Enabled | Enabled |
| Private endpoint | Not configured | Required — one per VNet that needs access |
| Selected networks | None configured | Specific VNet subnets if not using private endpoint |

**Production network pattern:**

```
Application VNet (snet-app-dev-aue-001)
  → Private endpoint → Key Vault private IP (10.x.x.x)
  → DNS: privatelink.vaultcore.azure.net → private IP via Azure Private DNS Zone
  → No public internet access to vault
```

---

## Operational Controls

### Soft Delete Recovery

If a secret, key, or certificate is accidentally deleted:

1. Navigate to Key Vault → Objects → Secrets → Manage deleted secrets
2. Select the deleted secret → Recover
3. Recovery is available for the soft delete retention period (90 days in this vault)

If the vault itself is deleted:

1. `az keyvault list-deleted --resource-type vault`
2. `az keyvault recover --name kv-labenv-dev-aue-001`
3. Recovery is available during the retention period

Note: Purge protection is enabled on this vault. The vault and its contents cannot be permanently deleted until the retention period expires, even by the Owner.

### Secret Rotation Procedure

When a secret must be rotated:

1. Create a new secret version in Key Vault (do not delete the old version yet)
2. Update the consuming application to reference the new version or to use the latest version (recommended)
3. Confirm the application is using the new secret (check application logs)
4. Disable the old secret version in Key Vault (do not delete yet — allows rollback)
5. After 24 hours of confirmed operation, delete the old version

**Application reference pattern:** Use the latest version URI to avoid hardcoding version IDs:
```
Secret URI: https://kv-labenv-dev-aue-001.vault.azure.net/secrets/my-secret
(Omitting the version fetches the current enabled version)
```

---

## Governance Notes

**Design decision:** RBAC model over vault access policies. The RBAC model provides audit consistency, conditional access support, and standard Azure tooling. Vault access policies are a legacy model — new vaults should always use RBAC.

**Risk accepted:** Public network access is enabled in the lab environment. In production, this must be changed to private endpoint with no public access. This is documented as a known deviation from the production baseline.

**Handover note for operations:** All access to this Key Vault goes through Azure RBAC. To audit who has access: Key Vault → Access control (IAM) → Role assignments. To audit who read which secret: Log Analytics workspace → AzureDiagnostics table → filter OperationName = SecretGet. Secret rotation requires application validation before the old version is deleted.

**Senior SA annotation [complete for Senior Architect track]:**

- What is the architectural consequence of not enabling purge protection on a production Key Vault?
- A developer accidentally puts a plain-text password in Azure DevOps pipeline variables rather than reading from Key Vault. What is the governance failure, and what process change prevents recurrence?
- Design the alerting configuration that notifies the operations team within 5 minutes of a break-glass account or an unknown identity accessing Key Vault.
