# Module 02 — IAM and Entra Architecture

## Purpose

Rebuild architect-level identity and access management awareness for Azure. By the end of this module, you can design and review who or what accesses every Azure resource in a workload, and explain the controls that govern that access.

---

## Architect Briefing

Identity is not a feature of Azure. It is the control plane. Every access decision in Azure — human or service — flows through Microsoft Entra ID (formerly Azure Active Directory). If your last serious Azure engagement predates 2022, expect the naming, portal experience, and best-practice baseline to look different. The concepts are the same; the tooling has matured significantly.

Key shifts since 2021:

- **Azure Active Directory is now Microsoft Entra ID.** The rebrand is cosmetic but the portal reorganisation is not. AAD is now found under `entra.microsoft.com` and `portal.azure.com > Microsoft Entra ID`. The Azure RBAC blade remains in the resource itself.
- **Managed identities are the default.** If an engineer proposes using a service principal with a client secret for service-to-service authentication within Azure, the correct response is: why not a managed identity? Managed identities eliminate credential management entirely.
- **Azure Key Vault has two access models.** The legacy vault access policy model and the RBAC-based model. New deployments should use the RBAC model. Mixing them on the same vault is a governance anti-pattern.
- **PIM (Privileged Identity Management) is now standard.** Standing privileged access — permanent Owner or Global Administrator assignments — is the failure mode. Just-in-time (JIT) elevation via PIM is the control.
- **Conditional Access is the policy enforcement layer for human access.** MFA by itself is not sufficient. Conditional Access policies enforce device compliance, location restrictions, and sign-in risk thresholds.

---

## Engineering View

### Entra ID Tenant

The Entra ID tenant is the organisational identity authority. It stores:

- Users and groups
- App registrations and service principals
- Conditional Access policies
- Directory roles (Global Administrator, Application Administrator, etc.)

One tenant can govern multiple Azure subscriptions. The tenant is associated with a primary domain (e.g., `zencloud.com.au`) and can have additional verified domains.

Architect decision: **multi-tenant vs single-tenant.** Enterprise environments use one tenant with RBAC and Conditional Access to control access. Multi-tenant architectures introduce federation complexity and are only justified for specific B2B or SaaS scenarios.

### Azure RBAC

Azure RBAC controls who can do what to which Azure resources. It is separate from Entra ID directory roles (which control tenant-level administration).

RBAC components:

| Component | Description |
|---|---|
| **Security principal** | Who — user, group, service principal, or managed identity |
| **Role definition** | What — a collection of permissions (actions, not-actions, data-actions) |
| **Scope** | Where — management group, subscription, resource group, or resource |
| **Role assignment** | The binding of principal + role + scope |

RBAC is additive: if a user has Contributor at the subscription and Reader at a resource group, the effective permission is Contributor at that resource group (the broader permission wins).

Built-in roles to know at architecture depth:

| Role | Scope | Note |
|---|---|---|
| Owner | Any | Full control including role assignments — use sparingly and with JIT |
| Contributor | Any | Full control except role assignments and Azure Policy |
| Reader | Any | Read-only — safe for audit and monitoring accounts |
| User Access Administrator | Any | Manages RBAC assignments — do not grant permanently |
| Key Vault Administrator | Key Vault | Full control of vault — use RBAC model |
| Key Vault Secrets User | Key Vault | Read secrets only — use for application identities |
| Storage Blob Data Contributor | Storage | Read, write, delete blobs — preferred over storage account keys |
| Storage Blob Data Reader | Storage | Read blobs only — use for read-only service access |

Architect rule: **assign roles to groups, not individuals.** Individual role assignments break when people leave, change roles, or are part of multiple teams. Group-based RBAC is manageable.

Architect rule: **least privilege, narrowest scope.** Never assign Owner at the subscription scope unless it is a break-glass account. Never assign Contributor to a service identity when Key Vault Secrets User is sufficient.

### Managed Identities

Managed identities are Azure-managed credentials for Azure resources. The credential lifecycle (creation, rotation, deletion) is handled by Azure — you never see or store the credential.

**System-assigned managed identity:** Tied to a specific resource. Created when the resource is created, deleted when the resource is deleted. Use when the identity should not outlive the resource.

**User-assigned managed identity:** Created as a standalone resource. Can be assigned to multiple resources. Persists independently. Use when multiple resources share the same access pattern, or when the identity lifecycle should be managed independently of any single resource.

