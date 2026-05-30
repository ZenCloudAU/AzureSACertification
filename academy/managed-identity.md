# Managed Identity

Purpose: use Azure-managed identities where possible instead of storing secrets in code or configuration.

Use for:
- Azure Functions
- Logic Apps
- App Services
- Automation jobs
- Other Azure-hosted workloads

Review questions:
- What resource is using the identity?
- What target service does it access?
- Is the access scoped tightly?
- Is the identity documented for handover?
