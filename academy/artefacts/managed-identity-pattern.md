# Managed Identity Pattern

**Purpose:** Document the managed identity design decision for each Azure-hosted workload component. This record explains what identity is used, why, what it accesses, and what would change this design.

**Artefact owner:** [Name / role]
**Environment:** [Subscription name / ID]
**Date created:** [YYYY-MM-DD]

---

## Pattern Overview

Managed identities eliminate the need to store credentials in application configuration or code. Azure manages the identity lifecycle. The application uses the Azure IMDS (Instance Metadata Service) or Azure SDK to obtain tokens at runtime.

**Default rule:** Use managed identities for any Azure-hosted workload accessing Azure services. Only use service principals when managed identity is not technically available.

---

## Managed Identity Decisions — This Environment

### Identity 1 — Function App

| Field | Value |
|---|---|
| Resource name | `func-labenv-dev-aue-001` |
| Resource type | Azure Function App |
| Identity type | System-assigned |
| Reason for system-assigned | Single resource — identity lifecycle should match resource lifecycle |
| Object ID (Principal ID) | [from Identity blade] |
| Enabled | Yes |
| Date enabled | [YYYY-MM-DD] |

**Access granted:**

| Target Resource | Role Assigned | Scope | Reason |
|---|---|---|---|
| `kv-labenv-dev-aue-001` | Key Vault Secrets User | Key Vault resource | Read application secrets — no credential in configuration |

**What the application does:**

The Function App uses the Azure SDK's `DefaultAzureCredential` or `ManagedIdentityCredential` to authenticate to Key Vault. No connection string or client secret is stored in the function configuration. The token is issued by Entra ID and presented to Key Vault, which validates it against the role assignment before granting access.

**Code pattern (Python):**
```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

credential = DefaultAzureCredential()
client = SecretClient(vault_url="https://kv-labenv-dev-aue-001.vault.azure.net", credential=credential)
secret = client.get_secret("my-secret-name")
```

**Code pattern (.NET):**
```csharp
var credential = new DefaultAzureCredential();
var client = new SecretClient(new Uri("https://kv-labenv-dev-aue-001.vault.azure.net"), credential);
KeyVaultSecret secret = client.GetSecret("my-secret-name");
```

---

## User-Assigned Managed Identity Pattern

> Not used in this lab. Document this pattern for future reference.

**When to use user-assigned:**

| Scenario | Why User-Assigned |
|---|---|
| Multiple Function Apps need the same Key Vault access | Assign one identity to multiple apps — consistent RBAC |
| Identity must survive resource recreation (e.g., blue-green deploy) | System-assigned is deleted with the resource; user-assigned persists |
| CI/CD pipeline needs to deploy infrastructure | Pipeline tools can reference the identity by ID; system-assigned is harder to pre-configure |
| Shared services access pattern | One user-assigned identity for all app tier components accessing a shared service |

**Lifecycle for user-assigned identity:**

```
Create: az identity create --name id-labenv-dev-aue-001 --resource-group rg-labenv-dev-aue-001
Assign: az [resource-type] identity assign --identities [user-assigned-id] ...
RBAC: az role assignment create --assignee-object-id [object-id] ...
Review: quarterly access audit
Delete: az identity delete (only when no resources are using it)
```

---

## Decision Record: System-Assigned vs User-Assigned

For this lab's Function App:

| Factor | System-Assigned | User-Assigned |
|---|---|---|
| Lifecycle binding | Tied to Function App — deleted with it | Independent — persists if Function App is deleted |
| RBAC assignment | One-to-one per resource | One identity, many resources |
| IaC complexity | Simpler — auto-created by resource definition | Requires separate resource creation |
| Identity sharing | Not possible — one per resource | Possible — same identity on multiple resources |
| **Decision** | **Selected** | Not selected |
| **Reason** | Single Function App, lab context, lifecycle alignment is correct | N/A |

---

## Failure Modes to Watch

**Identity is not enabled.** The application deploys, calls Key Vault, receives a 401. The developer adds a client secret to environment variables as a quick fix. The fix ships to production. The managed identity was never confirmed.

*Prevention:* Verify identity is enabled in the Identity blade immediately after deployment. Verify RBAC assignment is confirmed. Test the authentication call before any other debugging.

**RBAC propagation delay.** Managed identity is enabled and RBAC is assigned, but the application still gets 403 errors for up to 10 minutes. RBAC propagation is not instant.

*Prevention:* Wait 5–10 minutes after role assignment before testing.

**Scope too broad.** The team assigns Contributor to the managed identity at the subscription level because Key Vault Secrets User was "confusing." The Function App now has Contributor on the entire subscription.

*Prevention:* Assign the narrowest role at the narrowest scope. Document the reasoning in this artefact.

**System-assigned identity deleted accidentally.** The Function App is redeployed and the system-assigned identity's Object ID changes. The RBAC assignment on Key Vault now points to a deleted identity. The application fails.

*Prevention:* For workloads where identity continuity matters across deployments, use user-assigned managed identity. Record identity Object IDs in this artefact and the RBAC matrix.

---

## Governance Notes

**Design decision:** System-assigned managed identity chosen for the lab Function App because the identity lifecycle should match the resource. If this were a production workload with multiple app components sharing Key Vault access, user-assigned would be required.

**Risk accepted:** No risk accepted on managed identity implementation. Managed identity is the required default.

**Handover note for operations:** The Function App authenticates to Key Vault via managed identity — no credentials to rotate. If the Function App is redeployed or recreated, the system-assigned identity changes. The new Object ID must be assigned the Key Vault Secrets User role before the application can access secrets.

**Senior SA annotation [complete for Senior Architect track]:**

- Describe when this pattern would break in a production scenario:
- What would you change if this workload needed to access three different Azure services (Key Vault, Storage, Service Bus)?
- How does managed identity interact with private endpoints? What changes?
