# Senior SA Review Rubric

This rubric is used to assess Senior Architect track completion and to guide the quality of Senior SA review sections in each module.

---

## Purpose

The Senior SA review is not a knowledge test. It is a governance and design authority test. It asks: can this person challenge a team's Azure design, identify risks that a junior engineer would miss, and produce artefacts that go directly into build?

A passing senior SA review demonstrates architecture seniority, not just technical knowledge.

---

## Scoring

Each review question or artefact annotation is assessed on four dimensions:

| Dimension | What It Measures | Pass Threshold |
|---|---|---|
| **Depth** | Does the answer go beyond what the module text states? | Yes — adds reasoning, trade-offs, or context not stated in the module |
| **Risk identification** | Does the answer name a real failure mode or governance gap? | At least one specific, non-generic risk named |
| **Handover readiness** | Would an engineer be able to act on this? | Yes — specific enough to guide a decision or action |
| **Challenge quality** | Would a principal architect accept this as a peer-level response? | Yes — not superficial, not vague |

---

## Scoring Guide

### Depth

| Score | Description |
|---|---|
| 0 | Repeats module text verbatim or gives a one-word answer |
| 1 | Paraphrases the module with minor addition |
| 2 | Adds context from real-world experience or adjacent Azure knowledge |
| 3 | Synthesises multiple considerations into a reasoned position |

### Risk Identification

| Score | Description |
|---|---|
| 0 | No risk named, or generic ("it could fail") |
| 1 | Names a risk category without specifics |
| 2 | Names a specific, realistic risk with Azure context |
| 3 | Names a risk and explains the detection or mitigation pattern |

### Handover Readiness

| Score | Description |
|---|---|
| 0 | Answer is not actionable — no engineer could act on it |
| 1 | Partially actionable — direction is clear but detail is missing |
| 2 | Actionable — an engineer with the context could act on it |
| 3 | Directly usable — could go into an engineer brief without modification |

### Challenge Quality

| Score | Description |
|---|---|
| 0 | Does not challenge — accepts the stated pattern uncritically |
| 1 | Mild challenge — identifies a gap but does not propose an alternative |
| 2 | Substantive challenge — proposes a different approach with reasoning |
| 3 | Principal-level challenge — frames the question, the trade-offs, and the governance implications |

---

## Pass Threshold

**Senior Architect track pass:** Total score ≥ 8 out of 12 per module review, with no dimension scoring 0.

A score of 0 in any dimension is an automatic defer — the learner must revise that answer before the module is signed off.

---

## Artefact Annotation Standard

For Senior Architect track, artefacts must include governance annotations beyond the base template. Annotations should address:

- **Design decision:** Why this approach was chosen over the alternative
- **Risk accepted:** What risk is being accepted and why
- **Governance note:** What ongoing control, review, or audit applies to this artefact
- **Handover note:** What the operations team needs to know about this configuration

Annotations that are generic ("this is important") do not pass. Annotations must be specific to the artefact context.

---

## Reviewer Guidance

When conducting a Senior SA review:

1. Read the learner's answers before reading the model answers
2. Score each dimension independently
3. Note the specific gap if a score of 0 or 1 is given
4. Confirm the artefact annotations are specific, not generic
5. Record the sign-off in [testing/release-acceptance.md](testing/release-acceptance.md) or the learner's own record

The purpose of the review is to develop the learner, not just gate them. A score of 1 in risk identification should come with specific feedback on what risk was missed and why it matters.

---

## Model Answer Quality Bar

When authoring senior SA review model answers in module files, the answer must:

- Score 3 in at least two dimensions
- Score ≥ 2 in all dimensions
- Be specific enough that a senior engineer could disagree with it and explain why

Generic model answers ("consider security") are not accepted. If a model answer cannot be written at this standard for a given question, the question should be revised.
