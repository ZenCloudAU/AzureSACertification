# Release Plan — Azure Architect Re-Entry Lab

## Phase 1 — Platform Foundation

**Goal:** Establish course structure and platform control baseline content.

**Scope:**

- Course governance files: README, course-charter, product-map, completion-tracks, role-signoff-model, release-plan, senior-sa-review-rubric
- Module 00: Orientation
- Module 01: Azure Platform Control Baseline
- Lab 01: Platform Control Baseline
- Artefacts: azure-platform-control-checklist, naming-and-tagging-standard, budget-and-cost-guardrail
- Testing: qa-checklist, ux-test-checklist, azure-technical-validation, release-acceptance

**Success criteria:**

- All governance files complete and internally consistent
- Module 00 and 01 complete with all required sections
- Lab 01 complete with all required sections
- Three Phase 1 artefacts complete and usable as real templates
- All four testing files complete and executable
- QA, UX, and Senior SA sign-off achieved

**Status:** In progress

---

## Phase 2 — Identity and Access

**Goal:** Complete IAM and Entra Architecture content stream.

**Scope:**

- Module 02: IAM and Entra Architecture
- Lab 02: IAM, RBAC, and Key Vault
- Artefacts: iam-rbac-matrix, managed-identity-pattern, service-principal-governance-standard, key-vault-access-pattern, break-glass-access-procedure

**Success criteria:**

- Module 02 complete with all required sections including senior SA review
- Lab 02 complete with all required sections
- All five Phase 2 artefacts complete and usable as real templates
- QA, UX, and Senior SA sign-off achieved

**Status:** In progress

---

## Phase 3 — Networking (Planned)

**Scope:**

- Module 03: Azure Networking
- Lab 03: Hub-Spoke Network Design
- Artefacts: network-topology-decision, hub-spoke-design-record, nsg-rule-standard

**Dependencies:** Phase 1 and 2 complete and signed off.

---

## Phase 4 — Storage and Data (Planned)

**Scope:**

- Module 04: Storage, Backup, Retention, and Data Tiering
- Lab 04: Storage Design and Backup Configuration
- Artefacts: storage-design-record, backup-retention-matrix, data-classification-register

---

## Phase 5 — Operations (Planned)

**Scope:**

- Module 05: Monitoring and Operations
- Lab 05: Monitoring Baseline
- Artefacts: monitoring-baseline-specification, alert-rule-register, runbook-template

---

## Phase 6 — Security (Planned)

**Scope:**

- Module 06: Security-by-Design
- Lab 06: Defender for Cloud and Security Posture
- Artefacts: threat-model-summary, security-control-matrix, defender-posture-baseline

---

## Phase 7 — Integration (Planned)

**Scope:**

- Module 07: Integration Patterns
- Lab 07: Integration Pattern Design
- Artefacts: integration-pattern-decision, event-flow-diagram

---

## Phase 8 — IaC and DevOps (Planned)

**Scope:**

- Module 08: IaC and DevOps Recognition
- Lab 08: Bicep and Pipeline Review
- Artefacts: iac-review-checklist, pipeline-design-notes

---

## Phase 9 — Architecture Translation (Planned)

**Scope:**

- Module 09: Architecture-to-Engineering Translation
- Lab 09: Engineer Brief Production
- Artefacts: engineer-brief-template, design-decision-log

---

## Phase 10 — Operational Handover (Planned)

**Scope:**

- Module 10: Operational Handover
- Lab 10: Handover Pack Production
- Artefacts: handover-pack-template, runbook-acceptance-checklist

---

## Release Cadence

Each phase is released when:

1. All content files in scope are complete
2. All testing checklists pass
3. All role sign-offs are recorded in release-acceptance.md

Phases are released sequentially. A phase does not release until the prior phase is signed off.

---

## Known Gaps (Tracked at Release)

Gaps identified during authoring that are deferred to a later phase are recorded in the release-acceptance.md sign-off record for the relevant phase. Gaps are not silently omitted — they are documented with the reason for deferral.
