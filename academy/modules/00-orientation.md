# Module 00 — Orientation

## Purpose

Set the operating model for the Azure Architect Re-Entry Lab so learners can engage with every subsequent module efficiently and at the right depth.

---

## Architect Briefing

You are not starting from zero. You have architecture experience, Azure familiarity, and enough conceptual foundation to engage with these modules as a peer — not as a student.

What has changed since your last active Azure engagement:

- Azure Active Directory is now **Microsoft Entra ID**. The underlying concepts are the same. The naming, portal experience, and service boundaries have shifted.
- The Azure portal has reorganised significantly. Finding things is faster once you know the new layouts; the first visit back can be disorienting.
- **Azure Policy** and **Microsoft Defender for Cloud** have matured significantly as platform governance tools. They are now the default controls for policy enforcement and posture management — not optional add-ons.
- **Bicep** has replaced ARM templates as the preferred IaC language. Azure CLI and PowerShell Az module syntax has evolved.
- The **Cloud Adoption Framework (CAF)** and **Azure Landing Zones** are now the reference architecture for enterprise Azure. If your last engagement predates landing zones, expect a shift in how subscription design and governance are approached.
- **Managed identities** are now the standard for service-to-service authentication. If your last design used connection strings or stored credentials, the baseline has moved.

This course reconnects you with the engineering surface at the level needed to lead, review, govern, and translate designs. It does not cover certification exam content — that is in the repo root.

---

## Engineering View

This module has no Azure services to configure. It establishes the operating model.

### Three Tracks

You self-select your track per module. You are not locked in. Switch tracks as your confidence returns.

**Foundation:** Read the module. Answer the review questions. Enough to participate in design reviews and ask informed questions.

**Practitioner:** Complete the lab. Produce the artefact. Enough to supervise engineering work and contribute to governance.

**Senior Architect:** Complete the senior SA review. Annotate the artefact. Enough to lead design authority, govern delivery, and produce engineer-ready specifications.

### How Modules Work

Each module follows this structure:

1. **Purpose** — why this module exists and what it achieves
2. **Architect briefing** — what an experienced architect needs to reconnect with, without being talked down to
3. **Engineering view** — how the services actually work, what the controls are, and what the trade-offs look like
4. **Azure service map** — the key services, their relationships, and their governance controls
5. **Common failure modes** — what goes wrong, why, and what an architect should have caught before it did
6. **Review questions** — questions that require reasoning, not just recall
7. **Lab task** — a pointer to the associated lab
8. **Artefact output** — what the lab produces and why it matters
9. **Notes** — additional context, caveats, and things worth knowing
10. **Hints** — for learners who are stuck or want to sanity-check their approach
11. **Completion criteria** — unambiguous pass/fail per track
12. **Senior SA review** — principal-architect-level challenge questions

### How Labs Work

Labs are structured practice tasks using the Azure portal and CLI. They produce artefacts — real governance documents that could be used in an actual engagement with minimal modification.

Labs do not walk you through every click. They give you a goal, a scenario, the steps at the right level of detail for an architect, and the evidence you need to capture.

Labs are designed to be done in a personal Azure subscription with no production risk. Cost and clean-up guidance is included in every lab.

### How Artefacts Work

Artefacts are the tangible output of the course. They are:

- **Templates** — structured enough to be directly useful
- **Governance documents** — the kind a design authority or solutions architect would produce for an engagement
- **Evidence of competence** — completing an artefact demonstrates you understand the design, not just the configuration

Artefacts are stored in `academy/artefacts/`. Each artefact has an associated module and lab that explains the context.

### How Senior SA Review Works

The Senior SA review at the end of each module contains questions that test governance depth, not technical recall. A good senior SA review answer:

- Goes beyond what the module text states
- Names a specific risk or failure mode
- Is specific enough for an engineer to act on
- Would pass peer review by a principal architect

Review your answers against the [senior-sa-review-rubric.md](../senior-sa-review-rubric.md) before signing off.

---

## Azure Service Map

No Azure services are configured in this module. The service map for this module is the course itself:

| Course Component | Azure Analogue |
|---|---|
| Modules | Azure service documentation |
| Labs | Azure portal + CLI practice |
| Artefacts | Architecture decision records and governance documents |
| Senior SA review | Design authority challenge and governance gate |
| Completion tracks | Role-based access — you access what you need at your level |

---

## Common Failure Modes

**Skipping the orientation and jumping to technical modules.** The orientation establishes the mental model that makes subsequent modules coherent. Learners who skip it often complete modules without understanding why the structure exists.

**Treating the Foundation track as sufficient for leadership roles.** The Foundation track builds recognition. Leading a team, reviewing engineering work, and governing delivery requires Practitioner or Senior Architect depth.

**Completing labs without producing artefacts.** The lab steps are the exercise. The artefact is the output. Without the artefact, you have practised but not produced. Artefacts are the signal that the learning has translated into usable work product.

**Expecting the course to replicate Microsoft Learn.** It does not. Microsoft Learn covers configuration. This course covers architecture depth, design decisions, failure modes, and governance. If you want step-by-step configuration tutorials, use Microsoft Learn in parallel. This course assumes you can find your way to a resource in the Azure portal.

---

## Review Questions

1. What is the difference between the Foundation and Senior Architect tracks, and which is appropriate for someone returning to lead an Azure delivery?

2. Why does this course produce artefacts rather than just ask review questions?

3. Name two Azure services or capabilities that have changed significantly since 2021 and explain how the change affects architecture design decisions.

4. What is the relationship between the Velocity Academy platform and this repository?

---

## Lab Task

Module 00 has no lab. The first lab is [Lab 01 — Platform Control Baseline](../labs/lab-01-platform-control-baseline.md).

---

## Artefact Output

Module 00 has no artefact. Review the artefact index in [README.md](../README.md) to understand what the course will produce.

---

## Notes

- The course assumes you have access to a personal Azure subscription for lab work. A free-tier or pay-as-you-go subscription is sufficient.
- All labs include cost and clean-up guidance. No lab should cost more than a few dollars if clean-up is done promptly.
- This repo is the Markdown source. The Velocity Academy site is the rendered learning experience. Authoring always happens here.
- The repo root contains AZ-305 and AZ-104 certification study content. That content is separate from the academy course and serves a different purpose.

---

## Hints

- If you are unsure which track to use, start at Foundation for the first two modules. Switch to Practitioner once you have reoriented.
- If you have been away from Azure for more than 12 months, expect to spend more time in the Engineering View sections. The service names and portal layouts will look different.
- The artefact templates are designed to be sparse by default so they are easy to extend. Do not treat a sparse template as incomplete — add what your context requires.

---

## Completion Criteria

**Foundation:** Read this module. Answer all four review questions in writing. Understand the track model and course structure.

**Practitioner:** Foundation criteria met. No additional lab or artefact required for this module.

**Senior Architect:** Practitioner criteria met. Review the senior-sa-review-rubric.md and confirm you understand the scoring model before proceeding to Module 01.

---

## Senior SA Review

1. A junior architect says they want to do the Foundation track for all modules because they learn better by reading than by doing. What is your response?

2. The course produces artefacts rather than just marking review questions. From a design authority perspective, why does this matter more than quiz scores?

3. You are re-entering Azure delivery after two years in a non-technical management role. What is the minimum track level you would set for yourself before leading an architecture review for a client? Why?
