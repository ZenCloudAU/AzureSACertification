# Service Principal Governance Standard

**Purpose:** Define the governance standard for service principal creation, access control, credential management, and decommission. Every service principal in the Azure environment must comply with this standard.

**Artefact owner:** [Name / role]
**Environment:** [Subscription name / ID]
**Date created:** [YYYY-MM-DD]

---

## When to Use a Service Principal

Use a service principal only when a managed identity is not available.

| Scenario | Preferred Identity | Reason |
|---|---|---|
| Azure Function App accessing Key Vault | Managed identity | Azure-hosted — managed identity available |
| GitHub Actions deploying to Azure | Service principal with federated credential | Not Azure-hosted — managed identity not available; use OIDC |
| Azure DevOps pipeline deploying Bicep | Service principal with federated credential | Not Azure-hosted — OIDC preferred over client secret |
| On-premises automation accessing Azure API | Service principal with certificate or secret | No Azure hosting — managed identity not available |
| Third-party SaaS integration | Service principal with client secret | External service — no Azure identity available |

**Rule:** Before creating a service principal, confirm that a managed identity cannot fulfil the same purpose.

---

## Service Principal Register

| App Registration Name | App ID | Auth Method | Role | Scope | Owner | Created Date | Secret / Cert Expiry | Last Reviewed |
|---|---|---|---|---|---|---|---|---|
| [Example: github-actions-labenv-deploy] | [App ID] | Federated (OIDC) | Contributor | `rg-labenv-dev-aue-001` | [Name] | [Date] | N/A | [Date] |

---

## Authentication Method Standard

### Federated Credentials (Preferred for CI/CD)

Federated credentials use OIDC to establish trust between the service principal and an external identity provider (e.g., GitHub Actions, Azure DevOps). No secret or certificate is stored or managed.

**GitHub Actions configuration:**
```yaml
- name: Azure Login
  uses: azure/login@v2
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

**Federated credential setup (Azure Portal):** App registration → Certificates & secrets → Federated credentials → Add → GitHub Actions → configure org, repo, and branch/environment

**Governance note:** No credential expiry to manage. The trust relationship is revoked by removing the federated credential from the app registration.

---

### Client Certificates (Second Preference)

Where federated credentials are not available, use a certificate rather than a client secret. Certificates have a longer valid period and are harder to exfiltrate than plain secrets.

**Governance requirements:**
- Certificate issued by internal CA or Azure Key Vault (preferred)
- Expiry date recorded in the service principal register
- Alert set 60 days before expiry
- Rotation procedure documented

---

### Client Secrets (Last Resort)

Client secrets are passwords. They are the most common source of credential exposure in Azure environments.

**Client secret governance requirements:**

| Requirement | Standard |
|---|---|
| Maximum validity period | 12 months — no non-expiring secrets |
| Storage | Azure Key Vault only — never in code, never in environment variables of non-Azure services, never in CI/CD pipeline variable (use federated credential instead) |
| Scope | Never share a single secret between multiple applications |
| Rotation | Rotate 30 days before expiry — set an alert |
| Rotation procedure | Documented per application — includes which applications use the secret and how to update them |
| Expiry alert | Azure Monitor alert or Logic App triggered by Key Vault secret expiry metadata |

**Secret rotation alert pattern:**

```
Key Vault secret [sp-labenv-deploy-secret] approaching expiry
  → Key Vault → Event Grid → Azure Function → email alert to owner
```

Or: Set a calendar reminder. If the team cannot operate a technical alert, a calendar reminder is better than nothing. But it is not sufficient for a production environment.

---

## Lifecycle Standard

### Creation

1. Document the purpose and required access in this artefact before creating the service principal
2. Create the app registration with a meaningful name: `[workload]-[purpose]-[env]`
3. Configure the authentication method (federated first, certificate second, secret last)
4. Assign the minimum required role at the minimum scope
5. Record in the Service Principal Register
6. Set expiry alert if client secret or certificate is used

### Review

- Quarterly: confirm the service principal is still in use
- Quarterly: confirm the role assignment is still the minimum required
- Quarterly: confirm the owner is still correct and active
- At team change: review whether the service principal ownership transfers or is decommissioned

### Decommission

1. Confirm the service principal is no longer in use — check sign-in logs for recent activity
2. Remove all role assignments before deleting
3. Delete the app registration (this also deletes the associated service principal)
4. Remove the entry from the Service Principal Register
5. Revoke any secrets or certificates that were issued (rotate the consuming application first if still needed)

---

## Failure Modes

**Non-expiring client secret committed to a repository.** This is the most common credential breach pattern in Azure. Once in a repo, the secret is in Git history even after deletion. Mitigation: mandatory code scanning, secret detection in CI pipelines (GitHub Secret Scanning, Azure DevOps credential scan).

**Service principal with Owner role at subscription scope.** Discovered during an access review 18 months after the engineer who created it left. The client says it was "just for the original deployment." The account still authenticates via a stored secret no one knows the location of.

**No owner recorded.** The person who created the service principal left. No one knows what it does or whether it can be deleted. Quarterly review would have caught this.

**Shared secret between multiple applications.** One application is decommissioned. The team rotates the secret. Three other applications stop working.

**Federated credential misconfigured.** The trust is set to `main` branch only but the pipeline runs from `deploy/*` branches. Authentication fails. The developer falls back to adding a client secret as the quick fix.

---

## Governance Annotation

**This environment's service principals:**

| Service Principal | Status | Notes |
|---|---|---|
| [None created in this lab] | N/A | Lab uses managed identity for all Azure-hosted workloads |
| [Future: GitHub Actions deploy SP] | Planned | Will use federated credential when pipeline is established |

**Senior SA annotation [complete for Senior Architect track]:**

- What is the governance consequence of a service principal secret expiring in production without an alert?
- Design the monitoring alert that would give 30 days' warning before a Key Vault-stored client secret expires. What Azure services are involved?
- A client says they have 47 service principals and no register. What is your first action and what is the remediation plan?
