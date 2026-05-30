# Course Charter — Azure Architect Re-Entry Lab

## Product Promise

Rebuild Azure engineering fluency at architecture depth — fast enough to lead, review, and govern cloud delivery again.

---

## Problem This Course Solves

Experienced architects who have moved away from daily Azure engineering face a specific gap: they understand design principles, governance frameworks, and Well-Architected concepts, but lose confidence in the engineering surface — how services actually behave, what controls exist, what engineers are doing, and what good looks like.

This course is not about learning Azure from scratch. It is about reconnecting at the level needed to:

- Lead architecture design sessions
- Review engineering work with authority
- Challenge designs and ask the right questions
- Produce artefacts that engineers can actually use
- Own the handover from design to build

---

## Audience

**Primary:** Solution architects, cloud architects, design authorities, senior consultants, and technical leads with:

- Prior Azure delivery experience (design, engineering, or governance)
- AZ-305 or AZ-104 certification (current or lapsed)
- Architecture seniority in consulting, enterprise, or managed services
- A gap of 6–24+ months from active Azure engineering

**Not the audience for:**

- Engineers who work in Azure daily
- Architects seeking a first Azure certification
- Learners who need Microsoft Learn-style step-by-step tutorials

---

## Learning Outcomes

On completion, the learner will be able to:

1. Explain the Azure platform control baseline and why it matters before any workload is deployed
2. Design and review IAM, RBAC, and Entra identity patterns at architecture depth
3. Design Azure networking for a standard enterprise workload
4. Specify storage, backup, and retention requirements with cost implications understood
5. Define a monitoring and operations baseline and challenge its gaps
6. Review security-by-design decisions and identify failure modes
7. Recognise IaC patterns and critique pipeline and deployment approaches
8. Translate an architecture design into engineer-ready specifications
9. Govern operational handover from build to run

---

## Course Scope

### In Scope

- Module content: architect briefing, engineering view, Azure service map, failure modes, review questions
- Labs: structured practice with real Azure portal and CLI tasks
- Artefacts: governance and design documents learners produce and keep
- Senior SA review: challenging questions and sign-off criteria for advanced learners
- Three completion tracks: Foundation, Practitioner, Senior Architect

### Out of Scope

- Microsoft Learn content reproduction
- Beginner concepts or step-by-step portal walkthroughs without design context
- Azure tenant infrastructure (no live resources are managed in this repo)
- Velocity Academy site configuration (managed in the velocity-academy repo)
- Certification exam preparation (certification content is in the repo root)

---

## Editorial Standards

**Voice:** Direct. Confident. Peer-to-peer. Not tutorial voice. The learner is already a senior professional.

**Depth:** Architect-level recognition and decision-making — not configuration-level detail for its own sake. Every engineering detail should connect to a design decision, a risk, or a governance concern.

**Format:** Markdown only. Lowercase hyphenated filenames. Fixed module and lab structure throughout.

**Accuracy:** All Azure content must reflect current service behaviour. Flag dated content with `> Note: verify against current Azure documentation — this section was last reviewed [month year].`

**Artefacts:** Every artefact should be usable in a real engagement with minimal modification. Templates should be filled, not just described.

**Review questions:** Should require reasoning, not recall. A good review question cannot be answered by searching the module text alone.

**Senior SA review:** Should challenge at the level of a principal architect or technical director — asking about trade-offs, risks, governance gaps, and handover implications.

---

## Success Criteria

The course is successful when:

- A learner with prior Azure experience can complete modules without feeling talked down to
- A practitioner-track learner produces artefacts that would pass peer review in a real engagement
- A senior architect-track learner can challenge a team's Azure design with authority
- The QA lead can verify every completion criterion without ambiguity
- The course does not duplicate Microsoft Learn — it adds the layer Microsoft Learn doesn't provide

---

## Ownership

| Role | Responsibility |
|---|---|
| Product Manager | Scope, sequencing, release decisions |
| UX Lead | Learner flow, clarity, and experience quality |
| Lead Designer | Page structure, formatting, visual consistency |
| Senior Azure Solution Architect | Azure technical accuracy, review quality, artefact correctness |
| QA Lead | Testing, acceptance criteria, release gates |
| Product Owner | Final approval |

---

*Version 1.0 · Phase 1–2 · ZenCloud Global Consultants*
