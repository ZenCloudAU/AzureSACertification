# UX Test Checklist

**Purpose:** Verify learner experience quality before release. Checks that the course reads clearly at architect level, the learning path is coherent, and learners can navigate without confusion.

**UX Lead:** [Name]
**Release:** [Phase 1 / Phase 2]
**Date executed:** [YYYY-MM-DD]

---

## 1. Voice and Tone

| Item | Check | Status | Notes |
|---|---|---|---|
| Course addresses learner as a peer — not a student | "You are reconnecting" not "In this lesson you will learn" | | |
| No condescending explanations of basic concepts | Assumes architecture seniority | | |
| No tutorial voice in module body | Not "Click the button, then..." — modules state what and why | | |
| No excessive hedging ("you might want to consider possibly...") | Direct, confident | | |
| Consistent second-person voice throughout | "You" not "the learner" | | |
| No marketing language | Not "powerful" or "seamless" | | |

---

## 2. Reading Flow

| Item | Check | Status | Notes |
|---|---|---|---|
| Module 00 Orientation is readable as a standalone entry point | No unexplained forward references | | |
| Module 01 assumes Module 00 without re-explaining it | Good sequencing — not repetitive | | |
| Module 02 references Module 01 where appropriate | Builds correctly on platform baseline | | |
| Section headings are clear — learner knows what they are getting | "Architect Briefing" not "Background" | | |
| Azure service map tables are readable without context | Column headers are self-explanatory | | |
| Failure modes are readable as standalone items | Each one makes sense without reading the whole module | | |
| Review questions are clearly questions — not discussion prompts | Unambiguous what is being asked | | |

---

## 3. Learning Path Coherence

| Item | Check | Status | Notes |
|---|---|---|---|
| Module 00 → Module 01 → Module 02 progression is logical | Each module builds on the previous | | |
| Lab 01 builds on Module 01 — the lab is the application of the module | | | |
| Lab 02 builds on Module 02 — and assumes Lab 01 environment is in place | | | |
| Artefacts are clearly explained in context before the learner is asked to produce them | | | |
| Completion criteria are at the end of each module — easy to find | | | |
| Senior SA review is clearly distinct from Foundation review questions | | | |
| Track choices are explained once (Module 00) and consistently referenced | | | |

---

## 4. Navigation

| Item | Check | Status | Notes |
|---|---|---|---|
| README provides clear navigation to all files | | | |
| Module files link to their associated lab and artefacts | | | |
| Lab files link to their associated artefacts | | | |
| Artefact files link to related artefacts where appropriate | | | |
| Governance files are discoverable from the README | | | |
| Testing files are discoverable from the README | | | |
| A learner starting at README can reach any file in 2 clicks | | | |

---

## 5. Track Experience

**Foundation track test:** Read Module 01 through to Completion Criteria. A Foundation learner should be able to confirm they know what to do without reading the lab or artefact sections.

| Item | Status | Notes |
|---|---|---|
| Foundation completion criteria are unambiguous | | |
| Review questions for Foundation are answerable from the module text alone | | |
| Foundation learner is not required to read lab or artefact content to complete | | |

**Practitioner track test:** Follow Lab 01 steps. A Practitioner learner should be able to complete every step and produce every artefact without asking for help.

| Item | Status | Notes |
|---|---|---|
| Lab prerequisites are complete and realistic | | |
| Lab steps are at the right level — not too detailed (tutorial) or too sparse (unhelpful) | | |
| Evidence capture instructions are clear | | |
| Artefact templates are clearly linked from the lab | | |
| Cost and clean-up instructions are complete | | |

**Senior Architect track test:** Review the senior SA review section in Module 02. A Senior Architect learner should feel genuinely challenged.

| Item | Status | Notes |
|---|---|---|
| Senior SA review questions cannot be answered by paraphrasing the module | | |
| Questions require cross-topic reasoning (module + real-world experience) | | |
| Senior SA review rubric is accessible and clearly explains scoring | | |
| Artefact annotation sections are specific enough to guide Senior Architect track learners | | |

---

## 6. Format and Consistency

| Item | Check | Status | Notes |
|---|---|---|---|
| All modules use the same heading structure | | | |
| All labs use the same heading structure | | | |
| Tables are used for: service maps, role assignments, comparison matrices | | | |
| Code blocks are used for CLI commands and code examples | | | |
| No orphaned heading without content | | | |
| No placeholder text left unfilled in module or lab body (artefacts may have fill-in fields) | | | |

---

## UX Test Result

| Dimension | Status | Notes |
|---|---|---|
| Voice and tone | | |
| Reading flow | | |
| Learning path coherence | | |
| Navigation | | |
| Track experience (all three tracks) | | |
| Format and consistency | | |

**Overall UX status:** [ ] Pass — proceed to Azure technical validation / [ ] Revise — return to author with specific feedback

**UX Lead sign-off:** [Name] — [Date]

---

## Revision Notes

| Section / File | Issue | Required change |
|---|---|---|
| | | |
