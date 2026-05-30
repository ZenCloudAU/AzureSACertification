# Product Map — Azure Architect Re-Entry Lab

## Course Arc

```
Phase 1: Platform Foundation
  Module 00 — Orientation
  Module 01 — Azure Platform Control Baseline
    Lab 01 — Platform Control Baseline
    Artefacts: platform-control-checklist, naming-and-tagging-standard, budget-and-cost-guardrail

Phase 2: Identity and Access
  Module 02 — IAM and Entra Architecture
    Lab 02 — IAM, RBAC, and Key Vault
    Artefacts: iam-rbac-matrix, managed-identity-pattern, service-principal-governance-standard,
               key-vault-access-pattern, break-glass-access-procedure

Phase 3: Network (Planned)
  Module 03 — Azure Networking
    Lab 03
    Artefacts: network-topology-decision, hub-spoke-design-record, nsg-rule-standard

Phase 4: Storage and Data (Planned)
  Module 04 — Storage, Backup, Retention, and Data Tiering
    Lab 04
    Artefacts: storage-design-record, backup-retention-matrix, data-classification-register

Phase 5: Operations (Planned)
  Module 05 — Monitoring and Operations
    Lab 05
    Artefacts: monitoring-baseline-specification, alert-rule-register, runbook-template

Phase 6: Security (Planned)
  Module 06 — Security-by-Design
    Lab 06
    Artefacts: threat-model-summary, security-control-matrix, defender-posture-baseline

Phase 7: Integration (Planned)
  Module 07 — Integration Patterns
    Lab 07
    Artefacts: integration-pattern-decision, event-flow-diagram

Phase 8: IaC and DevOps (Planned)
  Module 08 — IaC and DevOps Recognition
    Lab 08
    Artefacts: iac-review-checklist, pipeline-design-notes

Phase 9: Translation (Planned)
  Module 09 — Architecture-to-Engineering Translation
    Lab 09
    Artefacts: engineer-brief-template, design-decision-log

Phase 10: Handover (Planned)
  Module 10 — Operational Handover
    Lab 10
    Artefacts: handover-pack-template, runbook-acceptance-checklist
```

---

## Module-to-Artefact Map

| Module | Artefact Produced |
|---|---|
| 01 Platform Control Baseline | azure-platform-control-checklist |
| 01 Platform Control Baseline | naming-and-tagging-standard |
| 01 Platform Control Baseline | budget-and-cost-guardrail |
| 02 IAM and Entra Architecture | iam-rbac-matrix |
| 02 IAM and Entra Architecture | managed-identity-pattern |
| 02 IAM and Entra Architecture | service-principal-governance-standard |
| 02 IAM and Entra Architecture | key-vault-access-pattern |
| 02 IAM and Entra Architecture | break-glass-access-procedure |

---

## Track-to-Outcome Map

| Track | Module Outcome | Lab Outcome | Artefact Outcome |
|---|---|---|---|
| Foundation | Pass all review questions | Not required | Not required |
| Practitioner | Pass all review questions | Complete lab steps and capture evidence | Produce artefact in own context |
| Senior Architect | Pass review questions + senior SA review | Complete lab + identify risks | Produce artefact + annotate with governance notes |

---

## Governance File Map

| File | Purpose | Audience |
|---|---|---|
| course-charter.md | Defines the product | PM, Product Owner |
| product-map.md | Maps all content relationships | PM, Lead Designer |
| completion-tracks.md | Defines track criteria | PM, UX Lead |
| role-signoff-model.md | Defines review and approval responsibilities | PM, QA Lead |
| release-plan.md | Phase milestones and release gates | PM |
| senior-sa-review-rubric.md | Scoring guide for senior review | Senior SA, QA Lead |

---

## Testing File Map

| File | When Used |
|---|---|
| qa-checklist.md | Before each release |
| ux-test-checklist.md | Before each release |
| azure-technical-validation.md | Before each release, after any Azure content change |
| release-acceptance.md | At release — sign-off record |
