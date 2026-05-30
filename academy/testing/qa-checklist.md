# QA Checklist

**Purpose:** Gate checks before each course release. Every item must pass or be formally deferred with documented reason before content is published to Velocity Academy.

**QA Lead:** [Name]
**Release:** [Phase 1 / Phase 2]
**Date executed:** [YYYY-MM-DD]

---

## 1. File Structure

| Item | Check | Status | Notes |
|---|---|---|---|
| All files in scope exist at correct paths | Verify against product-map.md | | |
| All filenames are lowercase and hyphenated | No spaces, no uppercase | | |
| No orphaned files (files not referenced from README or product-map) | Cross-reference | | |
| Folder structure matches product-map.md | modules/, labs/, artefacts/, testing/ | | |

---

## 2. Module Structure Compliance

Check every module in scope against the required 12-section structure:

| Required Section | Present in Module 00 | Present in Module 01 | Present in Module 02 |
|---|---|---|---|
| Purpose | | | |
| Architect briefing | | | |
| Engineering view | | | |
| Azure service map | | | |
| Common failure modes | | | |
| Review questions | | | |
| Lab task | | | |
| Artefact output | | | |
| Notes | | | |
| Hints | | | |
| Completion criteria | | | |
| Senior SA review | | | |

---

## 3. Lab Structure Compliance

Check every lab in scope against the required 9-section structure:

| Required Section | Present in Lab 01 | Present in Lab 02 |
|---|---|---|
| Goal | | |
| Scenario | | |
| Prerequisites | | |
| Steps | | |
| Evidence to capture | | |
| Artefact to produce | | |
| Cost and clean-up | | |
| Review questions | | |
| Completion checklist | | |

---

## 4. Artefact Completeness

| Artefact | Exists | Template filled (not blank) | Owner field present | Date field present |
|---|---|---|---|---|
| azure-platform-control-checklist.md | | | | |
| naming-and-tagging-standard.md | | | | |
| budget-and-cost-guardrail.md | | | | |
| iam-rbac-matrix.md | | | | |
| managed-identity-pattern.md | | | | |
| service-principal-governance-standard.md | | | | |
| key-vault-access-pattern.md | | | | |
| break-glass-access-procedure.md | | | | |

---

## 5. Content Quality

| Item | Check | Status | Notes |
|---|---|---|---|
| Modules address architect audience — not beginner voice | No tutorial language, no "first, open the portal and..." | | |
| Review questions require reasoning — not just recall | Cannot be answered by ctrl+F in the module | | |
| Senior SA review questions are at principal-architect level | Would challenge an experienced SA | | |
| Failure modes are specific — not generic | "No budget alert action group" not "budget misconfigured" | | |
| Artefacts are usable without modification for a real engagement | Minimal placeholder text, real structure | | |
| Hints are helpful without giving away the answer | Directional, not prescriptive | | |
| Notes section adds value beyond the module body | Not a summary of what was just said | | |
| Completion criteria are unambiguous — clear pass/fail per track | No "learner understands" — must be verifiable | | |

---

## 6. Cross-Reference Accuracy

| Item | Check | Status | Notes |
|---|---|---|---|
| All internal links in README resolve | Click every link | | |
| All internal links in modules resolve | Lab task links, artefact output links | | |
| All internal links in labs resolve (artefact refs) | | | |
| All internal links in artefacts resolve (related artefact refs) | | | |
| Module list in README matches files in modules/ | | | |
| Lab list in README matches files in labs/ | | | |
| Artefact list in README matches files in artefacts/ | | | |

---

## 7. Governance Files

| File | Exists | Complete | Internally consistent with README |
|---|---|---|---|
| course-charter.md | | | |
| product-map.md | | | |
| completion-tracks.md | | | |
| role-signoff-model.md | | | |
| release-plan.md | | | |
| senior-sa-review-rubric.md | | | |

---

## 8. Testing Files

| File | Exists | Complete |
|---|---|---|
| qa-checklist.md | | |
| ux-test-checklist.md | | |
| azure-technical-validation.md | | |
| release-acceptance.md | | |

---

## QA Result

| Outcome | Count |
|---|---|
| Items passed | |
| Items failed | |
| Items deferred (with reason) | |

**Overall QA status:** [ ] Pass — proceed to UX review / [ ] Fail — return to author / [ ] Conditional — deferred items noted in release-acceptance.md

**QA Lead sign-off:** [Name] — [Date]

---

## Deferred Items

| Item | Reason for deferral | Target release |
|---|---|---|
| | | |
