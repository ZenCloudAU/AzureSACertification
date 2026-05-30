# Azure Platform Control Checklist

**Purpose:** Verify that platform controls are in place before any workload resources are deployed to an Azure subscription.

**Usage:** Complete this checklist at environment setup. Revisit at each major change milestone. Do not proceed to workload deployment until all Required items are checked.

**Artefact owner:** [Name / role]
**Environment:** [Subscription name / ID]
**Date completed:** [YYYY-MM-DD]
**Reviewed by:** [Name / role]

---

## 1. Subscription Configuration

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| Subscription named according to standard | Required | | |
| Subscription owner identity confirmed | Required | | |
| Management group placement confirmed | Required | | |
| Directory (Entra tenant) confirmed | Required | | |
| Subscription purpose documented | Required | | |

---

## 2. Resource Group Standards

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| Resource groups named per naming standard | Required | | |
| All six mandatory tags applied | Required | | |
| Resource group lifecycle boundaries defined (which resources belong here) | Required | | |
| CanNotDelete lock on production resource groups | Required (prod) | | |
| Resource group owner identity confirmed | Required | | |

---

## 3. Naming and Tagging

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| Naming standard documented in artefact | Required | | |
| Tag schema documented in artefact | Required | | |
| Azure Policy for tag enforcement assigned | Required | | |
| Naming compliance checked (no ad-hoc names) | Required | | |
| Tag policy remediation run on existing non-compliant resources | Required | | |

---

## 4. Budget and Cost Controls

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| Monthly budget created with defined amount | Required | | |
| Budget alert at 80% configured with action group | Required | | |
| Budget alert at 100% configured with action group | Required | | |
| Action group email destination confirmed | Required | | |
| Subscription owner receiving cost alerts | Required | | |
| Cost analysis filter by tag confirmed working | Recommended | | |
| Azure Advisor cost recommendations reviewed | Recommended | | |

---

## 5. Identity and Access

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| RBAC assignments use groups, not individuals | Required | | |
| No Owner assignment at subscription scope (except break-glass) | Required | | |
| Break-glass access accounts documented | Required | | |
| RBAC assignment register (RBAC matrix) current | Required | | |
| Entra ID audit logs routing to Log Analytics | Required | | |

---

## 6. Policy and Governance

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| Azure Policy initiative assigned | Required | | |
| Policy compliance state reviewed | Required | | |
| Top non-compliant policies remediated or risk-accepted | Required | | |
| Allowed locations policy in place | Required | | |
| Tag inheritance policy assigned | Required | | |

---

## 7. Monitoring and Audit

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| Log Analytics workspace created | Required | | |
| Subscription Activity Log routing to workspace | Required | | |
| Key Vault diagnostic settings configured | Required | | |
| Entra ID sign-in logs routing to workspace | Recommended | | |
| Diagnostic settings policy deployed (DeployIfNotExists) | Recommended | | |
| Log retention period confirmed | Required | | |

---

## 8. Security Baseline

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| Defender for Cloud enabled (CSPM tier) | Required | | |
| Secure Score recorded | Required | | |
| Top security recommendations reviewed | Required | | |
| Security contact email configured in Defender for Cloud | Required | | |
| Regulatory compliance baseline selected | Recommended | | |

---

## 9. Clean-Up and Lifecycle

| Item | Required | Status | Evidence / Notes |
|---|---|---|---|
| Clean-up plan documented for lab / non-production resources | Required | | |
| Resource lifecycle owner confirmed | Required | | |
| Decommission date recorded (if applicable) | Required | | |

---

## Checklist Status

**Date:** [YYYY-MM-DD]
**Completed by:** [Name]
**Overall status:** [ ] All Required items complete — proceed to workload deployment / [ ] Gaps remain — see notes below

**Gaps and deferred items:**

| Item | Reason for deferral | Target date |
|---|---|---|
| | | |

---

## Sign-Off

| Role | Name | Date | Status |
|---|---|---|---|
| Engineer | | | |
| Solution Architect | | | |
| Senior SA (if Practitioner track or above) | | | |
