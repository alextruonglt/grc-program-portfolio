# 08 - Risk Tracking

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Effective Date:** March 5, 2026
**Environment:** Live Production Azure Shared Tenant (~1,000+ Users | March 2026 Assessment Baseline)

---

> ⚠️ **Disclaimer:** All documents in this folder are sanitized versions of risk tracking documents produced following a gap assessment conducted on a real, live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. These documents are published for portfolio and educational purposes only.

---

## What This Folder Contains

| File | Document Type | Purpose |
|---|---|---|
| `poam-summary.md` | Plan of Action and Milestones | Tracks all 20 identified control gaps as actionable items with control mappings, responsible owners, remediation actions, milestone dates, and status |
| `risk-register.md` | Risk Register | Formalizes all 17 identified risks with likelihood and impact scoring, inherent and residual risk calculations, treatment decisions, and POA&M linkages |

These two documents are the operational core of the GRC program. Every assessment finding, every policy requirement, every technical gap in this repository ultimately flows into one of these two documents. The POA&M converts gaps into tracked tasks. The Risk Register converts threats into scored, owned, and treated risks. Without them, the GRC program has no accountability mechanism. With them, it has a living record of exactly where the organization stands and what is being done about it.

---

## Why These Documents Exist

No formal risk management process of any kind existed in the ABC Corp cloud environment before March 5, 2026. The gap assessments in `/01-framework-assessments` rated this as Gap Rating 1 (Not Implemented) for the NIST 800-53 RA control family, Tier 1 (Partial) for the NIST CSF GV.RM category, and a Critical nonconformity (NC-02, NC-04) for ISO 27001 Clause 6.1.2. The SOC 2 assessment rated CC3 (Risk Assessment) as Not in Place. At time of assessment, 1,344 open vulnerability findings existed with no risk owners and no treatment decisions.

**Confirmed at assessment time (real data from a live environment):**

| Finding | Source | Impact |
|---|---|---|
| No risk register existed | Governance documentation review | 1,344 open findings had no risk owners, no scoring, no treatment decisions |
| No POA&M or remediation tracking existed | Governance documentation review | No structured mechanism to prioritize or track control gap closure |
| No risk management methodology was documented | Governance documentation review | No defined process for identifying, scoring, or treating risks |
| 5 Critical-scored risks all actively present in the live environment | Multiple assessment sources | Highest-priority risks were not theoretical; they were confirmed active conditions |

---

## The POA&M

The POA&M is the accountability document of the GRC program. It is the primary artifact a FISMA auditor, an ATO reviewer, or a Security Lead will inspect to understand whether identified gaps are being actively remediated or allowed to accumulate.

**Program scope:** 20 tracked items. 5 Critical priority. 9 High priority. 6 Medium priority.

**Three-phase timeline structure:**

| Phase | Timeframe | Items | Focus |
|---|---|---|---|
| Immediate | 0 to 30 days | 7 items | The five Critical findings plus the two High-priority items with the most immediate threat exposure |
| Short-term | 31 to 90 days | 8 items | High-priority governance and operational gaps requiring structured remediation |
| Program maturation | 91+ days | 5 items | Medium-priority items that build long-term program capability |

**The 20 items cover every major domain in the program:**

| Domain | POA&M Items |
|---|---|
| Identity and access | POA-001 (CA/MFA), POA-002 (PIM/GA count), POA-007 (account attributes), POA-008 (credential attacks) |
| Vulnerability management | POA-003 (vuln remediation SLAs), POA-004 (endpoint protection), POA-005 (alert backlog) |
| Configuration management | POA-006 (security baseline/DISA-STIG) |
| Security monitoring | POA-005, POA-013 (audit log review) |
| Incident response | POA-009 (no IR plan) |
| Risk management | POA-010 (risk register), POA-011 (internal audit), POA-015 (risk methodology) |
| Third-party risk | POA-012 (vendor inventory) |
| Program governance | POA-014 (SSP), POA-017 (program charter/budget) |
| Training | POA-018 (completion tracking) |
| Business continuity | POA-019 (RTO/RPO, recovery testing) |
| Compliance | POA-020 (Statement of Applicability) |

