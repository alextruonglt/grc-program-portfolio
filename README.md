# GRC Program Portfolio
## Azure Cloud Environment | Live Production Assessment | March 2026

**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Assessment Date:** March 5, 2026
**Environment:** Live Production Microsoft Azure Shared Tenant (~1,000+ Users)
**Frameworks:** NIST SP 800-53 Rev 5 | NIST CSF 2.0 | ISO/IEC 27001:2022 | SOC 2 TSC | MITRE ATT&CK

---

> ⚠️ **Disclaimer:** All documents in this repository are sanitized versions of work products produced for a real, live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This repository is published for portfolio and educational purposes only.

---

## What This Repository Is

This is a complete GRC program built from scratch on a real, live Azure shared tenant serving approximately 1,000 or more users. It is not a simulation. It is not a lab exercise. Every finding, every number, every KQL query result, and every remediation target in this repository traces to a confirmed condition in a production environment assessed on March 5, 2026.

The program started with a read-only multi-framework gap assessment of the live tenant. That assessment found no GRC program of any kind: no policies, no procedures, no risk register, no POA&M, no detection rules, no vendor assessments, no configuration baselines, and no formal incident response capability. What it did find was a confirmed active credential attack, 83,600 sign-ins completing without MFA, 185 critical vulnerabilities with zero remediated, an empty endpoint protection telemetry table for 126 assets, and five permanent Global Administrators with no PIM configuration.

Everything in this repository is the documented response to those findings.

---

## The Numbers That Matter

These are real values from real queries against a real production system on March 5, 2026.

| Metric | Value | Significance |
|---|---|---|
| SFA sign-ins out of 203,906 total | 83,600 (41%) | Any account accessible with a password alone |
| CA policies enforced | 0 | CA portal returned HTTP 401 |
| Permanent Global Administrators | 5 | Max allowed: 2. PIM eligible assignments: 0 |
| PIM activation history entries | 0 | No privileged access ever governed |
| Assets under Tenable management | 126 | All 126 had zero endpoint telemetry in Sentinel |
| ProtectionStatus table rows | 0 | Table never populated; not a data flow issue |
| Critical vulnerabilities open | 185 | Findings remediated: 0 |
| High vulnerabilities open | 391 | Total open findings: 1,344 |
| Active Sentinel analytics rules | 0 | No detection layer existed |
| PowerShell encoded alerts, untriaged | 24,905 | Highest-volume alert type; zero response |
| Accounts with blank mandatory attributes | 2,315 of 2,315 | 100% of user accounts |
| Active credential attack (failed sign-ins) | 512 from single source | No lockout, no response, no detection |
| GRC program documents before assessment | 0 | Nothing existed |
| GRC program documents after assessment | 30+ | This repository |

---

## Repository Structure

```
/01-framework-assessments       Four independent gap assessments, same environment, same day
/02-program-governance          GRC Program Charter and System Security Plan
/03-identity-access             Access Control Policy, PAM Standard, ILM Procedure
/04-vulnerability-management    Vulnerability Management Policy, Procedure Report, Patch Procedure
/05-security-monitoring         Security Monitoring Policy, KQL Detection Rules, SIEM Use Cases
/06-configuration-baseline      Configuration Baseline Standard
/07-third-party-risk            TPRM Template with completed Tenable.io assessment
/08-risk-tracking               POA&M and Risk Register
/09-program-policies            Information Security Policy and Incident Response Plan
```

Each folder contains a README.md that explains the documents inside it, why they were written, what real findings drove them, and how they connect to the rest of the repository.

---

## The Story This Repository Tells

**Folder 01 identifies what was broken.** Four frameworks. One environment. One day. NIST 800-53 rated 58% non-compliant across 12 control families. CSF 2.0 rated Tier 1 (Partial) across 5 of 6 functions. ISO 27001 found 15 nonconformities including three that block certification. SOC 2 found 8 of 12 criteria Not in Place with 9-12 months estimated to audit readiness. The same critical gaps appear in every assessment because they reflect the same real environment.

