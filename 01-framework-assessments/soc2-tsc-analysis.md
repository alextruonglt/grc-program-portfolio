# SOC 2 Readiness Assessment
## ABC Corp | Microsoft Entra ID Tenant

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity & Access Governance Analyst
**Audit Date:** March 5, 2026
**Classification:** CONFIDENTIAL

---

> **Disclaimer:** This is a sanitized version of a SOC 2 Readiness Assessment (pre-audit gap analysis) conducted on a **live, active Microsoft Entra ID environment** serving approximately 1,000 or more users. All organization names, account names, domain references, and identifying details have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This report is published for portfolio and educational purposes only.
>
> This assessment was conducted by a real identity and access governance practitioner against a real production environment. The data, findings, and recommendations reflect actual conditions observed during the assessment. This is not a fabricated or simulated scenario.

---

> **Important Notice:** This document is a SOC 2 readiness assessment and pre-audit gap analysis. It is NOT a SOC 2 attestation report, and it does not constitute an opinion on the design or operating effectiveness of controls. No CPA firm has been engaged, and no attestation has been issued. This report is intended solely to identify gaps and guide remediation efforts prior to a formal audit engagement.

---

## 1. Executive Summary

ABC Corp operates as a shared Azure tenant serving approximately 1,000 or more users across its platform. This SOC 2 readiness assessment evaluates the environment against the AICPA Trust Services Criteria (TSC) 2017, with Security (Common Criteria) assessed in full and Availability assessed where evidence is available.

The current readiness posture is assessed as **early-stage** with significant gaps across foundational governance, access control, and vulnerability management domains. Of the ten criteria categories evaluated, no category achieves Audit Ready status. The majority fall into the Not in Place or Partially in Place ratings, indicating that a formal SOC 2 audit engagement today would result in widespread findings.

**Top Three Risks to Audit Success:**

1. **Access Control Deficiencies:** Only 59% of sign-ins are MFA-protected, Conditional Access enforcement is zero across 203,906 sign-ins, and five Global Administrator accounts are held as permanent active assignments with no Privileged Identity Management (PIM) configured. These are near-certain findings under CC6.
2. **Unresolved Vulnerability Backlog:** Vulnerability scans reveal 185 critical and 391 high findings across 126 assets, with zero fixed findings recorded. Scans are run ad hoc rather than on a defined schedule. This directly impacts CC7 and CC3.
3. **Absent Governance Documentation:** No formal risk register, no System Security Plan, no risk management strategy, no internal audit program, and no management review of the ISMS are in place. These gaps affect CC1 through CC3 and CC9.

**Estimated Time to Audit Readiness:** 9 to 12 months, assuming dedicated remediation resources and leadership commitment.

**Single Most Important Action:** Enforce Conditional Access policies with MFA for all users immediately. This single control closes the largest measurable security gap in the environment and is a prerequisite for virtually every other remediation activity.

---

## 2. Scope and Methodology

### 2.1 Trust Services Criteria in Scope

| Criteria | Rationale for Inclusion |
|---|---|
| CC - Security (Common Criteria) | Required for all SOC 2 reports; assessed in full across CC1-CC9 |
| A - Availability | Assessed where supporting evidence exists; relevant given cloud-hosted environment with backup enrollment |

### 2.2 Trust Services Criteria Out of Scope

| Criteria | Justification for Exclusion |
|---|---|
| PI - Processing Integrity | No business processing functions exist in the lab environment. The environment is educational and does not process financial or transactional data on behalf of customers. |
| C - Confidentiality | No formally classified confidential data is in scope. The environment does not hold data subject to contractual confidentiality obligations. |
| P - Privacy | No personal data processing commitments to customers exist. The organization has not established privacy notices or commitments that would trigger TSC Privacy criteria. |

### 2.3 Assessment Approach

This assessment was conducted in a read-only, documentation and analysis capacity against a live production environment. No changes were made to the environment during the assessment. Evidence was gathered from the following sources:

- Microsoft Entra ID tenant data (users, roles, MFA rates, Conditional Access configuration, PIM status)
- Microsoft Sentinel / Log Analytics Workspace (LAW) data including AuditLogs, SecurityAlert, SecurityIncident, SecurityBaseline, and ProtectionStatus tables
- Third-party vulnerability management platform exports
- Governance questionnaire responses
- Documentation inventory

