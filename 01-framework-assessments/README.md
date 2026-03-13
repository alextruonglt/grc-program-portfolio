# 01 - Framework Assessments

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Assessment Date:** March 5, 2026
**Environment:** Live Production Azure Shared Tenant (~1,000+ Users)

---

> ⚠️ **Disclaimer:** All documents in this folder are sanitized versions of assessments conducted on a real, live production Microsoft Azure environment. Organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. These reports are published for portfolio and educational purposes only.

---

## What This Folder Contains

This folder contains four independent gap assessments of the same live Azure environment, each conducted against a different industry framework. Every assessment was performed on the same date against the same tenant, which means the findings are directly comparable and the cross-framework mapping in each report is grounded in the same real telemetry.

All findings, metrics, and statistics in these documents are real. The 83,600 single-factor sign-ins, the 185 critical vulnerabilities with zero remediated, the empty ProtectionStatus table, the five permanent Global Administrators with no PIM: these are actual values pulled from live queries against a production system on March 5, 2026. No data was fabricated or estimated.

---

## The Four Assessments

| File | Framework | Standard | Overall Rating |
|---|---|---|---|
| `nist-800-53-gap-assessment.md` | NIST SP 800-53 Rev 5 | Federal control catalog | Non-compliance rate ~58% across 12 families assessed |
| `nist-csf-gap-assessment.md` | NIST Cybersecurity Framework 2.0 | Risk-based framework | Overall Tier 1: Partial across 5 of 6 functions |
| `iso-27001-gap-assessment.md` | ISO/IEC 27001:2022 | ISMS certification standard | Not certification ready; 15 nonconformities registered |
| `soc2-tsc-analysis.md` | SOC 2 Trust Services Criteria | AICPA attestation standard | 8 of 12 criteria Not in Place; 9-12 months to audit readiness |

---

## Why Four Frameworks on the Same Environment

Most GRC practitioners learn one framework at a time in isolation. Real security programs do not work that way. Organizations face overlapping compliance obligations simultaneously, and the same control gap shows up as a finding in every framework that touches it.

This assessment set was deliberately designed to demonstrate that multi-framework competency. The same environment, the same findings, assessed through four different lenses on the same day. The cross-framework crosswalk sections at the end of each report show exactly how a single remediation action closes gaps across multiple standards at once.

For example: enforcing Conditional Access with MFA closes a finding in all four frameworks simultaneously. It addresses NIST 800-53 IA-2, CSF 2.0 PR.AA, ISO 27001 Annex A 8.5, and SOC 2 CC6. One control, four frameworks, one remediation action. That is how real GRC programs are built efficiently.

---

## Key Findings Across All Four Assessments

These findings are consistent across all four framework reports because they reflect the same real environment:

**Identity and Access Control**
- Zero Conditional Access policies enforced across 203,906 sign-in events (CA portal returned HTTP 401 during assessment)
- 83,600 of 203,906 sign-ins (41%) completed on single-factor authentication
- Five Global Administrator accounts with permanent active assignments and no PIM configured
- 30 accounts with five or more failed sign-in attempts; top account: 512 failures from a single external source
- 100% of 2,315 user accounts had blank Department, Job Title, and Manager attributes

**Vulnerability and Endpoint Management**
- 185 critical and 391 high severity findings across 126 assets; zero findings remediated
- DISA-STIG compliance scan configured but never executed (status: Empty)
- ProtectionStatus table returned zero rows; endpoint protection telemetry entirely absent from Sentinel
- Vulnerability scans run ad hoc with no defined schedule

**Security Monitoring and Governance**
- No formal risk register, risk management process, or risk treatment documentation
- No System Security Plan, ISMS scope document, or Statement of Applicability
- No internal audit program and no documented management review cycle
- 24,905 PowerShell encoded command alerts in Sentinel with no documented triage or response

---

## Cross-Framework Mapping

The table below shows how the same top findings map across all four frameworks. Each document contains a full crosswalk section with deeper detail.

| Finding | NIST 800-53 | NIST CSF 2.0 | ISO 27001:2022 | SOC 2 TSC |
|---|---|---|---|---|
| Zero CA policies; 41% SFA sign-ins | AC-3, IA-2, IA-2(1) | PR.AA-01, PR.AA-05 | A.8.5 Secure Authentication | CC5, CC6 |
| 5 permanent GAs; no PIM | AC-2, AC-6 | PR.AA-02 | A.8.2 Privileged Access Rights | CC6 |
| 185 critical vulns; 0 fixed | RA-5, SI-2, CM-6 | ID.RA-01, PR.PS-01 | A.8.8 Technical Vulnerabilities | CC3, CC7 |
| ProtectionStatus empty | SI-3, AU-6 | PR.IR-01, DE.CM-06 | A.8.7 Malware Protection | CC4, CC7 |
| No risk register | RA-3, PM-9 | GV.RM, ID.RA | Clause 6.1.2, 6.1.3 | CC3, CC9 |
| No audit/management review | CA-2, CA-7 | GV.OV, DE.CM | Clause 9.2, 9.3 | CC1, CC4 |
| No incident response plan | IR-8, IR-4 | RS.MA-01 | A.5.26 | CC7 |
| No third-party inventory | SA-9, SR-3 | GV.SC | A.5.19, A.5.20 | CC9 |

---

## How These Assessments Connect to the Rest of This Repository

These four assessments are the foundation of the entire GRC program documented in this repository. Every policy, procedure, standard, and technical control in the folders that follow was written in direct response to findings first identified here.

- The Identity Lifecycle Management Procedure addresses the account attribute gaps and guest account findings documented in all four assessments
- The Vulnerability Management Policy sets the SLAs that close the zero-remediation finding
- The Security Monitoring Policy and KQL Detection Rule Library address the empty ProtectionStatus table and zero analytics rules findings
- The Configuration Baseline Standard establishes the hardening requirements that the DISA-STIG findings exposed
- The GRC Program Charter and System Security Plan establish the governance framework required by NIST 800-53 PM, ISO 27001 Clauses 4-10, SOC 2 CC1-CC3, and CSF 2.0 GV

The framework assessments told us what was broken. Everything else in this repository is the documented response to that.

---

## Methodology Note

All four assessments were conducted in a strictly read-only capacity. No system configurations were changed during or after the assessment. Evidence was gathered from:

- Microsoft Sentinel and Log Analytics Workspace (KQL queries against AuditLogs, SignInLogs, SecurityAlert, SecurityIncident, ProtectionStatus, SecurityBaseline tables)
- Microsoft Entra ID (user exports, role assignments, PIM status, Conditional Access portal)
- Tenable.io (authenticated vulnerability scan results, DISA-STIG compliance scan status)
- Governance documentation review (policies, architecture documents, training records)

All query results cited in these documents are real results from real executions against the live environment on or around March 5, 2026.