**Folder 02 establishes the authority to fix it.** The GRC Program Charter formally authorizes the GRC Analyst to require evidence, assign remediation, and escalate non-compliance. The System Security Plan documents the system under the NIST 800-18 framework, establishes the authorization boundary, and shows a 45% effective control implementation rate across 126 reviewed controls. The IATO status is real: three Critical-tier POA&M items were open when the SSP was written.

**Folders 03 through 07 execute the remediation.** Each folder addresses one or more control domains that the assessments rated as deficient. Every document in every folder cites the specific POA&M item it closes and the specific framework controls it implements.

**Folder 08 tracks everything.** The POA&M converts 20 identified gaps into tracked tasks with owners, timelines, and evidence requirements. The Risk Register formalizes 17 risks with inherent and residual scoring, where five risks remained Critical residual because the existing controls provided no meaningful reduction in exposure.

**Folder 09 governs the whole.** The Information Security Policy is the document that did not exist before this engagement. It is the foundational authority from which everything else derives. The Incident Response Plan converts the detection capability in Folder 05 into an operational response capability. Six playbooks. All built from real alert types confirmed in the live environment.

---

## Why the Folder Order Matters

The folders are arranged in the order a GRC program actually develops:

1. You assess before you build
2. You establish governance authority before you write policy
3. You write policy before you write procedure
4. You define what "correctly configured" means before you monitor for drift
5. You track risks and remediation throughout
6. You anchor it all with a foundational policy that governs everything

This is not a collection of independent documents. It is a program. Every document references the findings that required it, the policies that govern it, and the tracking items that measure its completion.

---

## Document Inventory

| Folder | Document | Type |
|---|---|---|
| 01 | nist-800-53-gap-assessment.md | Gap Assessment |
| 01 | nist-csf-gap-assessment.md | Gap Assessment |
| 01 | iso-27001-gap-assessment.md | Gap Assessment |
| 01 | soc2-tsc-analysis.md | Gap Assessment |
| 02 | grc-program-charter.md | Program Charter |
| 02 | system-security-plan.md | System Security Plan |
| 03 | access-control-policy.md | Policy |
| 03 | pam-standard.md | Technical Standard |
| 03 | identity-lifecycle-management-procedure.md | Operational Procedure |
| 04 | vulnerability-management-policy.md | Policy |
| 04 | vulnerability-management-procedure-report.md | Procedure Report |
| 04 | patch-management-procedure.md | Operational Procedure |
| 05 | security-monitoring-policy.md | Policy |
| 05 | kql-detection-rule-library.md | Technical Library (29 rules) |
| 05 | siem-use-case-documentation.md | Operational Registry (18 use cases) |
| 06 | configuration-baseline-standard.md | Technical Standard |
| 07 | tprm-template.md | Template and Procedure |
| 08 | poam-summary.md | Plan of Action and Milestones |
| 08 | risk-register.md | Risk Register |
| 09 | information-security-policy.md | Governing Policy |
| 09 | incident-response-plan.md | Operational Plan |

**Total: 21 documents across 9 folders**

---

## Key Technical Highlights

**29 production-ready KQL detection rules** organized into six threat categories, each mapped to MITRE ATT&CK tactics and techniques, with full Sentinel scheduling metadata and analyst response guidance. Two rules (EP-001 and EP-002) are pending data source availability because the ProtectionStatus table was empty at assessment time. Every other rule is immediately deployable.

**18 SIEM use cases** providing the operational context that KQL rules alone cannot. Each use case documents why the threat matters in this specific environment, how to distinguish true positives from false positives, who gets notified at each escalation level, and how to measure whether the detection is working. UC-001 and UC-002 were written because the credential attack and 83,600 SFA events were active conditions, not hypothetical threats.

