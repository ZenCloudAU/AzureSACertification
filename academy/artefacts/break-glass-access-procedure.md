# Break-Glass Access Procedure

**Purpose:** Document the emergency access procedure for this Azure environment. Break-glass accounts are used only when all normal authentication paths have failed — MFA unavailable, primary admin account compromised, or federated identity provider unreachable.

**Artefact owner:** [Name / role]
**Environment:** [Subscription name / ID — Tenant ID]
**Date created:** [YYYY-MM-DD]
**Last tested:** [YYYY-MM-DD]
**Next test due:** [YYYY-MM-DD — quarterly]

---

## What Break-Glass Accounts Are

Break-glass accounts are emergency access accounts that exist outside of the normal identity lifecycle:

- Not federated through any external identity provider
- Cloud-only accounts in the Entra ID tenant
- Excluded from all Conditional Access policies
- Not linked to any individual employee
- Permanent Global Administrator directory role
- Password stored physically offline — not in a password manager, not digitally

They exist for one reason: to restore administrative access to the Azure tenant when all other methods have failed.

---

## Break-Glass Account Register

> Record account UPNs here but not passwords. Passwords are physically secured offline.

| Account | UPN | Type | Role | Excluded from CA | Password Location | Last Used | Last Tested |
|---|---|---|---|---|---|---|---|
| Break-glass 1 | `break-glass-01@[tenant-domain].onmicrosoft.com` | Cloud-only | Global Administrator | Yes | [Physical location description] | Never | [Date] |
| Break-glass 2 | `break-glass-02@[tenant-domain].onmicrosoft.com` | Cloud-only | Global Administrator | Yes | [Physical location description] | Never | [Date] |

**Note for personal/lab environments:** A personal Azure subscription with a single administrator does not strictly require break-glass accounts — the Microsoft Account fallback provides emergency access. However, documenting this pattern is required for this module as it reflects production practice.

---

## When to Use Break-Glass Access

Break-glass access is appropriate **only** when:

1. MFA is unavailable for all administrators (e.g., device lost, authenticator app unavailable, network blocking)
2. The primary Global Administrator account is compromised or locked out
3. The federated identity provider (e.g., on-premises AD, OKTA) is unreachable and blocking all admin access
4. A Conditional Access misconfiguration has locked out all users including administrators

**Do not use break-glass accounts for:**

- Routine administration — even if "quicker" to use
- Testing configurations that require Global Administrator
- Any task that can wait until normal access is restored
- Circumventing approval processes

**Every use of break-glass accounts is an incident.** Treat it as such.

---

## Break-Glass Account Configuration

The following configuration must be verified at each quarterly test:

| Configuration Item | Requirement | Verified |
|---|---|---|
| Account is cloud-only (not synced from on-premises AD) | Yes | |
| Account has Global Administrator directory role | Yes | |
| Account is excluded from all Conditional Access policies | Yes — explicitly excluded by account, not by group | |
| MFA is NOT enforced on the account (by design) | Yes — break-glass cannot depend on MFA | |
| Account can sign in to Entra ID portal | Confirmed at quarterly test | |
| Account has no active sessions | No persistent sessions | |
| RBAC: Owner role on all subscriptions | Yes | |

---

## Monitoring — Alert on Use

Every use of a break-glass account must trigger an immediate alert. This alert is a high-priority security event — the account should only ever be used in a genuine emergency.

**Alert configuration:**

```
Log Analytics workspace query (scheduled every 5 minutes):
SigninLogs
| where UserPrincipalName in ("break-glass-01@[domain]", "break-glass-02@[domain]")
| where TimeGenerated > ago(6m)

Action: Alert → Action Group → SMS and email to all administrators
Severity: Critical
```

**Portal alert setup:** Monitor → Alerts → Create → Log Analytics query → Action Group with SMS and email

Without this alert, break-glass use goes undetected. A compromised break-glass account could operate for months in a tenant without monitoring.

---

## Break-Glass Use Procedure

### During the Emergency

1. Retrieve the break-glass account password from physical storage
2. Sign in at `portal.azure.com` or `entra.microsoft.com` using the break-glass account
3. Perform only the minimum actions required to restore normal access
4. Log every action taken with timestamps

### Immediately After the Emergency

1. Identify and resolve the root cause of the emergency
2. Restore normal administrator access
3. Sign out of all break-glass account sessions
4. Return the physical password record to its secure location
5. Initiate an incident review

### Within 24 Hours

1. File an incident report covering: what happened, why break-glass was needed, what actions were taken, and what change prevents recurrence
2. Change the break-glass account password and update the physical record
3. Confirm the alert fired — if it did not, investigate the alert configuration
4. Review the Entra ID Audit Log for all actions taken by the break-glass account during the incident

---

## Quarterly Test Procedure

Break-glass accounts must be tested quarterly to confirm they work when needed. A break-glass account that cannot sign in is worthless.

1. Retrieve the password from physical storage
2. Attempt sign-in at `entra.microsoft.com` from a browser with no saved sessions
3. Confirm successful sign-in and access to the tenant
4. Confirm the Entra ID audit log captured the sign-in
5. Confirm the alert fired (the monitoring alert should trigger on the test sign-in — this is expected and should be suppressed during the test window with advance notice to the team)
6. Sign out
7. Return password to physical storage
8. Update the `Last Tested` date in this document
9. If sign-in fails: escalate immediately — treat as an incident

---

## Compliance with CAF and Microsoft Recommendations

This procedure aligns to Microsoft's guidance on emergency access accounts:

- Two accounts minimum (one account failure cannot leave the tenant unmanaged)
- Cloud-only accounts (independent of on-premises AD availability)
- Excluded from Conditional Access
- Monitored via sign-in logs
- Tested quarterly
- Password stored physically offline

Reference: [Microsoft Entra emergency access accounts](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/security-emergency-access)

---

## Governance Notes

**Design decision:** Two cloud-only accounts with Global Administrator role, excluded from all CA policies. This follows Microsoft guidance exactly. No deviation.

**Risk accepted:** A break-glass account with Global Administrator and no MFA is a significant privilege. The mitigation is: physical password storage, monitoring alert on every sign-in, quarterly test and password rotation, and the cultural rule that these accounts are never used for routine work.

**Handover note for operations:** The existence and location of break-glass password records must be communicated to senior leadership, not just technical staff. If the only person who knows where the password is stored is unavailable during an emergency, the break-glass accounts are useless.

**Senior SA annotation [complete for Senior Architect track]:**

- A client says they have never needed break-glass accounts in 5 years and considers the configuration overhead unnecessary. How do you respond, and what incident scenario do you walk them through?
- Design the Conditional Access exclusion policy for break-glass accounts. What are the risks of a broad exclusion vs. a named-account exclusion?
- What additional monitoring would you add beyond the sign-in log alert to detect unauthorised use of break-glass credentials?