Each Common Criteria category was evaluated against AICPA points of focus. Readiness ratings reflect the assessor's judgment of whether sufficient evidence exists to support auditor testing at this time.

### 2.4 Environment Constraints

ABC Corp operates as a shared Azure tenant with an educational or training platform context, not a traditional commercial SaaS or enterprise production environment. Several controls that would be standard in a production SOC 2 scope (such as physical security, formal HR onboarding, and customer-facing SLAs) are either not applicable or require adapted justification. Where controls are genuinely not applicable, this report notes the rationale rather than treating absence as a gap.

### 2.5 Readiness Assessment vs. SOC 2 Attestation

A SOC 2 readiness assessment is a consulting engagement conducted to identify gaps before a formal audit. It does not produce an audit opinion, a type I or type II report, or any assurance to third parties. A SOC 2 attestation requires engagement of a licensed CPA firm operating under AICPA AT-C Section 205. This report serves only as an internal preparation tool.

---

## 3. Common Criteria (CC) Assessment

### CC1 - Control Environment

**Readiness Rating: Partially in Place**

**Evidence Present:**
- A named cybersecurity owner exists.
- A cybersecurity policy and strategy document exists.
- Risks have been communicated to leadership.
- Security roles are documented and security oversight evidence exists.

**Gaps and Findings:**
The AICPA points of focus for CC1 require demonstration of board or management oversight, defined accountability structures, and evidence that the entity has committed to competence. While governance anchors are present, the organization lacks a formal risk management process, cybersecurity budgeting evidence, and a management review cycle. The absence of a formal asset inventory means the entity cannot demonstrate it understands the boundaries of what it is protecting. One hundred percent of user accounts in Entra ID have a blank Department field, indicating that organizational structure is not reflected in identity data, which weakens accountability tracing.

**Recommendations:**
Establish a documented management review cycle (at minimum quarterly). Populate Department attributes for all 2,315 Entra ID user accounts. Document a cybersecurity budget, even if nominal, to demonstrate management commitment.

---

### CC2 - Communication and Information

**Readiness Rating: Partially in Place**

**Evidence Present:**
- An information security policy exists.
- Security architecture is documented.
- System boundaries are defined.
- Security roles are documented.

**Gaps and Findings:**
CC2 requires that the entity communicates relevant quality information internally and externally. While an information security policy is in place, no ISMS scope document, Statement of Applicability, or system security plan exists. There is no documented process for communicating policy updates to the large user population. The absence of a nonconformity process means deviations from policy cannot be tracked or corrected in a structured way.

**Recommendations:**
Develop an ISMS scope document and a Statement of Applicability mapping controls to the TSC. Implement a documented policy acknowledgment process for users. Create a nonconformity and exception tracking log.

---

### CC3 - Risk Assessment

**Readiness Rating: Not in Place**

**Evidence Present:**
- Vulnerability scan data is available (most recent scan: February 25, 2026).
- Security alerts are being generated (50 distinct alert types, 24,905 PowerShell encoded command alerts as the top finding).
- Risks have been communicated to leadership at some level.

**Gaps and Findings:**
No formal risk register exists. No formal risk management process has been established. CC3 requires that the entity identifies risks to achieving its objectives, analyzes those risks, and determines how to respond. Scan data reveals 185 critical and 391 high vulnerabilities across 126 assets with zero fixed findings, which demonstrates that risk is being identified but not formally assessed, prioritized, or tracked through resolution. Scans are run ad hoc rather than on a defined schedule, meaning risk identification itself is not systematic. A compliance scan configuration (DISA-STIG) exists but has never been executed (status: Empty), representing a compliance monitoring gap.

**Recommendations:**
Establish a formal risk register immediately. Define a vulnerability management SLA (e.g., critical findings remediated within 30 days, high within 60 days). Schedule scans on a recurring cadence. Execute the compliance scan and document results.

---

### CC4 - Monitoring Activities

**Readiness Rating: Partially in Place**

**Evidence Present:**
- AuditLogs are present with 30-plus days of history covering operations including user updates, password resets, group membership changes, and service principal management.
- SecurityAlert data is present with 50 alert types across Medium, High, and Informational severities.
- SecurityIncident data is present with New, Active, Closed, and Deleted states.
- Authenticated vulnerability scans are being conducted.