**A completed TPRM assessment** for Tenable.io, one of the two Tier 1 vendors in the live environment. Not a template. A completed assessment against a real vendor with a real composite score (87.7, Low Risk), a real gap identified (Domain 8, sub-processor advance notice not contractually committed), and a real remediation recommendation (test Nessus Agent updates on non-production assets before broad deployment).

**A CBS compliance score of 20-30%** documented honestly in the Configuration Baseline Standard Section 9.3. This is not an estimate. It is the measured starting point against the requirements defined in that document. The same document includes the scoring methodology and KQL queries that will track improvement from that baseline going forward.

**A 45% SSP control implementation rate** across 126 controls reviewed in all 20 NIST 800-53 Rev 5 control families. Three families at 0% effective implementation. The number is in the document because it is accurate.

---

## The Multi-Framework Competency

The same environment was assessed against four frameworks on the same day. The same findings appear in every assessment because they trace to the same real conditions.

| Finding | NIST 800-53 | NIST CSF 2.0 | ISO 27001:2022 | SOC 2 TSC |
|---|---|---|---|---|
| Zero CA policies; 41% SFA | AC-3, IA-2 | PR.AA-01, PR.AA-05 | A.8.5 | CC5, CC6 |
| 5 permanent GAs; no PIM | AC-2, AC-6 | PR.AA-02 | A.8.2 | CC6 |
| 185 critical vulns; 0 fixed | RA-5, SI-2 | ID.RA-01, PR.PS-01 | A.8.8 | CC3, CC7 |
| ProtectionStatus empty | SI-3, AU-6 | PR.IR-01, DE.CM-06 | A.8.7 | CC4, CC7 |
| No risk register | RA-3, PM-9 | GV.RM, ID.RA | Clause 6.1.2 | CC3, CC9 |
| No IR plan | IR-8, IR-4 | RS.MA-01 | A.5.26 | CC7 |
| No third-party inventory | SA-9, SR-3 | GV.SC | A.5.19, A.5.20 | CC9 |

Remediating the CA policy gap closes findings in NIST 800-53 IA-2, CSF 2.0 PR.AA, ISO 27001 A.8.5, and SOC 2 CC6 simultaneously. One control, four frameworks. That efficiency is how mature GRC programs operate.

---

## How to Read This Repository

**If you are a hiring manager or interviewer:** Start with the folder READMEs. Each one explains what the documents contain, what real findings drove them, and how they connect to the rest of the program. The numbers cited throughout are real. If you want to verify any of them, the KQL queries that produced them are in the documents.

**If you are studying GRC:** Read the folders in order. Folder 01 shows you what a multi-framework assessment looks like against a real environment. Folder 02 shows you what a program charter and SSP look like when they are built from evidence rather than templates. Folders 03 through 09 show you the full lifecycle from policy to procedure to technical control to tracking.

**If you are an auditor or reviewer:** The cross-framework mapping tables in the folder READMEs and in the framework assessment documents show how each control gap maps across all four frameworks. The POA&M in Folder 08 shows every open item with its control family mapping, evidence basis, and remediation target. The SSP in Folder 02 shows the authorization status and the conditions that must be met before full ATO.

---

## About This Work

This GRC program was built over multiple sessions following the March 5, 2026 read-only gap assessment of a live Azure shared tenant used for cybersecurity education and professional development. The assessment was conducted as a learning and portfolio-building exercise using real access to a real production environment. All documents were produced by Alex Truong working as an identity and access governance analyst building toward a GRC practitioner role.

The security gaps documented in this portfolio were real gaps in a real learning environment. The remediation program documented here is what a real GRC response to those findings looks like.

No configuration changes were made during or as a result of the assessment. The GRC program documented here is the written deliverable of that work.

---

*This repository represents approximately 30 hours of assessment, analysis, and documentation work. It is published as a portfolio artifact demonstrating applied GRC methodology across multi-framework assessment, policy development, risk management, technical detection engineering, and program governance. All findings are grounded in real telemetry from a real production environment.*