Decision matrix:

| Scenario | System-Assigned | User-Assigned |
|---|---|---|
| Single Function App accessing Key Vault | Preferred | Acceptable |
| Multiple Function Apps with same access pattern | Not ideal | Preferred |
| Identity must persist after resource recreation | Not suitable | Required |
| IaC-managed deployment pipeline identity | Not suitable | Required |

To use a managed identity: enable it on the resource, then assign RBAC roles to the managed identity at the target service scope.

Example: Function App reads from Key Vault

```
Function App (system-assigned identity enabled)
  → RBAC assignment: Key Vault Secrets User → Key Vault [kv-labenv-dev-aue-001]
  → No connection string, no client secret, no credential in configuration
```

### Service Principals

A service principal is the identity object used by an application or automation process. Every app registration creates a service principal.

Service principals use one of:

- **Client secret** — a password. Must be rotated. Cannot be managed identity. High risk if stored in code or configuration.
- **Certificate** — better than a secret. Still requires lifecycle management.
- **Federated credential** — the modern approach. No secret or certificate. The service principal trusts an external identity provider (e.g., GitHub Actions, Azure DevOps) via an OIDC token. Zero credential storage.

Architect rule: prefer federated credentials for CI/CD pipeline identities. Prefer managed identities for Azure-hosted workloads. Use client secrets only when managed identity and federated credentials are not available.

Service principal governance requires:

- Owner recorded (who is responsible for this SP)
- Purpose documented
- Expiry managed (client secrets expire — set alerts, not manual reminders)
- Least-privilege RBAC
- Audit log review on a schedule

### Key Vault

Azure Key Vault stores secrets, keys, and certificates with controlled access and full audit logging.

**Access models:**

| Model | Description | Recommendation |
|---|---|---|
| Vault access policies (legacy) | Flat list of identities with permissions | Use only for existing deployments that cannot migrate |
| Azure RBAC (current) | Standard RBAC assignments on the vault | Use for all new Key Vaults |

**Critical settings every Key Vault must have:**

| Setting | Required State | Reason |
|---|---|---|
| Soft delete | Enabled (mandatory as of 2021) | Protects against accidental deletion — 7–90 day recovery window |
| Purge protection | Enabled for production | Prevents permanent deletion during retention period |
| Public network access | Disabled (preferred) or restricted by firewall | Limits network attack surface |
| Diagnostic settings | Enabled → Log Analytics workspace | Required for audit — who read what secret and when |
| RBAC authorization enabled | Yes (not vault access policies) | Consistent, auditable access model |

**Common Key Vault architecture pattern:**

```
Key Vault [kv-labenv-dev-aue-001]
├─ Secrets: connection strings, API keys, passwords
├─ Keys: CMK (customer-managed keys) for storage encryption
└─ Certificates: TLS certificates for applications

Access:
├─ Application identity (managed identity) → Key Vault Secrets User role
├─ DevOps pipeline (service principal / federated) → Key Vault Secrets User role
└─ Operations team group → Key Vault Reader role + Key Vault Secrets User role
   (not Key Vault Administrator — that is a break-glass scope)
```

### Conditional Access

Conditional Access is the policy enforcement layer for Entra ID sign-ins. It evaluates conditions and enforces access controls based on:

| Condition | Examples |
|---|---|
| User or group | Specific users, guest users, all users |
| Application | Azure portal, Microsoft 365, custom apps |
| Location | Named locations (IP ranges), countries |
| Device platform | Windows, iOS, Android |
| Sign-in risk | Low, medium, high (requires Entra ID P2) |
| Device compliance | Intune-compliant devices only |

Controls available:

- Require MFA
- Require compliant device
- Require managed device (Hybrid Azure AD joined)
- Block access
- Require terms of use acceptance

For an Azure lab environment, a minimum Conditional Access policy baseline:

1. Require MFA for all users accessing the Azure portal
2. Block access from countries you do not operate in
3. Block access for sign-in risk = high (if Entra ID P2 available)

### Privileged Identity Management (PIM)

PIM controls who has privileged access and for how long. Instead of permanent Owner or Global Administrator assignments, PIM provides:

- **Eligible assignments:** the user can request activation but does not have the role continuously
- **Active assignments:** the role is active for a defined time period (e.g., 4 hours)
- **Activation requirements:** MFA, justification, approval from a second person

