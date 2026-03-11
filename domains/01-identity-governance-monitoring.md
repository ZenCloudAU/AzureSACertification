# 🔐 Domain 1 — Design Identity, Governance, and Monitoring Solutions

**Exam weight: 25–30%**
**Reference:** https://learn.microsoft.com/en-us/training/paths/design-identity-governance-monitor-solutions/

---

## Microsoft Entra ID (Azure Active Directory)

Cloud-based identity and access management (IAM) service — the foundation of all Azure authentication and authorisation.

### Identity Types

| Type | Where identities live | Example |
|------|-----------------------|---------|
| Cloud identity | Azure AD only | Admin accounts created in the portal |
| Directory-synchronized | On-premises AD → Azure AD via AD Connect | Existing corporate employees |
| Guest users (B2B) | External directory | Partner organisation collaborators |

### Hybrid Identity Sync Options

| Method | How it works | Resilience | Use case |
|--------|-------------|------------|---------|
| **Password Hash Sync (PHS)** | Hash of on-prem password synced to Azure AD | Highest (works if on-prem down) | Recommended default |
| Pass-Through Auth (PTA) | Auth forwarded to on-prem AD via agent | Lower (on-prem dependency) | Must enforce on-prem password policies |
| Federation (AD FS) | Full token-based federation | Complex; on-prem dependency | Legacy claims requirements, SmartCard |

### Key Features

- **MFA:** Something you know + something you have + something you are
- **Conditional Access (P1):** Signals (user, device, location, risk) → policy decision → grant/block/MFA
- **PIM (P2):** Just-in-time privileged role activation — reduces standing admin access
- **Identity Protection (P2):** Risk-based Conditional Access, sign-in risk scores
- **Access Reviews (P2):** Periodic review of group memberships and role assignments
- **B2B:** Partner identities managed externally — guests invited into tenant
- **B2C:** Customer-facing — sign-in with Google, Facebook, Microsoft account, custom IdP
- **SSPR (P1):** Self-service password reset

### Licence Tiers (Key Exam Differentiators)

| Feature | Free | P1 | P2 |
|---------|------|----|----|
| MFA | ✅ (per-user only) | ✅ | ✅ |
| Conditional Access | ❌ | ✅ | ✅ |
| SSPR | ❌ | ✅ | ✅ |
| Group-based licensing | ❌ | ✅ | ✅ |
| PIM | ❌ | ❌ | ✅ |
| Identity Protection | ❌ | ❌ | ✅ |
| Access Reviews | ❌ | ❌ | ✅ |

---

## Azure RBAC

### Scope Hierarchy (broadest → narrowest, inheritance flows downward)

```
Management Group
    └── Subscription
            └── Resource Group
                    └── Resource (individual)
```

**Deny assignments override allow.** Use the narrowest scope possible (least privilege).

### Key Built-in Roles

| Role | Permissions | Use case |
|------|------------|---------|
| **Owner** | Full access + manage access | Full resource + delegation control |
| **Contributor** | Create and manage, no access management | Developers, operators |
| **Reader** | View only | Auditors, read-only access |
| **User Access Administrator** | Manage access only, no resource actions | Delegation without operational access |

### Custom Roles

Define `Actions`, `NotActions`, `DataActions`, `NotDataActions`, and `AssignableScopes` in JSON. Create at management group scope for enterprise-wide availability.

RBAC hard limit: **2,000 role assignments per subscription**.

---

## Management Groups

Organise subscriptions into a policy/RBAC governance hierarchy above subscriptions.

```
Tenant Root Group
    ├── Production MG → RBAC + Policy cascade to all children
    │   ├── Corp-Prod-East (subscription)
    │   └── Corp-Prod-West (subscription)
    ├── Non-Production MG
    │   └── Corp-Dev (subscription)
    └── Sandbox MG
        └── Corp-Sandbox (subscription)
```

- **Maximum depth: 6 levels** (not counting Root or subscription level)
- Policy and RBAC assigned at MG level cascade to all descendant subscriptions and resources
- A subscription can only belong to one management group at a time

---

## Azure Policy

### How It Works

1. **Definition:** JSON rule — "if [condition] then [effect]"
2. **Assignment:** Scope definition to MG / Subscription / RG
3. **Initiative:** Bundle of policy definitions (e.g., ISO 27001 compliance set)
4. **Compliance:** Dashboard shows compliant/non-compliant resources
5. **Remediation:** `DeployIfNotExists` and `Modify` effects trigger auto-remediation tasks

### Policy Effects (strongest → weakest)

| Effect | Behaviour |
|--------|-----------|
| **Deny** | Block resource creation/update that violates policy |
| Append | Add required fields/properties to request |
| Modify | Add/modify/remove tags or properties at create/update |
| DeployIfNotExists | Deploy related resource if condition not met |
| AuditIfNotExists | Audit (log) if related resource doesn't exist |
| Audit | Log non-compliance — don't block |
| Disabled | Skip policy evaluation |

**Exam tip:** `Deny` is the only effect that actively *prevents* a non-compliant resource being created.

---

## Azure Monitor

### Component Map

| Component | Data type | Retention | Purpose |
|-----------|-----------|-----------|---------|
| **Metrics** | Numerical time-series | **93 days** default | CPU%, bytes, latency, counts |
| **Log Analytics** | Structured log data (KQL) | Configurable (30–730 days) | Query across resources |
| **Application Insights** | APM traces, exceptions, dependencies | 90 days default | Web app performance monitoring |
| **Alerts** | Triggered rules | N/A | Notify or act on threshold breach |
| **Workbooks** | Interactive reports | N/A | Combine metrics + log data |
| **Dashboards** | Visualisation | N/A | Pin charts and query results |

### Data Sources

- **Platform logs:** Activity Log (subscription-level), Resource Diagnostic Logs (per-resource), Azure AD Logs
- **Application:** App Insights SDK, OpenTelemetry
- **Guest OS:** Azure Monitor Agent (AMA), Log Analytics Agent (legacy MMA)
- **Custom:** REST API, Direct Ingest API

### KQL Basics (Log Analytics)

```kql
// Errors in last 24 hours by type
AppExceptions
| where TimeGenerated > ago(24h)
| summarize count() by problemId
| order by count_ desc

// VMs with CPU > 80% (5-min average)
Perf
| where CounterName == "% Processor Time" and CounterValue > 80
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5m)
```

---

## Microsoft Defender for Cloud

Unified cloud security management across Azure, on-premises, and multi-cloud (AWS/GCP).

| Capability | Description |
|-----------|-------------|
| **Secure Score** | 0–100 posture metric — higher = more secure |
| Security recommendations | Actionable hardening items (with effort/impact rating) |
| Regulatory compliance | Map to CIS, NIST, ISO 27001, PCI-DSS, SOC |
| Defender plans (CSPM + CWPP) | Per-workload protection (VMs, SQL, Containers, Storage, AppService) |
| Threat alerts | Real-time detection and incident correlation |
| Attack path analysis | Visualise lateral movement risk |

---

## Key Numbers

| Fact | Value |
|------|-------|
| Management group max depth | **6 levels** (below tenant root) |
| RBAC assignments per subscription | **2,000** |
| RBAC assignments per MG | **500** |
| Monitor Metrics default retention | **93 days** |
| Log Analytics default retention | **30 days** (extend to 730 days) |
| App Insights default retention | **90 days** |
| Conditional Access requires | **Azure AD Premium P1** minimum |
| PIM requires | **Azure AD Premium P2** |