**What makes this POA&M different from a template:** Every finding is cited with its actual evidence source. POA-001 does not say "MFA is insufficient." It says "MFA rate 59%, single-factor 83,600 of 203,906 sign-ins, CA notApplied 203,906, CA Enforced 0" with the exact SigninLogs query that returned those numbers. POA-004 does not say "endpoint protection may be missing." It says "ProtectionStatus query: No data returned. Total endpoints: No data." Every item can be verified against the real assessment data.

**Cross-framework control mapping:** Each of the 20 items is mapped to NIST 800-53, NIST CSF 2.0, ISO 27001, and SOC 2 TSC simultaneously. This is the multi-framework competency the assessment folder demonstrates, applied at the operational tracking level. A reviewer who looks at the framework coverage table at the bottom of the POA&M will see that a single remediation action (enforcing CA policies for POA-001) simultaneously closes gaps in IA-2, PR.AA, A.8.5, and CC6.1. That cross-framework efficiency is how mature GRC programs operate.

---

## The Risk Register

The risk register is the business-level translation of the technical gaps. Where the POA&M asks "what are we doing to fix this," the risk register asks "how bad is it if we do not fix this." Both questions must be answered for risk management to function.

**17 risk entries covering every major threat area:**

| Risk ID | Risk | Inherent Score | Residual Score | Treatment |
|---|---|---|---|---|
| RISK-001 | Unauthorized access via SFA | 25 (Critical) | 20 (Critical) | Mitigate |
| RISK-002 | Permanent GA accounts without PIM | 20 (Critical) | 15 (High) | Mitigate |
| RISK-003 | Critical and High vulns unremediated | 25 (Critical) | 20 (Critical) | Mitigate |
| RISK-004 | Unknown endpoint protection posture | 20 (Critical) | 20 (Critical) | Mitigate |
| RISK-005 | 24,905 uninvestigated security alerts | 20 (Critical) | 16 (High) | Mitigate |
| RISK-006 | No OS-level configuration baseline | 16 (High) | 12 (High) | Mitigate |
| RISK-007 | No user attribute data; access reviews impossible | 12 (High) | 9 (Medium) | Mitigate |
| RISK-008 | Active credential attack activity (512 failures) | 20 (Critical) | 16 (High) | Mitigate |
| RISK-009 | No formal incident response capability | 15 (High) | 12 (High) | Mitigate |
| RISK-010 | No formal risk management process | 16 (High) | 9 (Medium) | Mitigate |
| RISK-011 | No internal audit or management review | 12 (High) | 9 (Medium) | Mitigate |
| RISK-012 | Unmanaged third-party cloud providers | 12 (High) | 8 (Medium) | Mitigate |
| RISK-013 | Insufficient log review process | 12 (High) | 9 (Medium) | Mitigate |
| RISK-014 | No SSP or formal authorization boundary | 9 (Medium) | 6 (Medium) | Mitigate |
| RISK-015 | Unvalidated backup and recovery | 12 (High) | 8 (Medium) | Mitigate |
| RISK-016 | No security awareness training completion tracking | 9 (Medium) | 6 (Medium) | Mitigate |
| RISK-017 | No data classification policy | 9 (Medium) | 9 (Medium) | Mitigate |

**Inherent vs. residual risk distribution:**

The inherent distribution (before controls) shows 5 Critical, 7 High, and 5 Medium risks. The residual distribution (after existing controls) shows 3 Critical, 4 High, and 8 Medium risks with 2 Low. That shift from inherent to residual reflects what little control infrastructure existed at time of assessment: Security Defaults providing baseline MFA prompts for some scenarios, authenticated vulnerability scanning identifying but not remediating findings, SIEM collecting but not triaging alerts. The controls existed in skeleton form. They were not functioning at a level that meaningfully reduced the risks they were supposed to address.