**Gaps and Findings:**
SecurityBaseline data is absent (no data found), meaning OS-level compliance monitoring is not functioning. ProtectionStatus data is absent, meaning endpoint protection coverage cannot be verified or reported on. Vulnerability scans are ad hoc, not scheduled, which means monitoring is reactive rather than continuous. Thirty accounts have more than five failed sign-in attempts, with the top account showing 512 failures, indicating that anomalous authentication activity is not being acted upon in a timely manner. While incident data is present, no internal audit program exists and no management review of monitoring outputs is documented.

**Recommendations:**
Onboard SecurityBaseline and ProtectionStatus data sources. Investigate and remediate the 30 accounts with excessive failed sign-in attempts. Establish a documented monitoring review cadence. Define and document alert triage procedures.

---

### CC5 - Control Activities

**Readiness Rating: Not in Place**

**Evidence Present:**
- Conditional Access portal is accessible (HTTP 401 returned on load, suggesting authentication is required).
- Security Defaults are enabled in Entra ID.

**Gaps and Findings:**
This is one of the most critical gaps in the environment. Conditional Access policy count, active policies, and report-only policies are all listed as N/A. Enforcement across 203,906 sign-ins is zero (0 Enforced, 0 Failed, 203,906 NotApplied). This means that the technical control layer intended to enforce MFA, device compliance, and location-based access restrictions is entirely absent. Security Defaults provide a baseline but are insufficient for SOC 2 purposes because they cannot be granularly documented, tested, or reported against TSC control points. Password expiration and password complexity policies were not found, meaning password hygiene controls cannot be evidenced.

**Recommendations:**
Design and implement a Conditional Access policy framework with at minimum: MFA enforcement for all users, MFA enforcement for all administrators, block legacy authentication, and require compliant devices for sensitive access. Disable Security Defaults once CA policies are live. Document password policies formally.

---

### CC6 - Logical and Physical Access Controls

**Readiness Rating: Not in Place**

**Evidence Present:**
- Eight privileged accounts are identified across the tenant.
- MFA is protecting 120,306 of 203,906 sign-ins (59%).
- Guest accounts are limited to 2, and neither holds a privileged role.
- AuditLogs capture access provisioning events.

**Gaps and Findings:**
CC6 is among the highest-priority criteria for SOC 2 and the current posture presents multiple audit-level findings. Five Global Administrators hold permanent active assignments with no PIM configuration, meaning there are no just-in-time access controls, no approval workflows, and no time-bound privilege elevation. Forty-one percent of sign-ins (83,600 total) occur without MFA. Role assignment method is a mix, indicating inconsistency in provisioning practices. No PIM eligible or active assignments exist (0 of each), confirming that privileged access management is absent. No Conditional Access enforcement is in place.

**Recommendations:**
Deploy PIM for all eight privileged accounts immediately. Convert all five Global Administrator permanent assignments to PIM-eligible roles. Enforce MFA via Conditional Access for 100% of sign-ins. Conduct a role assignment review and document the results. Establish a formal access provisioning and deprovisioning procedure.

---

### CC7 - System Operations

**Readiness Rating: Partially in Place**

**Evidence Present:**
- SecurityIncident data is present with lifecycle states (New, Active, Closed, Deleted).
- SecurityAlert data is present with 50 alert types.
- AuditLogs capture system operation events.
- A cybersecurity policy exists.

**Gaps and Findings:**
No formal incident response plan is documented in the evidence set. The presence of 24,905 PowerShell encoded command alerts as the top alert type suggests either a significant threat volume or a high rate of false positives that have not been tuned. Without documented alert triage procedures and incident response runbooks, the organization cannot demonstrate that security events are being evaluated, escalated, and resolved in a controlled manner. Zero fixed vulnerability findings against 185 critical and 391 high vulnerabilities indicates that operational remediation processes are not functioning. Training completion is not tracked, meaning user awareness as an operational control cannot be evidenced.

**Recommendations:**
Develop and document a formal incident response plan. Create alert triage procedures and runbooks for the top alert types. Initiate a vulnerability remediation campaign with a documented prioritization approach. Begin tracking security awareness training completion.

---

### CC8 - Change Management

**Readiness Rating: Not in Place**

**Evidence Present:**
- AuditLogs capture changes including user modifications, service principal additions and removals, password resets, and group membership changes.
- Security architecture is documented.

