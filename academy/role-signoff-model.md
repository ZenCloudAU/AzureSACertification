# Role Sign-Off Model

## Roles

### Product Owner

Holds final approval authority for the course and all releases.

**Signs off:** Release acceptance. Any scope change that affects the product promise.

---

### Product Manager

Owns scope, sequencing, and release decisions. Accountable for the course charter, product map, and release plan.

**Signs off:** Phase milestone completion. Module inclusion and ordering. Artefact list. Track definitions.

**Review gate:** Each phase release requires PM confirmation that all scope items are complete and all gaps are documented.

---

### UX Lead

Owns learner flow, clarity, and experience quality. Reviews all module and lab content for readability, structure consistency, and learner progression logic.

**Signs off:** UX test checklist for each release. Module structure compliance.

**Review gate:** No module progresses to release without UX review confirming it follows the required structure and reads clearly at architect level.

---

### Lead Designer

Owns page structure, formatting standards, and visual consistency across all Markdown content.

**Signs off:** Formatting and structure compliance per release. Artefact template design.

**Review gate:** Confirms all files use required headings, lowercase hyphenated filenames, and consistent formatting.

---

### Senior Azure Solution Architect

Owns Azure technical accuracy, review quality, and artefact correctness. The final authority on whether Azure content is current, correct at architecture depth, and aligned to real engagement practice.

**Signs off:** Azure technical validation checklist per release. Senior SA review sections in all modules. All artefact templates.

**Review gate:** No module or artefact is released without Senior SA sign-off. If a service has changed since content was authored, the Senior SA flags it for revision before release.

---

### Course Architect

Owns the pedagogical design of the course — how modules sequence, how tracks work, how review questions build on each other, and whether the course achieves its learning outcomes.

**Signs off:** Module learning objectives. Lab design. Completion criteria.

**Review gate:** Confirms each module achieves its stated purpose and completion criteria are unambiguous.

---

### QA Lead

Owns testing and release acceptance. Executes QA checklists and coordinates sign-off from all roles.

**Signs off:** QA checklist, UX checklist, Azure technical validation, and release acceptance record.

**Review gate:** Release blocked if any checklist item is unresolved or if any role has not signed off their gate.

---

## Sign-Off Process

```
Author completes content
       ↓
QA Lead runs qa-checklist.md and ux-test-checklist.md
       ↓
Senior SA runs azure-technical-validation.md
       ↓
UX Lead confirms structure compliance
       ↓
PM confirms scope and sequencing
       ↓
QA Lead compiles release-acceptance.md
       ↓
Product Owner gives final approval
       ↓
Content published to Velocity Academy
```

---

## Sign-Off Record

Sign-off records are maintained in [testing/release-acceptance.md](testing/release-acceptance.md).

Each release record includes:
- Phase and scope
- Date of sign-off for each role
- Any items deferred to a future release with reason noted
- Final approval status