**Why five Critical-rated risks all remained at Critical residual:** RISK-001 (SFA at 41% of sign-ins), RISK-003 (1,344 vulns, zero remediated), and RISK-004 (endpoint protection entirely unknown) all scored Critical residual because the existing controls provided no meaningful risk reduction. Security Defaults did not prevent 83,600 SFA sign-ins. Authenticated scanning that produces 1,344 findings with zero remediations is not reducing vulnerability exposure. An endpoint protection capability that produces zero ProtectionStatus telemetry is not protecting anything that can be confirmed.

---

## How These Two Documents Work Together

The POA&M and the Risk Register are two views of the same data, serving different audiences.

A technical administrator reads the POA&M and understands: what specific actions need to be taken, in what order, by when, and how to confirm the action worked. POA-001 says "configure and enforce at least one Conditional Access policy requiring MFA for all users, validate enforcement via SigninLogs query after deployment, target completion March 20, 2026."

A Security Lead or executive reads the Risk Register and understands: what is the business exposure, how bad is it, what does it compare to if controls were better, and who owns the decision to treat it. RISK-001 says "inherent score 25, residual score 20, both Critical, treatment decision is Mitigate, risk owner is Identity Admin, linked to POA-001."

The POA&M is operational. The Risk Register is strategic. A GRC program needs both. Most GRC portfolios have one or neither. This folder has both, built from the same real evidence base, with explicit linkage between every POA&M item and its corresponding risk entry.

---

## Key Statistics

| Metric | Value | Source |
|---|---|---|
| Total POA&M items | 20 | POA&M summary table |
| Critical priority POA&M items | 5 | POA&M summary table |
| Items in the 0-30 day window | 7 | POA&M timeline |
| Risk entries in the risk register | 17 | Risk register |
| Critical inherent risk scores | 5 | Risk register dashboard |
| Critical residual risk scores | 3 | Risk register dashboard |
| Total open Tenable findings at assessment | 1,344 | Tenable export (cited in RISK-003, POA-003, POA-010) |
| Findings with no remediation at assessment | 1,344 (100%) | Tenable export (Fixed: 0) |
| Frameworks mapped per POA&M item | 4 (NIST 800-53, CSF 2.0, ISO 27001, SOC 2) | POA&M framework coverage table |

---

## Framework Coverage

| Control | NIST 800-53 | NIST CSF 2.0 | ISO 27001:2022 | SOC 2 TSC |
|---|---|---|---|---|
| Risk assessment and risk register | RA-3, RA-5, PM-9 | GV.RM, ID.RA | Clause 6.1.2, A.8.8 | CC3.1, CC3.2 |
| Plan of action and milestones | CA-5, PM-4 | ID.IM-04 | Clause 10.1 | CC4.2 |
| Risk management strategy | PM-9, RA-7 | GV.RM | Clause 6.1 | CC3.1 |
| Corrective action process | CA-5, SI-12 | ID.IM-01 | Clause 10.2 | CC4.2 |
| Authorization and SSP | CA-6, PL-2 | GV.PO | Clause 4.3, 6.1.3 | CC2.1 |

---

## Connection to the Rest of the Repository

These two documents are the lowest layer of the entire GRC program. Every other document in the repository connects upward into them.

- All 17 risk entries in the Risk Register trace to findings first identified in the framework assessments in `/01-framework-assessments`
- All 20 POA&M items link to policies and procedures in folders `/03` through `/07` that were built to close the identified gaps
- The GRC Program Charter in `/02-program-governance` Section 10.3 lists seven risk items with scores and treatments that are the current-state subset of the 17 risks documented here
- The SSP in `/02-program-governance` Section 11 contains the POA&M summary table that references POA-001 through POA-008 as the primary open items at time of SSP publication
- Every policy document in folders `/03` through `/07` references the specific POA&M items it remediates in its Related Documents section
- The SIEM Use Case Documentation in `/05-security-monitoring` lists POA-004 as a blocker for UC-009 and UC-010, directly referencing this folder's content as a dependency for detection coverage