**Gaps and Findings:**
No formal change management process or change control documentation is in evidence. While AuditLogs provide a record of changes after the fact, there is no pre-authorization workflow, change advisory process, or rollback procedure documented. No System Security Plan exists to define the baseline against which changes are measured. CC8 requires that the entity implements policies over system changes including development, acquisition, and maintenance. The absence of any formal change management documentation means that even well-executed changes cannot be evidenced as controlled.

**Recommendations:**
Implement a change management procedure. At minimum, define categories of change, require documented approval for privileged-account and infrastructure changes, and establish a change log that references AuditLog entries.

---

### CC9 - Risk Mitigation

**Readiness Rating: Not in Place**

**Evidence Present:**
- Known cloud and SaaS services are identified (Microsoft 365, Azure, a vulnerability management platform, and a learning management platform).
- A cybersecurity policy exists.

**Gaps and Findings:**
CC9 addresses vendor and business partner risk management. Third-party providers have not been formally inventoried beyond a known-services list. No supplier security requirements exist in contracts. No third-party monitoring process is in place. The identified cloud/SaaS services have not been formally assessed for security posture. CC9 also covers risk transfer mechanisms such as cyber insurance, for which no evidence was provided.

**Recommendations:**
Develop a formal third-party/vendor inventory. Document the security posture or contractual security requirements for each known service. Establish a periodic vendor review process. Evaluate cyber insurance coverage and document the decision.

---

## 4. Availability (A1) Assessment

### A1.1 - Availability Performance Objectives

**Readiness Rating: Not in Place**

No RTO (Recovery Time Objective) or RPO (Recovery Point Objective) has been documented. No uptime SLA or availability target is defined for the environment.

**Recommendations:** Define and document RTO and RPO targets. Establish an availability baseline or SLA appropriate for the service context.

---

### A1.2 - Environmental Protections, Software, Data Backup, and Recovery

**Readiness Rating: Partially in Place**

**Evidence Present:**
- Azure Backup is enrolled for the environment.
- The environment is cloud-only, eliminating physical infrastructure risks.

**Gaps and Findings:**
Azure Backup enrollment provides a foundational control, but no documented backup policy, retention schedule, or backup verification procedure exists in evidence. Without RTO/RPO documentation, the backup configuration cannot be evaluated against a recovery objective. Recovery has not been tested.

**Recommendations:** Document the backup policy including retention period and coverage scope. Test recovery procedures and document results. Align backup configuration to defined RTO/RPO targets once established.

---

### A1.3 - Recovery Testing

**Readiness Rating: Not in Place**

No recovery plan exists and no recovery test has been conducted. A1.3 requires that recovery procedures are tested to confirm that the entity can restore operations within its availability commitments.

**Recommendations:** Develop a documented recovery plan. Conduct and document at least one recovery test annually. Record test results, findings, and any corrective actions.

---

## 5. SOC 2 Readiness Scorecard

| CC Category | Readiness Rating | Top Finding | Priority |
|---|---|---|---|
| CC1 - Control Environment | Partially in Place | No formal risk management process; 100% blank Department fields | High |
| CC2 - Communication | Partially in Place | No ISMS scope document, no SOA, no nonconformity process | High |
| CC3 - Risk Assessment | Not in Place | No risk register; 185 critical/391 high vulns with 0 fixed | Critical |
| CC4 - Monitoring Activities | Partially in Place | No SecurityBaseline or ProtectionStatus data; no monitoring review cadence | High |
| CC5 - Control Activities | Not in Place | 0 CA policies enforced across 203,906 sign-ins; no password policies found | Critical |
| CC6 - Logical Access | Not in Place | 5 permanent GAs, no PIM, 41% of sign-ins without MFA | Critical |
| CC7 - System Operations | Partially in Place | No IR plan; 24,905 encoded PS alerts unaddressed; 0 vulns fixed | High |
| CC8 - Change Management | Not in Place | No change control process; no system security plan | High |
| CC9 - Risk Mitigation | Not in Place | No vendor inventory; no supplier security requirements | Medium |
| A1.1 - Availability Objectives | Not in Place | No RTO/RPO defined | Medium |
| A1.2 - Backup and Recovery | Partially in Place | Azure Backup enrolled but no documented policy or test | Medium |
| A1.3 - Recovery Testing | Not in Place | No recovery plan; no test conducted | Medium |

### Overall Readiness Statement

Of the 12 criteria sub-categories assessed, **4 are rated Partially in Place (33%)** and **8 are rated Not in Place (67%)**. Zero criteria achieve Largely in Place or Audit Ready status.