PIM is available with Entra ID P2 (included in Microsoft 365 E3/E5 and Azure AD Premium P2 licences).

For a lab environment without PIM licensing: document what PIM would enforce and why. The absence of PIM is a risk to record, not a permission to skip the analysis.

### Break-Glass Access

Break-glass accounts are emergency access accounts used only when normal authentication paths fail — for example, when MFA is unavailable or a Global Administrator account is compromised.

Break-glass requirements:

- Two accounts minimum
- No MFA dependency (federated identity, Conditional Access, and MFA policies excluded)
- Not linked to any individual (shared, organisation-controlled)
- Password stored physically offline (sealed envelope in physical safe)
- Alert on use — diagnostic settings must capture sign-ins and alert immediately when these accounts are used
- Reviewed regularly to confirm they still work

Every Azure tenant should have documented break-glass accounts. An architect who signs off on an Azure design without confirming break-glass access exists is missing a critical governance control.

---

## Azure Service Map

| Service | Role | Key Setting |
|---|---|---|
| Microsoft Entra ID | Identity authority for all Azure access | Tenant, users, groups, app registrations |
| Azure RBAC | Controls who can do what to which resources | Role definition + scope + security principal |
| Managed Identities | Credential-free service-to-service authentication | System-assigned or user-assigned |
| Service Principals | Application and automation identities | Auth method: federated credential preferred |
| Azure Key Vault | Secrets, keys, and certificate management | RBAC model, soft delete, purge protection, diagnostics |
| Conditional Access | Policy enforcement for human sign-ins | MFA, device compliance, location, risk |
| PIM | Just-in-time privileged access | Eligible assignments, activation requirements |
| Entra ID Audit Logs | Identity operation audit trail | Archive to Log Analytics workspace |
| Entra ID Sign-in Logs | Authentication event log | Archive to Log Analytics workspace |
| Microsoft Defender for Cloud | Detects over-privileged identities and IAM risks | IAM recommendations in Secure Score |

---

## Common Failure Modes

**Service principal with a non-expiring client secret in application configuration.** This is the most common IAM failure mode in Azure. The secret never rotates, leaks into a repository, and the scope is broader than required because "it was easier to give Contributor." Design pattern: managed identity for Azure-hosted workloads, federated credentials for pipelines.

**Owner role assigned permanently to individuals.** Standing Owner access at subscription scope means any compromise of that account gives full control of the environment. PIM or time-limited access is the control. If PIM is not available, Owner assignments must be documented, reviewed quarterly, and removed when not needed.

**Key Vault using vault access policies on new deployments.** The RBAC model is now the recommended approach. Vault access policies are a flat list with no inheritance, no conditional access, and no standard audit tooling. A Key Vault using the legacy model is a governance gap.

**Managed identity not scoped tightly enough.** The team assigns Contributor to a managed identity on the subscription because it is easier than finding the correct built-in role. This means every function execution runs with subscription-level Contributor — the blast radius of a compromise or misconfiguration is the entire subscription.

**No diagnostic settings on Key Vault.** Secrets are being read, but there is no audit trail. You cannot answer "who read the database password last Tuesday" without Key Vault diagnostic logs.

**Break-glass accounts not documented or tested.** Discovered when the Global Administrator's MFA device is lost and access to the tenant is blocked.

**Group membership not reviewed.** A team member who left 6 months ago is still a member of the Azure-Contributor-Subscription-01 group. Their access continues until the group is reviewed. Access reviews (Entra ID Governance) are the control.

**Conditional Access in report-only mode.** The team turned on Conditional Access policies in report-only mode during testing and never promoted them to enforced. All sign-ins are logged but none are blocked. The policy is not in effect.

---

## Review Questions

1. A development team's application needs to read secrets from Azure Key Vault. The developer proposes using a service principal with a client secret stored in the application's environment variables. What is your response, and what do you recommend instead?

2. You are reviewing an Azure subscription. Every developer on the team has permanent Contributor access at subscription scope. What is wrong with this, and what access model do you implement?

3. What is the difference between a system-assigned and user-assigned managed identity? Give a scenario where you would choose each.

4. A Key Vault has 15 vault access policy entries and 3 unused identities (accounts that no longer exist). What are the governance implications and what remediation do you perform?

