# Azure Architect Re-Entry Lab

**Product promise:** Rebuild Azure engineering fluency at architecture depth.

This is not beginner Azure training. It is not a Microsoft Learn clone. It is a structured re-entry programme for experienced architects who understand design principles and need to reconnect with the current Azure engineering surface — including how services behave, how to review engineering work, and how to produce engineer-ready artefacts.

---

## Audience

Solution architects, cloud architects, design authorities, senior consultants, and technical leads who:

- Hold or have held Azure solutions architect or administrator certifications
- Understand architecture principles and governance frameworks
- Have moved away from daily Azure engineering
- Need to rebuild enough fluency to lead, review, challenge, and translate designs into engineer-ready work

---

## How This Course Works

The course uses three tracks. Learners choose their entry point per module and advance across tracks as fluency returns.

| Track | What It Produces |
|---|---|
| Foundation | Recognition and review-question answers |
| Practitioner | Completed lab, produced artefact |
| Senior Architect | Senior review commentary and handover-ready governance artefact |

Each module follows a fixed structure. Each lab produces a real artefact. The senior SA review section tests whether the learner can govern and challenge — not just execute.

---

## Course Structure

### Governance

| File | Purpose |
|---|---|
| [course-charter.md](course-charter.md) | Product promise, audience, editorial standards, scope |
| [product-map.md](product-map.md) | Module and artefact map |
| [completion-tracks.md](completion-tracks.md) | Track definitions and completion criteria |
| [role-signoff-model.md](role-signoff-model.md) | Who reviews and approves what |
| [release-plan.md](release-plan.md) | Phase milestones and release readiness |
| [senior-sa-review-rubric.md](senior-sa-review-rubric.md) | Senior SA scoring rubric |

### Modules

| Module | Title | Status |
|---|---|---|
| [00](modules/00-orientation.md) | Orientation | Phase 1 |
| [01](modules/01-platform-control-baseline.md) | Azure Platform Control Baseline | Phase 1 |
| [02](modules/02-iam-entra-architecture.md) | IAM and Entra Architecture | Phase 2 |
| 03 | Azure Networking | Planned |
| 04 | Storage, Backup, Retention, and Data Tiering | Planned |
| 05 | Monitoring and Operations | Planned |
| 06 | Security-by-Design | Planned |
| 07 | Integration Patterns | Planned |
| 08 | IaC and DevOps Recognition | Planned |
| 09 | Architecture-to-Engineering Translation | Planned |
| 10 | Operational Handover | Planned |

### Labs

| Lab | Title | Status |
|---|---|---|
| [01](labs/lab-01-platform-control-baseline.md) | Platform Control Baseline | Phase 1 |
| [02](labs/lab-02-iam-rbac-keyvault.md) | IAM, RBAC, and Key Vault | Phase 2 |

### Artefacts

| Artefact | Purpose |
|---|---|
| [azure-platform-control-checklist.md](artefacts/azure-platform-control-checklist.md) | Pre-deployment control checklist |
| [naming-and-tagging-standard.md](artefacts/naming-and-tagging-standard.md) | Resource naming and tag schema |
| [budget-and-cost-guardrail.md](artefacts/budget-and-cost-guardrail.md) | Budget alert and cost control template |
| [iam-rbac-matrix.md](artefacts/iam-rbac-matrix.md) | Identity and access assignment register |
| [managed-identity-pattern.md](artefacts/managed-identity-pattern.md) | Managed identity design decision record |
| [service-principal-governance-standard.md](artefacts/service-principal-governance-standard.md) | Service principal lifecycle standard |
| [key-vault-access-pattern.md](artefacts/key-vault-access-pattern.md) | Key Vault access design pattern |
| [break-glass-access-procedure.md](artefacts/break-glass-access-procedure.md) | Emergency access procedure |

### Testing

| File | Purpose |
|---|---|
| [qa-checklist.md](testing/qa-checklist.md) | QA gate checks per release |
| [ux-test-checklist.md](testing/ux-test-checklist.md) | Learner experience test criteria |
| [azure-technical-validation.md](testing/azure-technical-validation.md) | Azure accuracy review checklist |
| [release-acceptance.md](testing/release-acceptance.md) | Release sign-off record |

---

## Repository Role

This repository is the **Markdown source of truth** for the Azure Architect Re-Entry Lab course. The Velocity Academy platform at [velocity-academy](https://github.com/ZenCloudAU/velocity-academy) consumes this content. All authoring happens here.

---

*ZenCloud Global Consultants · AZ-305 + AZ-104 Certified · Brisbane, Australia*