This result indicates that the environment is **not prepared for a SOC 2 audit engagement at this time.** Based on the volume and severity of gaps identified, a realistic timeline to first audit readiness is **9 to 12 months**, contingent on dedicated remediation effort beginning immediately. The three Critical-priority items (CC3, CC5, CC6) represent the minimum threshold that must be resolved before any audit engagement is appropriate.

---

## 6. Pre-Audit Remediation Plan

### Sprint 1: Days 1-30 - Foundational Gaps

| Action Item | Owner Role | Effort | CC Category Closed |
|---|---|---|---|
| Enforce MFA via Conditional Access for all users | Identity Admin | 1-2 days | CC5, CC6 |
| Deploy PIM for all 8 privileged accounts; convert 5 GAs to eligible | Identity Admin | 2-3 days | CC6 |
| Block legacy authentication via CA policy | Identity Admin | 1 day | CC5, CC6 |
| Populate Department field for all 2,315 Entra ID users | Identity Admin / HR | 3-5 days | CC1 |
| Establish a formal risk register | Security Owner | 3-5 days | CC3 |
| Initiate vulnerability remediation campaign (prioritize 185 critical findings) | Security Engineer | Ongoing | CC3, CC7 |
| Schedule vulnerability scans on recurring weekly cadence | Security Engineer | 1 day | CC3, CC4 |
| Execute compliance scan (DISA-STIG) and document results | Security Engineer | 1-2 days | CC3 |
| Investigate and document action on 30 accounts with 5-plus failed sign-ins | Identity Admin | 2-3 days | CC4, CC6 |

---

### Sprint 2: Days 31-60 - Control Documentation

| Action Item | Owner Role | Effort | CC Category Closed |
|---|---|---|---|
| Draft and publish ISMS scope document | Security Owner | 3-5 days | CC2 |
| Draft Statement of Applicability (SOA) mapping controls to TSC | Security Owner | 5-7 days | CC2 |
| Develop and publish formal incident response plan | Security Owner | 5-7 days | CC7 |
| Create alert triage procedures and top-10 alert runbooks | Security Analyst | 5-7 days | CC7 |
| Draft and implement change management procedure | Security Owner | 3-5 days | CC8 |
| Develop a formal third-party vendor inventory | Security Owner | 2-3 days | CC9 |
| Document security requirements for all known cloud/SaaS services | Security Owner | 3-5 days | CC9 |
| Define and document password policy (complexity, expiration) | Identity Admin | 1-2 days | CC5 |
| Define RTO/RPO targets and document availability commitments | Security Owner | 2-3 days | A1.1 |
| Begin tracking security awareness training completion | Training Coordinator | 2-3 days | CC7 |
| Onboard SecurityBaseline data source | Security Engineer | 2-3 days | CC4 |

---

### Sprint 3: Days 61-90 - Evidence Collection and Testing

| Action Item | Owner Role | Effort | CC Category Closed |
|---|---|---|---|
| Conduct and document access provisioning and deprovisioning process review | Identity Admin | 3-5 days | CC6 |
| Document backup policy with retention schedule and coverage scope | Security Owner | 1-2 days | A1.2 |
| Conduct and document first recovery test | Security Engineer | 1-2 days | A1.3 |
| Establish management review meeting cadence; document first review | Security Owner / Leadership | Ongoing | CC1, CC4 |
| Implement nonconformity and exception tracking log | Security Owner | 2-3 days | CC2 |
| Document cybersecurity budget evidence | Leadership | 1-2 days | CC1 |
| Evaluate and document cyber insurance decision | Leadership | 1-2 days | CC9 |
| Conduct internal pre-audit evidence review for all CC categories | Security Owner | 5-7 days | All |
| Engage SOC 2 audit firm for scoping and kickoff | Leadership / Security Owner | 3-5 days | All |

---

## 7. Auditor Selection Guidance

Organizations preparing for their first SOC 2 audit should select a CPA firm with demonstrated experience in cloud-native and SaaS environments, specifically one familiar with Azure, Microsoft Entra ID, and SIEM tooling as evidence sources. Look for firms that have performed SOC 2 examinations under AICPA AT-C Section 205, can articulate how they test each Trust Services Criterion point of focus, and are willing to conduct a pre-audit readiness review before the formal engagement begins. Smaller boutique audit firms with cloud specializations often provide more hands-on engagement and clearer communication for organizations conducting their first examination than larger generalist firms.

A Type I SOC 2 report describes the design of controls at a single point in time and requires the auditor to opine that controls were suitably designed as of the report date. A Type II SOC 2 report covers a period of time (typically 6 to 12 months) and requires the auditor to opine on both design and operating effectiveness across that period. Type II reports are significantly more valuable to customers and stakeholders and are considered the market-standard expectation for most SaaS and cloud service providers.

Most organizations begin with a Type I report for two reasons: it requires less lead time (no observation period), and it allows the organization to establish a control baseline before committing to a Type II testing window. A Type I report can typically be initiated once foundational controls are in place and basic documentation is complete.

Given the current readiness posture assessed in this report, a **Type I report is the recommended starting point.** The organization should complete at minimum Sprint 1 and Sprint 2 remediation activities before engaging an audit firm. A Type I examination could realistically be targeted for 6 to 9 months from the current date, with a Type II engagement following a 6-month observation period thereafter.

---

## 8. Crosswalk Note: SOC 2 Gaps to NIST, CSF, and ISO 27001

The gaps identified in this assessment do not exist in isolation. Addressing them for SOC 2 purposes simultaneously closes control deficiencies across NIST 800-53, the NIST Cybersecurity Framework (CSF), and ISO 27001 Annex A. The following crosswalk maps the top SOC 2 findings to equivalent control families across all four frameworks.

| SOC 2 Gap | TSC Criteria | NIST 800-53 Control Family | NIST CSF Function | ISO 27001 Annex A |
|---|---|---|---|---|
| No formal risk register or risk management process | CC3, CC9 | RA (Risk Assessment) | ID.RA (Risk Assessment) | A.6.1.1, A.8.2 |
| 0 Conditional Access policies enforced | CC5, CC6 | AC (Access Control), IA (Identification and Authentication) | PR.AC (Access Control) | A.9.1, A.9.4 |
| 5 permanent Global Admins, no PIM | CC6 | AC-2, AC-6, AC-17 | PR.AC-4 | A.9.2, A.9.4.2 |
| 41% of sign-ins without MFA | CC6 | IA-2, IA-5 | PR.AC-7 | A.9.4.2 |
| 185 critical and 391 high vulns, 0 fixed | CC3, CC7 | RA-5, SI-2 | ID.RA-1, RS.MI-3 | A.12.6.1 |
| No incident response plan | CC7 | IR (Incident Response) | RS.RP (Response Planning) | A.16.1 |
| No change management process | CC8 | CM (Configuration Management) | PR.IP-3 | A.12.1.2, A.14.2 |
| No vendor inventory or supplier security requirements | CC9 | SA-9, SR (Supply Chain Risk) | ID.SC (Supply Chain Risk) | A.15.1, A.15.2 |
| No RTO/RPO defined, no recovery test | A1.1, A1.3 | CP (Contingency Planning) | RC.RP (Recovery Planning) | A.17.1, A.17.2 |
| No SecurityBaseline or ProtectionStatus data | CC4 | AU (Audit and Accountability), SI-4 | DE.CM (Continuous Monitoring) | A.12.4, A.12.6 |
| No ISMS scope document or SOA | CC2 | PL (Planning) | ID.GV (Governance) | A.5.1, Clause 4.3 |
| No management review cycle | CC1, CC4 | CA (Assessment, Authorization, Monitoring) | ID.GV-4 | Clause 9.3 |

Remediating the SOC 2 gaps identified in this report will produce simultaneous compliance improvements against NIST 800-53 Revision 5, the NIST CSF 2.0, and ISO 27001:2022. Organizations operating in environments with overlapping compliance obligations (FedRAMP, CMMC, HIPAA) will find that the foundational controls required for SOC 2 CC and Availability criteria represent the largest shared control set across all major frameworks.

---

*This assessment was conducted on March 5, 2026 against a live, active environment. All findings reflect real observations. Organization and individual identifiers have been sanitized for public distribution. This report is valid as a point-in-time assessment and should be refreshed if significant changes are made to the environment.*

---

### About This Report

This report was produced as part of a real GRC consulting engagement on a live Microsoft Entra ID tenant. It demonstrates end-to-end SOC 2 readiness assessment methodology including: evidence collection from SIEM and identity platforms, AICPA TSC gap analysis, risk-tiered remediation planning, and cross-framework compliance mapping. The methodology, structure, and findings are authentic and reflect actual practitioner work product.