5. Your client's Azure tenant has no break-glass accounts documented. They say they have never needed them. What do you tell them and what do you require before you sign off on the design?

6. Conditional Access policies are set to report-only mode. The security team says they are still reviewing the impact. This has been the status for 4 months. What is your assessment?

---

## Lab Task

Complete [Lab 02 — IAM, RBAC, and Key Vault](../labs/lab-02-iam-rbac-keyvault.md).

The lab establishes a documented identity baseline for a single Azure workload, including group-based RBAC, a managed identity for a function app, a Key Vault with RBAC model and diagnostic settings, and a break-glass documentation record.

---

## Artefact Output

This module produces five artefacts:

1. [iam-rbac-matrix.md](../artefacts/iam-rbac-matrix.md) — identity and access assignment register
2. [managed-identity-pattern.md](../artefacts/managed-identity-pattern.md) — managed identity design decision record
3. [service-principal-governance-standard.md](../artefacts/service-principal-governance-standard.md) — service principal lifecycle and governance standard
4. [key-vault-access-pattern.md](../artefacts/key-vault-access-pattern.md) — Key Vault access design pattern
5. [break-glass-access-procedure.md](../artefacts/break-glass-access-procedure.md) — emergency access procedure

---

## Notes

- Entra ID P1 is required for Conditional Access and group-based application assignment. Entra ID P2 is required for PIM, Identity Protection, and access reviews. Many organisations have P1 via Microsoft 365 Business Premium or E3.
- For a personal lab subscription, you may be the Global Administrator of your personal tenant. This is fine for learning purposes but means you are validating design patterns, not production controls.
- Key Vault RBAC requires enabling the "Azure role-based access control" permission model on the vault. This is a one-time, non-reversible change per vault — vault access policies cannot be re-enabled after switching to RBAC.
- Managed identity RBAC assignments propagate within a few minutes but can take up to 10 minutes in some regions. If your app is throwing a 403 immediately after assigning the role, wait and retry.
- The Entra ID Audit Log and Sign-in Log are retained for 7 days on free tier, 30 days on P1/P2. Archive to Log Analytics for longer retention. Compliance requirements typically mandate 90 days hot and 1–2 years cold.

---

## Hints

- To find the managed identity's object ID for RBAC assignment: navigate to the resource → Identity → copy the Object ID. Use this ID when assigning roles via CLI: `az role assignment create --assignee <object-id>`.
- When switching a Key Vault to RBAC model: `az keyvault update --name <kv-name> --enable-rbac-authorization true`. All existing vault access policies become inactive but are not deleted.
- If you cannot see the Conditional Access blade, you do not have a P1 or P2 licence in your tenant. For the lab, document what you would configure rather than skipping the section.
- Break-glass accounts should use cloud-only identities (not federated through AD), have the Global Administrator directory role, and have all Conditional Access policies explicitly excluded. Record the account UPN in the break-glass procedure but not the password.

---

## Completion Criteria

**Foundation:** Answer all six review questions in writing. Name all ten services in the Azure service map and state the governance purpose of each.

**Practitioner:** Foundation criteria met. Lab 02 complete with all evidence captured. All five artefacts produced with content reflecting your actual lab configuration and reasoning, not generic text.

**Senior Architect:** Practitioner criteria met. Senior SA review answered at rubric standard. Artefacts annotated with governance notes, design decisions, risks accepted, and handover notes for operations.

---

## Senior SA Review

1. You join an engagement where the Azure environment has been running for 18 months. You discover that three service principals have client secrets expiring in 7 days, no alert is configured, and the team does not know which applications use them. How do you triage this and what do you put in place to prevent recurrence?

2. A client is moving a workload from on-premises to Azure and wants to replicate their existing Active Directory group structure directly into Entra ID for RBAC. What are the risks of this approach and what alternative model do you propose for an Azure-native workload?

3. Your engineering team has implemented Key Vault with the RBAC model, soft delete enabled, and diagnostic settings configured. The Secure Score shows Key Vault as compliant. An internal security auditor asks what would happen if a developer deleted a secret by mistake. Walk through the recovery scenario and identify any gaps.

4. Design the break-glass access model for an Azure tenant with 5 subscriptions, 30 engineers, and Entra ID P2 licensing. What accounts are created, how are they governed, and what monitoring tells you when they are used?
