# IAM RBAC Matrix

**Purpose:** Record every identity, role assignment, scope, and control for this Azure environment. This is the source of truth for access governance — who or what can do what, at which scope, and who is accountable.

**Artefact owner:** [Name / role]
**Environment:** [Subscription name / ID]
**Date created:** [YYYY-MM-DD]
**Date last reviewed:** [YYYY-MM-DD]
**Review frequency:** Quarterly minimum, or after any team change

---

## Human Access — Group-Based RBAC

| Group Name | Group Object ID | Role | Scope | Justification | Review Date |
|---|---|---|---|---|---|
| `grp-labenv-dev-contributors` | [Object ID] | Contributor | `rg-labenv-dev-aue-001` | Engineering team — deploy and manage resources | [YYYY-MM-DD] |
| `grp-labenv-dev-readers` | [Object ID] | Reader | `rg-labenv-dev-aue-001` | Monitoring and read-only access | [YYYY-MM-DD] |
| `grp-labenv-dev-contributors` | [Object ID] | Key Vault Secrets User | `kv-labenv-dev-aue-001` | Read secrets for application access pattern | [YYYY-MM-DD] |
| `grp-labenv-dev-readers` | [Object ID] | Key Vault Reader | `kv-labenv-dev-aue-001` | View vault metadata, not secrets | [YYYY-MM-DD] |

---

## Human Access — Individual Assignments

> Principle: Assign roles to groups, not individuals. Individual assignments are exceptions and require documented justification. List all exceptions here.

| User Principal Name | Object ID | Role | Scope | Justification | Expiry | Review Date |
|---|---|---|---|---|---|---|
| [your@email.com] | [Object ID] | Key Vault Administrator | `kv-labenv-dev-aue-001` | Lab admin — temporary during lab setup | [date] | [YYYY-MM-DD] |

---

## Service Access — Managed Identities

| Resource Name | Identity Type | Object ID | Role | Scope | Justification |
|---|---|---|---|---|---|
| `func-labenv-dev-aue-001` | System-assigned | [Object ID from Identity blade] | Key Vault Secrets User | `kv-labenv-dev-aue-001` | Function App reads secrets for application operation — no credential stored |

---

## Service Access — Service Principals

> No service principals are created in this lab. This section is reserved for future CI/CD pipeline identities. When created, record all service principals here.

| App Registration Name | App ID (Client ID) | Auth Method | Role | Scope | Owner | Secret Expiry | Review Date |
|---|---|---|---|---|---|---|---|
| [Future: GitHub Actions SP for lab deployment] | [App ID] | Federated credential (OIDC) | Contributor | `rg-labenv-dev-aue-001` | [Name] | N/A (federated) | [YYYY-MM-DD] |

---

## Subscription-Level Access

| Principal | Type | Role | Scope | Justification |
|---|---|---|---|---|
| [your@email.com] | User | Owner | Subscription | Personal subscription — owner by default |
| Break-glass account 1 | User | Owner | Subscription | Emergency access — see break-glass-access-procedure.md |
| Break-glass account 2 | User | Owner | Subscription | Emergency access — see break-glass-access-procedure.md |

---

## Access Control Design Decisions

| Decision | Choice | Reason |
|---|---|---|
| Human access model | Group-based RBAC | Individuals move teams — group membership is the control |
| Service access model | Managed identity preferred | No credential to rotate, leak, or forget |
| Key Vault permission model | Azure RBAC (not vault access policies) | Consistent, auditable, supports Conditional Access |
| Subscription-level individual assignments | Owner for account only | Personal subscription — no team context |
| Standing privileged access | Owner on personal subscription only — no permanent Owner on team subscriptions | PIM not available in this lab; document risk |

---

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation | Status |
|---|---|---|---|---|
| No PIM — standing Contributor for team | Medium | High | Quarterly access review, least-privilege scope (resource group not subscription) | Accepted — document for production |
| Managed identity over-scoped | Low | High | Verified Key Vault Secrets User (not Contributor) assigned | Mitigated |
| No access review tooling (no Entra ID Governance licence) | High | Medium | Manual quarterly review — calendar reminder set | Accepted |
| Individual Key Vault Administrator assignment | Low | Medium | Temporary — remove after lab setup complete | In progress |

---

## Access Review Schedule

| Review | Frequency | Last Completed | Next Due | Reviewer |
|---|---|---|---|---|
| Group membership review — contributors | Quarterly | [date] | [date] | [Name] |
| Group membership review — readers | Quarterly | [date] | [date] | [Name] |
| Service principal review | Quarterly | [date] | [date] | [Name] |
| Managed identity role audit | Quarterly | [date] | [date] | [Name] |
| Subscription-level Owner review | Semi-annually | [date] | [date] | [Name] |

---

## Governance Notes

**Analyst note:** This matrix must be updated within 24 hours of any access change. Access changes that are not recorded here are ungoverned, regardless of what exists in the Azure portal. The portal is the system of record for what is deployed; this document is the governance record of why.

**Senior SA annotation [complete for Senior Architect track]:**

- Design decision rationale:
- Risks accepted and why:
- Handover note for operations:
- What would change for a production engagement:
