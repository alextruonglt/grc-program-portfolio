# ISO/IEC 27001:2022 Gap Assessment Report

**Organization:** ABC Corp | Microsoft Entra ID Tenant  
**Prepared by:** Alex Truong | Identity & Access Governance Analyst  
**Audit Date:** March 5, 2026  
**Classification:** CONFIDENTIAL  

---

> **Disclaimer:** This is a sanitized version of an ISO/IEC 27001:2022 Gap Assessment conducted on a live production Microsoft Azure and Entra ID environment. All organization names, account names, domain references, and identifying details have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This report is published for portfolio and educational purposes only.
>
> **This assessment was performed on a real, live environment.** All findings, metrics, and counts reflect actual telemetry data collected during the assessment. Evidence was gathered from Microsoft Sentinel (Log Analytics Workspace), Tenable vulnerability management, and Microsoft Entra ID exports. The assessor operated in a strictly read-only capacity.

---

## Table of Contents

1. Executive Summary
2. Scope and Methodology
3. Clause 4-10 Assessment
4. Annex A Control Assessment
5. Nonconformity Register
6. Certification Readiness Assessment
7. Crosswalk Note: ISO 27001 to NIST 800-53 / CSF

---

## 1. Executive Summary

ABC Corp operates within a shared Microsoft Azure tenant serving approximately 1,000 or more users. This gap assessment evaluates the environment's conformance against ISO/IEC 27001:2022, covering ISMS Clauses 4 through 10 and all applicable Annex A control themes. The assessment was conducted in a read-only capacity using exported telemetry, governance documentation, and identity data.

The overall ISMS maturity of this environment is low. The majority of ISMS clauses yield Nonconformity (NC) findings due to the absence of foundational documentation including an ISMS scope document, Statement of Applicability, formal risk register, internal audit program, and management review process. While a named cybersecurity owner exists and a security policy is present, the governance structure required to sustain a certifiable ISMS has not been formalized.

The most critical nonconformities are as follows. First, no Statement of Applicability (SoA) exists, which is a mandatory deliverable under Clause 6.1.3 and a prerequisite for any Stage 1 certification audit. Second, Conditional Access policies returned a 401 error during assessment, meaning zero CA policies are enforced across 203,906 sign-in events, leaving the tenant without a policy-based access control layer. Third, MFA is enforced on only 59% of sign-ins (120,306 of 203,906), with 83,600 single-factor authentications recorded, representing a critical authentication control failure. Fourth, vulnerability scanning identifies 185 critical and 391 high-severity findings across 126 assets with zero fixed findings to date and a manual-only scan schedule.

Key Annex A gaps include the absence of endpoint protection telemetry, no OS security baseline data, no PIM configuration despite five permanent Global Administrator assignments, and no documented supplier security requirements.

This environment is not ready for ISO 27001 certification. Significant remediation effort is required across governance, risk management, access control, and operational security domains before a Stage 1 audit could be responsibly attempted.

---

## 2. Scope and Methodology

### 2.1 ISMS Scope Definition

The assessment scope encompasses the ABC Corp environment hosted within a shared Microsoft Azure tenant. The environment supports approximately 1,000 or more users and integrates the following cloud and SaaS services: Microsoft 365, Microsoft Azure, a vulnerability management platform, and a learning management system. System boundaries are documented and a security architecture document exists. However, a formal ISMS scope document conforming to Clause 4.3 requirements does not exist. Physical infrastructure is fully cloud-hosted; physical controls are inherited from Microsoft Azure's ISO 27001-certified datacenters and are treated as Not Applicable for this assessment scope.

### 2.2 Annex A Themes Covered

| Theme | Coverage |
|---|---|
| Annex A 5 - Organizational Controls | Assessed |
| Annex A 6 - People Controls | Assessed |
| Annex A 7 - Physical Controls | Inherited from Azure datacenters - N/A for scope |
| Annex A 8 - Technological Controls | Assessed |

### 2.3 Assessment Approach

The assessment was conducted exclusively in a read-only documentation and analysis capacity. Evidence was gathered from the following sources:

- Microsoft Sentinel / Log Analytics Workspace (LAW) KQL query exports covering AuditLogs, SecurityAlert, SecurityIncident, SecurityBaseline, and ProtectionStatus tables
- Tenable vulnerability management platform exports (authenticated scan results, STIG scan status)
- Microsoft Entra ID user, role, and policy exports
- Governance documentation review

Gap ratings follow the scale below:

| Rating | Label | Definition |
|---|---|---|
| 0 | Not Implemented | Control absent, no evidence |
| 1 | Partially | Some elements present but major gaps |
| 2 | Largely | Broadly implemented, minor gaps or undocumented |
| 3 | Fully | Implemented and evidenced |
| NC | Nonconformity | Clause 4-10 requirement not met |
| OFI | Opportunity for Improvement | Annex A control present but improvable |

### 2.4 Assessment Constraints

The following constraints were noted during the assessment and may affect finding severity interpretation:

- The Conditional Access portal returned a 401 Unauthorized error during assessment; CA policy details could not be retrieved
- No Microsoft Entra ID P1 or P2 licensing is present, which limits PIM, Identity Protection, and advanced CA capabilities
- The assessor role was strictly read-only; no system configuration was altered

### 2.5 Physical Controls Inheritance Note

All physical security controls (Annex A 7.1 through 7.4) are inherited from Microsoft Azure's globally distributed datacenter infrastructure. Microsoft Azure maintains ISO 27001 certification, and the physical control obligations for this environment are satisfied through that certification inheritance. These controls are rated N/A throughout this report.

---

## 3. Clause 4-10 Assessment

### Clause 4 - Context of the Organization

**Status: Partial / Nonconformity (NC)**

| Item | Finding |
|---|---|
| Evidence Basis | System boundaries documented; security architecture documented; named cybersecurity owner present |
| Conforming Elements | System boundaries defined; security architecture document exists; stakeholder awareness evidenced |
| Nonconformity | No formal ISMS scope document exists per Clause 4.3; no documented analysis of internal and external issues per Clause 4.1; interested parties and their requirements are not formally documented per Clause 4.2 |
| Recommendation | Produce a formal ISMS scope document explicitly defining the information assets, services, and boundaries within scope. Complete a structured context analysis capturing internal and external factors. Document interested parties and their requirements. |

---

### Clause 5 - Leadership

**Status: Partial / Nonconformity (NC)**

| Item | Finding |
|---|---|
| Evidence Basis | Named cybersecurity owner identified; information security policy exists; security roles documented; security oversight evidence present; risks communicated to leadership |
| Conforming Elements | A named security owner is in place; an information security policy exists; security roles are documented |
| Nonconformity | No cybersecurity budgeting evidence is available, indicating a lack of formal resource commitment per Clause 5.1(e); management commitment is informal rather than structured |
| Recommendation | Formalize top management endorsement of the ISMS through a signed policy statement referencing ISO 27001:2022. Establish and document a cybersecurity budget. Assign a formal ISMS lead role with documented responsibilities. |

---

### Clause 6 - Planning

**Status: Nonconformity (NC)**

| Item | Finding |
|---|---|
| Evidence Basis | No formal risk management process, no risk register, no Statement of Applicability, no data classification policy |
| Conforming Elements | None identified for Clause 6 requirements |
| Nonconformity | Clause 6.1.2 requires a formal information security risk assessment process - none exists. Clause 6.1.3 requires a Statement of Applicability - none exists. Clause 6.1.1 requires risk treatment options to be documented - no risk register exists. |
| Recommendation | Develop a risk assessment methodology aligned to ISO 27005 or equivalent. Build and maintain a risk register. Produce a Statement of Applicability. These three items are non-negotiable prerequisites for certification. |

---

### Clause 7 - Support

**Status: Partial / Nonconformity (NC)**

| Item | Finding |
|---|---|
| Evidence Basis | Security awareness training program exists; training completion is not tracked; no cybersecurity budgeting evidence |
| Conforming Elements | A security awareness training program is in place; security roles are documented |
| Nonconformity | Clause 7.2 requires competence to be determined, maintained, and evidenced - training completion is not tracked. Clause 7.1 requires resources to be determined and provided - no budget documentation exists. Clause 7.5 requires documented information to be controlled - no ISMS document management framework is evidenced. |
| Recommendation | Implement training completion tracking. Establish a documented information management procedure for ISMS records. Allocate and document resources assigned to ISMS operation. |

---

### Clause 8 - Operation

**Status: Partial / Nonconformity (NC)**

| Item | Finding |
|---|---|
| Evidence Basis | Tenable authenticated scans active (last scan 2/25/26); LAW AuditLogs, SecurityAlert, SecurityIncident data present; MFA rate 59%; CA portal returned 401; 185 critical and 391 high findings with zero fixes; DISA-STIG compliance scan status Empty |
| Conforming Elements | Vulnerability scanning is operational; SIEM data collection is active; incident tracking via SecurityIncident table is present |
| Nonconformity | No documented operational security procedures exist. Risk treatment plans are absent per Clause 8.1. The DISA-STIG compliance scan has never been executed. Zero Tenable findings have been remediated. CA policies are inaccessible with zero enforcement. |
| Recommendation | Document operational procedures for vulnerability management, incident response, and access control. Execute the DISA-STIG compliance scan. Establish a remediation workflow that tracks and closes Tenable findings. Restore CA portal access and implement enforced CA policies. |

---

### Clause 9 - Performance Evaluation

**Status: Nonconformity (NC)**

| Item | Finding |
|---|---|
| Evidence Basis | No internal audit program; no management review of ISMS; LAW and Tenable provide metric data but no structured reporting framework |
| Conforming Elements | Technical telemetry is available through LAW and Tenable that could support performance metrics |
| Nonconformity | Clause 9.1 requires monitoring, measurement, analysis, and evaluation - no documented metrics program exists. Clause 9.2 requires internal audits - no internal audit program exists. Clause 9.3 requires management review - no management review has been conducted or documented. |
| Recommendation | Establish an ISMS metrics program using existing LAW and Tenable data. Develop an internal audit plan. Schedule and document management review meetings at least annually. |

---

### Clause 10 - Improvement

**Status: Nonconformity (NC)**

| Item | Finding |
|---|---|
| Evidence Basis | No nonconformity process documented; no corrective action process; no evidence of continual improvement activities |
| Conforming Elements | None identified |
| Nonconformity | Clause 10.1 requires nonconformities to be identified, corrected, and root-cause analyzed - no such process exists. Clause 10.2 requires continual improvement to be evidenced - no improvement activities are documented. |
| Recommendation | Develop a nonconformity and corrective action procedure. Use this gap assessment as the initial nonconformity input to populate a corrective action register. |

---

## 4. Annex A Control Assessment

### 5.1 - Information Security Policies

**Rating: 2 - Largely**

| Item | Detail |
|---|---|
| Evidence | Information security policy exists; cybersecurity strategy exists; security roles documented |
| Finding | A policy exists and has been communicated to leadership. However, there is no documented review cycle, no version control evidence, and no formal policy management procedure ensuring periodic review. |
| Recommendation | Establish a formal policy review schedule (minimum annually). Add version control and approval signatures. Ensure policy covers all required topic areas per ISO 27001:2022 A.5.1. |

---

### 5.23 - Information Security for Use of Cloud Services

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | Known cloud/SaaS services identified (Microsoft 365, Azure, vulnerability management platform, learning management system). No third-party inventory, no supplier security requirements in contracts, no third-party monitoring process. |
| Finding | Cloud services in use are known informally but have not been formally inventoried. No cloud security policy exists. No supplier agreements include information security requirements. No monitoring of third-party service providers is performed. |
| Recommendation | Produce a cloud services inventory. Include security requirements in supplier agreements. Establish a supplier review process. |

---

### 5.31 - Legal, Statutory, Regulatory and Contractual Requirements

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | No data classification policy (status: Unknown); no supplier security requirements in contracts; 100% of accounts have blank Department field in Entra ID |
| Finding | No documented process exists for identifying applicable legal and regulatory requirements. Data classification is unknown. With all accounts lacking departmental data, data ownership and compliance mapping cannot be established. |
| Recommendation | Identify applicable legal and regulatory requirements. Document compliance obligations. Establish a data classification policy. |

---

### 6.3 - Information Security Awareness, Education and Training

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | Security awareness training program exists; training completion is not tracked |
| Finding | A training program is in place. However, without completion tracking, there is no evidence that individuals with ISMS responsibilities have received required training. The program cannot be demonstrated as effective. |
| Recommendation | Implement training completion tracking. Record completions by role and date. Report completion rates to management as an ISMS metric. |

---

### 7.1-7.4 - Physical Security Controls

**Rating: N/A - Inherited**

| Item | Detail |
|---|---|
| Finding | Physical security controls are fully delegated to Microsoft Azure, which maintains ISO 27001 certification. These controls are not applicable to this assessment scope. |
| Recommendation | Maintain documentation confirming reliance on Microsoft Azure's physical security controls. Reference Azure compliance documentation in the SoA when produced. |

---

### 8.2 - Privileged Access Rights

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | 8 privileged accounts total; 5 Global Administrators; mixed role assignment method; PIM not configured; PIM eligible assignments: 0; all GA roles are permanent active; Security Defaults enabled; CA portal returned 401 |
| Finding | The environment has 5 permanent Global Administrator accounts. No PIM is configured, meaning no just-in-time activation, no approval workflow, and no time-bound access for privileged roles. All privileged access is permanently active with no automated controls. |
| Recommendation | Reduce Global Administrator count to a maximum of 2. Enable PIM (requires Entra ID P2) to convert permanent assignments to eligible assignments. Establish a quarterly access review process. |

---

### 8.3 - Information Access Restriction / Identity Management

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | Total users: 2,315; Guest accounts: 2; Guests holding roles: No; 100% of accounts have blank Department field; Password expiration policy: Not found; Password complexity: Not found |
| Finding | The user directory contains 2,315 accounts with no departmental classification, making access review and role-based access control impractical. Password policy details could not be confirmed. No formal identity lifecycle management process is evidenced. |
| Recommendation | Populate Department and job title attributes for all accounts. Define and enforce a password policy. Document a formal identity lifecycle management process. Conduct an access review to confirm all active accounts are still required. |

---

### 8.5 - Secure Authentication

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | MFA rate: 59% (120,306 of 203,906 sign-ins MFA-protected); Single-factor sign-ins: 83,600; CA enforcement: 0 across all 203,906 sign-in events; 30 accounts with more than 5 failed sign-ins; top failing account recorded 512 failures; Security Defaults enabled |
| Finding | MFA coverage of 59% means 83,600 sign-in events occurred with no second factor. This is a critical authentication gap. Zero CA events were enforced. The 30 accounts with repeated failed sign-ins and one account with 512 failures indicate potential brute-force activity that is not being actioned. |
| Recommendation | Investigate and resolve the CA portal 401 issue. Target 100% MFA enforcement via CA policies. Investigate the 30 accounts with high failed sign-in counts for potential compromise. Implement sign-in risk policies where licensing permits. |

---

### 8.7 - Protection Against Malware

**Rating: 0 - Not Implemented**

| Item | Detail |
|---|---|
| Evidence | LAW ProtectionStatus table: No data found; Total endpoints: No data; Protected: No data; Unprotected: No data |
| Finding | No endpoint protection telemetry is available in the Log Analytics Workspace. The ProtectionStatus table returned no data, meaning there is no visibility into whether any endpoints have active malware protection. This is a complete absence of control visibility. |
| Recommendation | Onboard endpoints to Microsoft Defender for Endpoint or equivalent and connect telemetry to LAW. Report on protection coverage as a recurring ISMS metric. |

---

### 8.8 - Management of Technical Vulnerabilities

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | Authenticated scans active; last scan date: 2/25/26; scan schedule: Ad Hoc/Manual; 185 critical findings; 391 high findings; 491 medium findings; 277 low findings; 805 new findings; 0 fixed findings; 126 unique assets; DISA-STIG compliance scan status: Empty (never executed) |
| Finding | Authenticated scanning is in place. However, the scan schedule is manual with no defined cadence, the DISA-STIG compliance scan has never been executed, and zero findings have been remediated. 185 critical and 391 high findings remain open with no evidence of a remediation program. |
| Recommendation | Establish a defined vulnerability scan schedule (recommended: weekly for critical assets). Execute the DISA-STIG compliance scan. Implement formal remediation SLAs. Track and report on fix rates as a key ISMS metric. |

---

### 8.12 - Data Leakage Prevention

**Rating: 0 - Not Implemented**

| Item | Detail |
|---|---|
| Evidence | No data classification policy (status: Unknown); no DLP configuration observed |
| Finding | No data classification policy exists and no data leakage prevention controls are evidenced. Without knowing what data is sensitive and where it resides, DLP controls cannot be meaningfully designed or deployed. |
| Recommendation | Establish a data classification policy as a prerequisite. Deploy Microsoft Purview Information Protection to classify and label sensitive data. Define DLP policies based on classification outcomes. |

---

### 8.14 - Redundancy of Information Processing Facilities

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | Azure Backup enrolled: Yes; RTO/RPO documented: No; Recovery plan tested: No |
| Finding | Azure Backup enrollment is a meaningful control. However, no RTO or RPO targets have been documented, and the recovery plan has never been tested. Backup without tested recovery provides limited assurance. |
| Recommendation | Define and document RTO and RPO targets. Conduct at least one recovery test annually and document results. Establish a business continuity and recovery plan referencing backup configurations. |

---

### 8.15 - Logging

**Rating: 2 - Largely**

| Item | Detail |
|---|---|
| Evidence | LAW AuditLogs data present; 30-plus days of audit history; operations captured include: Update user, Add service principal, Remove service principal, Hard delete service principal, Update PasswordProfile, Add member to group, Reset user password, Add user |
| Finding | Audit logging is active and capturing a meaningful range of identity operations with 30-plus days of retention. Gaps include absence of OS-level and endpoint logging (no SecurityBaseline data, no ProtectionStatus data) and lack of a documented log management policy. |
| Recommendation | Document a log management policy defining log sources, minimum retention periods, and review responsibilities. Extend logging to endpoint and OS-level events. Confirm log integrity protections are in place. |

---

### 8.16 - Monitoring Activities

**Rating: 2 - Largely**

| Item | Detail |
|---|---|
| Evidence | SecurityAlert: 50 alert types; top alert: PowershellEncoded with 24,905 occurrences; severity levels: Medium, High, Informational; SecurityIncident data present with New, Active, Closed, Deleted statuses; SecurityBaseline data: No data found |
| Finding | SIEM-based monitoring is active and generating alerts. The volume of PowershellEncoded alerts (24,905) warrants investigation to determine whether these represent genuine threats or tuning gaps. Incident lifecycle tracking is in place. OS-level baseline monitoring is absent. No formal alert triage or escalation procedure is documented. |
| Recommendation | Review and tune the PowershellEncoded alerts. Document an alert triage and escalation procedure. Enable OS security baseline monitoring to close the SecurityBaseline data gap. |

---

### 8.19 - Installation of Software on Operational Systems

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | Tenable identifies software-related findings within the 185 critical and 391 high findings; no formal software management policy documented |
| Finding | Authenticated scans are positioned to detect outdated or unauthorized software. However, no formal software installation or patch management policy exists. Zero findings have been remediated. |
| Recommendation | Develop a software management policy defining approved software, patch management timelines, and the process for removing unauthorized or end-of-life software. Use Tenable findings to drive a software remediation backlog. |

---

### 8.20 - Networks Security

**Rating: 1 - Partially**

| Item | Detail |
|---|---|
| Evidence | Tenable identifies network exposure findings within the critical and high finding counts; security architecture document exists at a high level |
| Finding | Tenable provides some visibility into network-level exposures. A security architecture document exists. However, no formal network security policy, no segmentation documentation, and no evidence of network access control review processes are present. |
| Recommendation | Document network security controls, segmentation approach, and firewall/NSG rule review cadence. Use Tenable findings to identify and close network exposure findings. |

---

### 8.28 - Secure Coding

**Rating: N/A**

No software development activities are in scope for this environment. This control is not applicable.

---

## 5. Nonconformity Register

| NC ID | Clause / Control | Finding | Risk Level | Recommended Corrective Action | Priority |
|---|---|---|---|---|---|
| NC-01 | Clause 4.3 | No formal ISMS scope document exists | High | Produce a formal ISMS scope document defining system boundaries, services, and exclusions | High |
| NC-02 | Clause 6.1.2 | No formal risk assessment process or methodology documented | Critical | Develop a risk assessment methodology; conduct and document initial risk assessment | Critical |
| NC-03 | Clause 6.1.3 | No Statement of Applicability (SoA) exists | Critical | Produce SoA documenting all Annex A controls with inclusion justification and implementation status | Critical |
| NC-04 | Clause 6.1.1 | No risk register exists; no risk treatment plan documented | Critical | Build and maintain a risk register; produce a risk treatment plan linked to SoA | Critical |
| NC-05 | Clause 7.2 | Training completion not tracked; no competence evidence | Medium | Implement training completion tracking; document competence requirements by role | Medium |
| NC-06 | Clause 8.1 | No documented operational security procedures; DISA-STIG scan never executed | High | Document operational procedures; execute DISA-STIG scan; establish remediation workflow | High |
| NC-07 | Clause 9.2 | No internal audit program exists | High | Develop and execute an ISMS internal audit program with defined scope and frequency | High |
| NC-08 | Clause 9.3 | No management review of ISMS conducted or documented | High | Schedule and document management review meetings; produce minutes and action items | High |
| NC-09 | Clause 10.1 | No nonconformity or corrective action process documented | High | Develop nonconformity procedure; populate initial register using this assessment | High |
| NC-10 | Annex A 8.5 | 83,600 single-factor sign-ins; zero CA policies enforced; CA portal returned 401 | Critical | Resolve CA portal access; enforce MFA for all users; investigate 30 accounts with repeated sign-in failures | Critical |
| NC-11 | Annex A 8.2 | 5 permanent Global Administrator accounts; no PIM configured | High | Reduce GA count to 2; implement PIM eligible assignments; conduct quarterly access review | High |
| NC-12 | Annex A 8.7 | No endpoint protection telemetry available; ProtectionStatus table empty | High | Onboard endpoints to Defender for Endpoint; confirm protection coverage; report as ISMS metric | High |
| NC-13 | Annex A 8.8 | 185 critical and 391 high findings; 0 fixed; manual scan schedule | Critical | Implement automated scan cadence; establish remediation SLAs; execute STIG compliance scan | Critical |
| NC-14 | Annex A 8.12 | No data classification policy; no DLP controls | High | Establish data classification policy; deploy Microsoft Purview DLP controls | High |
| NC-15 | Clause 5.1 | No cybersecurity budgeting evidence; informal management commitment | Medium | Document formal management endorsement; establish and record ISMS resource allocation | Medium |

---

## 6. Certification Readiness Assessment

If ABC Corp were pursuing ISO 27001:2022 certification, the environment is not currently in a position to proceed to a Stage 1 audit. Three gaps are categorically blocking certification readiness.

The first and most urgent gap is the complete absence of a Statement of Applicability. The SoA is a mandatory deliverable explicitly required by Clause 6.1.3 and is one of the first documents a certification auditor will request at Stage 1. Without it, the audit cannot proceed. Producing the SoA also requires a completed risk assessment and risk register, neither of which exists, meaning the foundational planning cycle under Clause 6 must be completed in its entirety before an SoA can be produced.

The second blocking gap is the failure of the authentication and access control framework. With 83,600 single-factor sign-in events, zero enforced Conditional Access policies, five permanent Global Administrators, and no PIM in place, the environment would face immediate major nonconformity findings against Annex A 8.2 and 8.5. These findings are not marginal; they represent a fundamental failure of the identity security controls that an ISO-certified environment would be expected to have in place.

The third blocking gap is the complete absence of Clause 9 and Clause 10 compliance. There is no internal audit program, no management review process, and no corrective action procedure. These three elements form the check and act phases of the PDCA cycle that underpins the ISMS. A certification auditor cannot verify ISMS operation without evidence of these activities.

A realistic timeline for certification readiness, assuming dedicated remediation effort, is 12 to 18 months. The first three months should be spent on documentation foundations: ISMS scope, risk methodology, SoA draft, and policy framework. Months four through nine should address technical control gaps including MFA enforcement, PIM implementation, vulnerability remediation cadence, and endpoint protection coverage. Months ten through eighteen should focus on operating and evidencing the ISMS through completed audit cycles, management reviews, and corrective action closure before engaging a certification body.

---

## 7. Crosswalk Note: ISO 27001 to NIST 800-53 / CSF

The findings in this assessment map closely to several NIST SP 800-53 Rev. 5 control families and NIST Cybersecurity Framework (CSF) 2.0 functions.

The Clause 6 nonconformities regarding the absence of a risk register, risk assessment methodology, and Statement of Applicability map directly to the NIST 800-53 RA (Risk Assessment) family, specifically RA-2 (Security Categorization), RA-3 (Risk Assessment), and RA-5 (Vulnerability Scanning). In CSF 2.0 terms, these gaps fall within the GOVERN (GV) and IDENTIFY (ID) functions, particularly GV.RM (Risk Management Strategy) and ID.RA (Risk Assessment).

The authentication and access control findings under Annex A 8.2 and 8.5 (MFA gaps, permanent GA accounts, no PIM) correspond to NIST 800-53 AC (Access Control) and IA (Identification and Authentication) families, specifically AC-2 (Account Management), AC-6 (Least Privilege), IA-2 (Identification and Authentication for Organizational Users), and IA-5 (Authenticator Management). In CSF 2.0, these map to the PROTECT (PR) function, PR.AA (Identity Management, Authentication, and Access Control).

The vulnerability management findings under Annex A 8.8 (185 critical and 391 high findings, zero fixes, manual scan schedule) map to NIST 800-53 RA-5 (Vulnerability Monitoring and Scanning), SI-2 (Flaw Remediation), and CM-8 (System Component Inventory). In CSF 2.0, these fall within IDENTIFY (ID.AM) and PROTECT (PR.PS, Platform Security).

The monitoring and logging findings under Annex A 8.15 and 8.16 map to NIST 800-53 AU (Audit and Accountability) family controls including AU-2 (Event Logging), AU-6 (Audit Record Review, Analysis, and Reporting), and SI-4 (System Monitoring). In CSF 2.0, these fall within the DETECT (DE) function, DE.CM (Continuous Monitoring).

The Clause 9 and Clause 10 gaps regarding absence of internal audits, management review, and corrective action procedures correspond to NIST 800-53 CA (Assessment, Authorization, and Monitoring) family controls including CA-2 (Control Assessments), CA-5 (Plan of Action and Milestones), and PM-9 (Risk Management Strategy). In CSF 2.0, these align with GOVERN (GV.OC) and IDENTIFY (ID.IM, Improvement).

---

*End of Report*

**Document Classification: CONFIDENTIAL**  
**Assessment Conducted: March 5, 2026**  
**Organization: ABC Corp | Microsoft Entra ID Tenant**

---

> **Portfolio Note:** This report demonstrates applied GRC methodology including ISO/IEC 27001:2022 clause-by-clause gap assessment, Annex A control rating, nonconformity register construction, certification readiness analysis, and NIST 800-53 / CSF crosswalk mapping. All findings are based on real telemetry from a live environment. Metrics such as the 59% MFA rate, 185 critical vulnerabilities, and 83,600 single-factor sign-ins are actual values collected during the assessment engagement.
