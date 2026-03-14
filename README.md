# 🔷 Microsoft Certified: Azure Solutions Architect Expert
[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-support%20my%20work-FFDD00?style=flat&logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/pmyint)

> **Certification:** Microsoft Certified: Azure Solutions Architect Expert
> **Exam:** AZ-305 · Designing Microsoft Azure Infrastructure Solutions
> **Renewal passed:** 92% · March 11, 2026 · Valid → April 29, 2027
> **Holder:** Phil Myint · ZenCloud Global Consultants · Brisbane, Australia

---

## 📋 Purpose

Self-directed study repo for the **AZ-305: Designing Microsoft Azure Infrastructure Solutions** exam and annual renewal.

As a Microsoft Azure solutions architect, you translate business requirements into Azure designs aligned to the **Well-Architected Framework** and **Cloud Adoption Framework**. This repo follows the same structured learning model as the [SAP EA Certification repo](https://github.com/ZenCloudAU/SAPEACertification) and [CISSP Certification repo](https://github.com/ZenCloudAU/CISSPCertification): scenario-first, architect's decision lens, elimination frameworks over memorisation.

---

## 🗺️ Repository Structure

```
AZ305/
│
├── README.md                                    ← This file
│
├── docs/
│   ├── exam-overview.md                         ← Official domains, weights, passing score, update history
│   ├── study-plan.md                            ← 6-week plan + renewal sprint approach + WAF pillars
│   └── key-links.md                             ← MS Learn paths, WAF, CAF, per-service docs, community guides
│
├── domains/
│   ├── 01-identity-governance-monitoring.md     ← Entra ID, RBAC, Policy, Mgmt Groups, Monitor (25–30%)
│   ├── 02-data-storage.md                       ← SQL DB, SQL MI, Cosmos DB, Blob, Files, ADLS (20–25%)
│   ├── 03-business-continuity.md                ← HA, ASR, Backup, AZs vs Sets, RTO/RPO (15–20%)
│   └── 04-infrastructure.md                     ← Compute, networking, app arch, data integration (30–35%)
│
├── artifacts/
│   ├── decision-frameworks.md                   ← 10 architect elimination trees + key numbers reference card
│   └── service-comparison-tables.md             ← 10 side-by-side matrices: LB, SQL, messaging, storage, compute
│
├── quiz/
│   ├── 01-identity-governance-monitoring.md     ← 15 Q&As — Domain 1
│   ├── 02-data-storage.md                       ← 15 Q&As — Domain 2
│   ├── 03-business-continuity.md                ← 15 Q&As — Domain 3
│   ├── 04-infrastructure.md                     ← 15 Q&As — Domain 4
│   └── renewal-assessment.md                    ← All 26 renewal questions · 92% result · full analysis
│
├── resources/
│   ├── glossary.md                              ← 65+ Azure service definitions and distinctions
│   ├── exam-traps.md                            ← 30 distractor patterns and elimination rules
│   └── book-notes.md                            ← Notes from uploaded study guides + community sources
│
└── LICENSE                                      ← MIT
```

---

## 📊 Official Exam Domain Structure

| # | Domain | Weight | Key Services |
|---|--------|--------|-------------|
| 1 | Design identity, governance, and monitoring solutions | **25–30%** | Entra ID, RBAC, Policy, Monitor |
| 2 | Design data storage solutions | **20–25%** | SQL DB/MI, Cosmos DB, Blob, ADLS |
| 3 | Design business continuity solutions | **15–20%** | AZs, ASR, Backup, RTO/RPO |
| 4 | Design infrastructure solutions | **30–35% ← highest** | Compute, networking, app arch |

> ⚠️ English exam significantly updated **October 18, 2024** · Reviewed January 14, 2026 · Passing score: **700/1000**

---

## 🏆 Renewal Result

```
✅ PASSED — 92%
March 11, 2026 · Valid until April 29, 2027
Pass threshold: 57% · 26 scenario-based questions · Open-book
```

---

## 🗂️ What's in the Repo

| Folder | Files | What's Inside |
|--------|-------|---------------|
| `docs/` | 3 files | Exam overview, 6-week study plan, all key links |
| `domains/` | 4 files | Full deep-dives for all 4 exam domains |
| `artifacts/` | 2 files | 10 decision frameworks + 10 service comparison matrices |
| `quiz/` | 5 files | 15 Q&As per domain + full renewal assessment (26 Q&As) |
| `resources/` | 3 files | Glossary (65+ terms), 30 exam traps, book + community notes |

---

## 🗺️ Template Alignment (SAP EA / CISSP Repo Parity)

| SAP EA Repo | This Repo | Purpose |
|-------------|-----------|---------|
| `README.md` with 🗺️ structure tree | ✅ Same format, same emoji anchors | Landing page |
| `docs/` — exam-overview, study-plan, key-links | ✅ Identical folder + filenames | Core study docs |
| `domains/` — numbered `01–0N` | ✅ 4 domain files numbered `01–04` | Domain deep-dives |
| `artifacts/` — cheatsheets & mappings | ✅ decision-frameworks, service-comparison-tables | Quick reference |
| `quiz/` — per-domain + milestone file | ✅ per-domain + `renewal-assessment.md` | Practice & simulation |
| `resources/` — glossary, exam-traps, book-notes | ✅ All three present | Supporting material |
| `LICENSE` (MIT) | ✅ MIT | Open source |

---

## 🗺️ How to Navigate

| Goal | Start here |
|------|-----------|
| New to AZ-305 | `docs/exam-overview.md` → `docs/study-plan.md` |
| Study by domain | `domains/04` first (highest weight) → 01 → 02 → 03 |
| Quick decision logic | `artifacts/decision-frameworks.md` |
| Service comparison | `artifacts/service-comparison-tables.md` |
| Domain practice | `quiz/01` through `quiz/04` |
| Renewal prep | `quiz/renewal-assessment.md` + `resources/exam-traps.md` |
| Avoid common traps | `resources/exam-traps.md` |

---

## 🔗 Related Repos

- [SAP EA Certification](https://github.com/ZenCloudAU/SAPEACertification)
- [CISSP Certification](https://github.com/ZenCloudAU/CISSPCertification)

---

*ZenCloud Global Consultants · github.com/ZenCloudAU*
