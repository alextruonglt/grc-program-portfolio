# NIST Cybersecurity Framework 2.0 Assessment Report

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity & Access Governance Analyst
**Audit Date:** March 5, 2026
**Environment:** Shared Azure Tenant | ~1,000+ Users
**Classification:** CONFIDENTIAL

---

> ⚠️ **Disclaimer:** This is a sanitized version of a NIST CSF 2.0 assessment conducted on a **live production Azure environment**. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This report is published for portfolio and educational purposes only.
>
> **This assessment was performed on a real, active environment**, not a simulated lab dataset. The findings, metrics, and tier ratings reflect actual telemetry, scan results, and configuration data collected on March 5, 2026.

---

## Table of Contents
1. Executive Summary
2. Scope and Methodology
3. CSF Function Summary Table
4. Detailed Function Assessments
5. CSF Profile: Current vs. Target
6. Crosswalk Note

---

## 1. Executive Summary

ABC Corp, operating within a shared Microsoft Azure tenant serving approximately 1,000+ users, was assessed against all six functions of the NIST Cybersecurity Framework 2.0 (CSF 2.0). This assessment was conducted on a read-only basis, relying on documentation review, log analysis, vulnerability scan data, and Microsoft Entra ID exports.

The environment presents a **Tier 1: Partial** overall maturity posture. Foundational elements exist, including a named cybersecurity owner, a documented information security policy, an active security awareness training program, and enrolled cloud backup, but consistent implementation, formal risk management, and enforced technical controls are absent across all six functions.

**Overall Tier Rating: Tier 1: Partial**

| Function | Tier |
|---|---|
| GV: Govern | Tier 1: Partial |
| ID: Identify | Tier 1: Partial |
| PR: Protect | Tier 1: Partial |
| DE: Detect | Tier 2: Risk Informed |
| RS: Respond | Tier 1: Partial |
| RC: Recover | Tier 1: Partial |

**Top 3 Critical Risks:**

1. **Zero Conditional Access enforcement across 203,906 sign-ins.** The Conditional Access portal returned a 401 error during assessment, no policies were confirmed active, and every single recorded sign-in was logged as `notApplied`. With 41% of sign-ins (83,600) completing on single-factor authentication, the identity perimeter is functionally unprotected.

2. **576 open vulnerability findings rated Critical or High, with zero remediated.** The most recent authenticated scan identified 185 critical and 391 high severity vulnerabilities across 126 unique assets. No remediation activity has been recorded, and the DISA-STIG compliance scan has never been executed.

3. **Five Global Administrators holding permanent active assignments with no PIM.** All privileged role assignments are permanent, Privileged Identity Management (PIM) is not configured, and no just-in-time access controls exist. Any compromise of a Global Administrator account would grant unrestricted tenant-wide access.

**Single Most Important Action:** Immediately configure and enforce at least one baseline Conditional Access policy requiring Multi-Factor Authentication for all users and blocking legacy authentication protocols. This single control would directly reduce the attack surface exposed by the 83,600 single-factor sign-ins and partially mitigate the Global Administrator standing-access risk.

---

## 2. Scope and Methodology

### 2.1 Assessment Scope

This assessment evaluated ABC Corp's Azure tenant environment against all six functions of the NIST Cybersecurity Framework 2.0. The scope included:

- Microsoft Entra ID configuration and identity controls
- Microsoft Sentinel / Log Analytics Workspace telemetry and alerting
- Tenable vulnerability management scan results
- Governance documentation and policy artifacts
- Azure Backup configuration
- Security awareness training program documentation

### 2.2 Assessment Standard

**NIST Cybersecurity Framework 2.0 (CSF 2.0)**, released February 2024, All six functions were assessed:

| Function | Code | Description |
|---|---|---|
| Govern | GV | Establish and monitor the organization's cybersecurity risk management strategy, expectations, and policy |
| Identify | ID | Understand the organization's current cybersecurity risks |
| Protect | PR | Use safeguards to prevent or reduce cybersecurity risks |
| Detect | DE | Find and analyze possible cybersecurity attacks and compromises |
| Respond | RS | Take action regarding a detected cybersecurity incident |
| Recover | RC | Restore assets and operations affected by a cybersecurity incident |

### 2.3 Tier Scale

| Tier | Label | Definition |
|---|---|---|
| Tier 1 | Partial | Cybersecurity risk management is ad hoc. Limited awareness. |
| Tier 2 | Risk Informed | Practices exist but not formalized or consistently applied. |
| Tier 3 | Repeatable | Formally approved policies. Consistently implemented. |
| Tier 4 | Adaptive | Continuously improved. Risk-informed decisions at org level. |

### 2.4 Tools and Data Sources

- **Tenable:** Authenticated vulnerability scans; DISA-STIG compliance data
- **Microsoft Log Analytics Workspace (LAW):** AuditLogs, SecurityAlert, SecurityIncident tables
- **Microsoft Entra ID:** User exports, role assignments, PIM configuration, MFA sign-in data
- **Conditional Access Portal:** Returned HTTP 401; policy data unavailable during assessment
- **Governance Documentation:** Policies, strategy documents, training records
- **Azure Portal:** Backup configuration, subscription overview

### 2.5 Assessment Constraints

- **Read-only access:** No write permissions were held. No changes were made to any configuration during assessment.
- **Conditional Access portal 401:** CA policy count and configuration could not be directly verified. All CA findings are inferred from sign-in log metadata (`notApplied` on all 203,906 sign-ins).
- **No Entra ID P1/P2 licenses:** Confirmed absence of Privileged Identity Management, advanced CA features, and Identity Protection.
- **Shared tenant environment:** Findings reflect the posture of the shared environment.
- **No SecurityBaseline or ProtectionStatus data:** LAW tables for OS baseline compliance and endpoint protection returned no data.

---

## 3. CSF Function Summary Table

| Function | Tier | Tier Label | Key Categories Assessed | Headline Finding |
|---|---|---|---|---|
| GV: Govern | 1 | Partial | GV.OC, GV.RM, GV.RR, GV.PO, GV.OV, GV.SC | Named cybersecurity owner and information security policy exist, but no formal risk management process, no risk register, no third-party supplier program, and no cybersecurity budget documentation are in place. |
| ID: Identify | 1 | Partial | ID.AM, ID.RA, ID.IM | Vulnerability scanner provides asset inventory for 126 assets; however, 100% of 2,315 Entra accounts have blank Department fields, no risk register is maintained, and the DISA-STIG compliance scan has never been executed. |
| PR: Protect | 1 | Partial | PR.AA, PR.AT, PR.DS, PR.PS, PR.IR | MFA is applied to only 59% of sign-ins; Conditional Access enforcement is 0 across all 203,906 sign-ins; five Global Administrators hold permanent assignments with no PIM; 576 Critical/High findings remain unmediated. |
| DE: Detect | 2 | Risk Informed | DE.CM, DE.AE | 30+ days of AuditLogs, 50 alert types including 24,905 PowerShell-encoded command alerts, and active SecurityIncident tracking demonstrate monitoring capability; SecurityBaseline and ProtectionStatus data are absent. |
| RS: Respond | 1 | Partial | RS.MA, RS.AN, RS.CO, RS.MI | SecurityIncident records exist in New, Active, Closed, and Deleted states; however, no formal Incident Response plan was identified and no structured alert triage documentation was provided. |
| RC: Recover | 1 | Partial | RC.RP, RC.CO | Azure Backup is enrolled; RTO/RPO have not been documented and no recovery plan testing has been performed. |

---

## 4. Detailed Function Assessments

*Functions are presented lowest tier first, with DE (Tier 2) assessed last.*

---

### 4.1 GV: Govern | Tier 1: Partial

#### Current Tier Rationale

ABC Corp demonstrates nascent governance activity: a named cybersecurity owner exists, an information security policy has been produced, security roles are documented, and there is evidence of security oversight. However, governance is ad hoc. No formal risk management process exists, no risk register is maintained, cybersecurity budgeting evidence is absent, and the organization's third-party provider landscape has not been inventoried. The absence of an ISMS scope document, Statement of Applicability, internal audit program, and management review process confirms a Tier 1 posture.

#### Evidence Mapping

| CSF Category | Evidence | Finding |
|---|---|---|
| GV.OC: Organizational Context | Named cybersecurity owner: Yes. Cybersecurity policy: Yes. Risks communicated to leadership: Yes. | Basic organizational context exists. Not formally documented in an ISMS scope or SOA. |
| GV.RM: Risk Management Strategy | Formal risk management process: No. Risk register: No. Cybersecurity budgeting evidence: No. | No structured risk management strategy. Risk decisions appear ad hoc. |
| GV.RR: Roles, Responsibilities, Authorities | Security roles documented: Yes. Security oversight evidence: Yes. | Roles exist on paper. No formal accountability framework identified. |
| GV.PO: Policy | Information security policy: Yes. Risk management strategy: No. System Security Plan: No. ISMS scope: No. SOA: No. | Single policy artifact exists. Policy ecosystem is incomplete. |
| GV.OV: Oversight | Internal audit program: No. Management review of ISMS: No. Nonconformity process: No. | No evidence of cyclical governance review or corrective action process. |
| GV.SC: Supply Chain Risk Management | Third-party providers inventoried: No. Supplier security requirements: No. Third-party monitoring: No. Known services: Microsoft 365, Azure, Tenable, additional SaaS. | Multiple known cloud/SaaS providers with no formal supply chain risk program. |

#### Key Gaps

- **GV.RM-01 / GV.RM-02:** No formal risk management process and no risk register.
- **GV.SC-01 through GV.SC-03:** No third-party inventory, no contractual security requirements, no supplier monitoring.
- **GV.OV-01 / GV.OV-02:** No internal audit program and no management review cycle.
- **GV.PO-02:** System Security Plan and ISMS scope are absent.

#### Recommendations

1. Establish a formal risk management process and create a risk register capturing the top identified risks, risk owners, and treatment decisions.
2. Conduct a third-party inventory of all cloud and SaaS providers and establish minimum security requirements.
3. Implement a quarterly management review cycle to assess control effectiveness and track nonconformities.
4. Develop a System Security Plan and ISMS scope document.

---

### 4.2 ID: Identify | Tier 1: Partial

#### Current Tier Rationale

Asset identification is partially addressed through authenticated vulnerability scanning of 126 assets, but this constitutes the entirety of formal inventory. No asset management system exists outside the vulnerability scanner. Identity asset data is severely degraded: 100% of the 2,315 Entra accounts have blank Department fields, preventing classification by business function or risk tier. No risk register exists, and no formal improvement process for identification practices has been documented.

#### Evidence Mapping

| CSF Category | Evidence | Finding |
|---|---|---|
| ID.AM: Asset Management | 126 unique assets in vulnerability scanner. No formal asset inventory outside scanner. 2,315 Entra users, 100% with blank Department fields. | Partial technical asset coverage. Identity assets are unclassified. |
| ID.RA: Risk Assessment | Risk register: No. 185 Critical, 391 High, 491 Medium, 277 Low vulnerability findings. Fixed findings: 0. | Vulnerability data exists but not translated into a risk register or treatment plan. |
| ID.IM: Improvement | No formal improvement process. Scan schedule: Ad hoc/manual. DISA-STIG scan status: Empty (never executed). | No continuous improvement cycle for identification practices. |

#### Key Gaps

- **ID.AM-01 / ID.AM-02:** No formal asset inventory beyond vulnerability scanner. Identity assets lack department classification.
- **ID.AM-07:** Privileged accounts lack classification. Five of eight privileged accounts are Global Administrators with permanent assignments.
- **ID.RA-01:** No risk register to capture or prioritize 576 Critical/High vulnerability findings.
- **ID.IM-01:** DISA-STIG compliance scan has never been executed. Zero compliance results available.

#### Recommendations

1. Populate the Department field for all 2,315 Entra accounts.
2. Create a formal asset inventory including cloud resources, endpoints, user identities, and SaaS services.
3. Translate vulnerability findings into a risk register, prioritizing 185 Critical and 391 High findings.
4. Execute the DISA-STIG compliance scan on a recurring cadence.

---

### 4.3 RS: Respond | Tier 1: Partial

#### Current Tier Rationale

SecurityIncident records are present with incidents in New, Active, Closed, and Deleted states, indicating some level of triage activity. However, no formal Incident Response plan was identified. No structured alert triage process, escalation paths, or communication procedures are documented. The presence of 24,905 PowerShell-encoded command alerts without evidence of systematic investigation demonstrates that detection capability has not been translated into a repeatable response process.

#### Evidence Mapping

| CSF Category | Evidence | Finding |
|---|---|---|
| RS.MA: Incident Management | SecurityIncident data: Yes. States: New, Active, Closed, Deleted. | Incidents are created and transitioned. No formal process documented. |
| RS.AN: Incident Analysis | 24,905 PowerShell-encoded command alerts. 50 unique alert types. Severity: Medium, High, Informational. | Alert volume without documented analysis procedures suggests alert fatigue risk. |
| RS.CO: Incident Response Reporting and Communication | No IR plan identified. No escalation paths documented. | No formal communication procedures for incident notification. |
| RS.MI: Incident Mitigation | Fixed vulnerability findings: 0. No mitigation tracking evidence. | No documented evidence of vulnerability or incident mitigation activity. |

#### Key Gaps

- **RS.MA-01:** No formal Incident Response plan has been created.
- **RS.AN-03:** 24,905 PowerShell-encoded alerts without documented triage procedures.
- **RS.CO-02:** No escalation or communication procedures for stakeholder notification.
- **RS.MI-01:** Zero findings have been marked as fixed, indicating no closed-loop remediation process.

#### Recommendations

1. Develop and approve a formal Incident Response plan.
2. Establish documented triage procedures and severity-based escalation thresholds for the 50 active alert types.
3. Investigate and classify the 24,905 PowerShell-encoded command alerts.
4. Implement a remediation tracking workflow to begin closing Critical and High vulnerability findings.

---

### 4.4 RC: Recover | Tier 1: Partial

#### Current Tier Rationale

Azure Backup is enrolled, representing a foundational recovery control. However, no RTO or RPO has been documented, no recovery plan exists, and no recovery plan testing has been performed. In the event of a disruptive incident, the organization would have no agreed-upon standard against which to measure restoration success.

#### Evidence Mapping

| CSF Category | Evidence | Finding |
|---|---|---|
| RC.RP: Recovery Planning | Azure Backup enrolled: Yes. RTO/RPO documented: No. Recovery plan tested: No. | Backup infrastructure exists but recovery planning documentation is absent. |
| RC.CO: Recovery Communication | No recovery communication plan identified. | No procedures for communicating restoration status during a recovery event. |

#### Key Gaps

- **RC.RP-01:** No formal recovery plan has been created.
- **RC.RP-02:** RTO and RPO have not been defined for any system or service.
- **RC.RP-03:** Recovery plan testing has never been conducted. Backup integrity is unvalidated.
- **RC.CO-03:** No communication plan for recovery status updates.

#### Recommendations

1. Document RTO and RPO for each system covered by Azure Backup.
2. Develop a formal recovery plan referencing Azure Backup procedures and restoration sequencing.
3. Conduct a tabletop exercise and at least one technical restoration test.
4. Define a recovery communication template for notifying stakeholders of service disruption.

---

### 4.5 PR: Protect | Tier 1: Partial

#### Current Tier Rationale

The Protect function exhibits the most acute technical risk of all six functions. MFA is applied to only 59% of sign-ins (120,306 of 203,906), leaving 83,600 sign-ins completed on single-factor authentication. Sign-in log metadata confirms zero enforced Conditional Access policies across all 203,906 sign-ins. Five Global Administrators hold permanent active role assignments with no PIM and no just-in-time access. The vulnerability scanner identifies 185 critical and 391 high findings across 126 assets, with zero remediations recorded. Security Defaults are enabled, which provides a baseline MFA prompt, but Security Defaults cannot substitute for Conditional Access enforcement.

#### Evidence Mapping

| CSF Category | Evidence | Finding |
|---|---|---|
| PR.AA: Identity Management and Access Control | MFA rate: 59% (120,306 / 203,906). CA notApplied: 203,906. CA Enforced: 0. Global Admins: 5 permanent. PIM: Not configured. Accounts with >5 failed sign-ins: 30. Top failing account: 512 failures. | Identity access controls are critically deficient. |
| PR.AT: Awareness and Training | Security awareness training: Yes. Completion tracked: No. | Training exists but completion is unverified. |
| PR.DS: Data Security | Data classification policy: Unknown. Password expiration policy: Not found. Password complexity: Not found. | No data protection baseline established. Password policy posture unknown. |
| PR.PS: Platform Security | SecurityBaseline data: No data. DISA-STIG scan: Never executed. OS baseline pass rates: No data. | No platform security baseline data available. |
| PR.IR: Technology Infrastructure Resilience | ProtectionStatus data: No data. Total endpoints: No data. Protected/Unprotected: No data. | Endpoint protection posture entirely unknown. |

#### Key Gaps

- **PR.AA-01 / PR.AA-05:** Zero Conditional Access policies enforced. 83,600 active single-factor sign-ins.
- **PR.AA-02:** Five Global Administrators with permanent standing access. No PIM configured.
- **PR.AA-08:** 30 accounts with >5 failed sign-ins; one account reached 512 failed sign-ins.
- **PR.PS-01:** No OS baseline compliance data. DISA-STIG scan never executed.
- **PR.IR-01:** Endpoint protection status entirely unknown.

#### Recommendations

1. Configure and enforce a Conditional Access policy requiring MFA for all users. This is the single highest-priority action identified in this assessment.
2. Configure Conditional Access to block legacy authentication protocols.
3. Reduce Global Administrator count to a maximum of 2 and enable PIM for all privileged roles.
4. Investigate the account with 512 failed sign-ins and the 29 other accounts with >5 failures.
5. Execute the DISA-STIG compliance scan and establish a remediation cadence for Critical and High findings.
6. Deploy endpoint protection tooling and integrate ProtectionStatus data into the Log Analytics Workspace.

---

### 4.6 DE: Detect | Tier 2: Risk Informed

#### Current Tier Rationale

The Detect function is the strongest of the six, achieving Tier 2. The environment has 30+ days of AuditLogs with a meaningful range of operation types. SecurityAlert data is present with 50 distinct alert types. SecurityIncident tracking is operational. However, the absence of SecurityBaseline and ProtectionStatus data represents a significant detection coverage gap. The volume of 24,905 PowerShell-encoded command alerts without documented triage procedures suggests detection capability is informally managed.

#### Evidence Mapping

| CSF Category | Evidence | Finding |
|---|---|---|
| DE.CM: Continuous Monitoring | AuditLogs: 30+ days. SecurityAlert: 50 types, top alert (PowerShell-encoded): 24,905 occurrences. SecurityBaseline: No data. ProtectionStatus: No data. | Core identity telemetry is present. Endpoint and baseline coverage is absent. |
| DE.AE: Adverse Event Analysis | SecurityIncident: Present (New, Active, Closed, Deleted). 30 accounts with >5 failed sign-ins. Severity levels: Medium, High, Informational. | Incident records and failed sign-in patterns are detectable. No documented analytical playbooks identified. |

#### Key Gaps

- **DE.CM-01:** SecurityBaseline data is absent.
- **DE.CM-06:** ProtectionStatus data is absent.
- **DE.AE-02:** 24,905 PowerShell-encoded alerts without documented analysis procedures.

#### Recommendations

1. Onboard SecurityBaseline data to the Log Analytics Workspace.
2. Integrate endpoint protection telemetry into the LAW.
3. Develop documented detection playbooks for top alert types, beginning with PowerShell-encoded command alerts.
4. Configure automated alert triage rules to reduce analyst burden from high-volume alerts.

---

## 5. CSF Profile: Current vs. Target

| Function | Current Tier | Current Label | Target Tier (12-Month) | Target Label | Gap Summary | Priority Actions to Close Gap |
|---|---|---|---|---|---|---|
| GV: Govern | 1 | Partial | 2 | Risk Informed | No risk management process, no risk register, no third-party program, no governance review cycle | (1) Create risk register; (2) inventory third-party providers; (3) establish quarterly management review |
| ID: Identify | 1 | Partial | 2 | Risk Informed | No formal asset inventory, 100% blank Department fields, no risk register, DISA-STIG never run | (1) Populate Department fields; (2) execute DISA-STIG scan; (3) translate findings into risk register |
| PR: Protect | 1 | Partial | 2 | Risk Informed | Zero CA enforcement, 41% single-factor sign-ins, 5 permanent GAs, no PIM, 576 Critical/High unmediated | (1) Enforce CA/MFA policy; (2) reduce GA count and enable PIM; (3) begin remediation cadence |
| DE: Detect | 2 | Risk Informed | 3 | Repeatable | Missing SecurityBaseline and ProtectionStatus data, no documented triage playbooks | (1) Onboard SecurityBaseline; (2) integrate ProtectionStatus; (3) document alert playbooks |
| RS: Respond | 1 | Partial | 2 | Risk Informed | No IR plan, no documented triage procedures, no communication protocols, zero remediation tracking | (1) Draft and approve IR plan; (2) document triage procedures; (3) establish remediation workflow |
| RC: Recover | 1 | Partial | 2 | Risk Informed | No RTO/RPO documented, no recovery plan, no Azure Backup testing | (1) Document RTO/RPO; (2) develop recovery plan; (3) conduct restoration test |

---

## 6. Crosswalk Note

The findings documented in this CSF 2.0 assessment map directly to control families within NIST Special Publication 800-53 Revision 5 and to Annex A of ISO/IEC 27001:2022, demonstrating the interconnected nature of these widely adopted frameworks.

The most critical finding, specifically zero Conditional Access enforcement across 203,906 sign-ins and 59% MFA coverage, maps to **NIST 800-53 IA (Identification and Authentication)**, particularly IA-2 (Identification and Authentication for Organizational Users), IA-2(1) (MFA to Privileged Accounts), and IA-2(2) (MFA to Non-Privileged Accounts). Under ISO 27001:2022, this maps to **Annex A 8.5 (Secure Authentication)** and **A 5.17 (Authentication Information)**.

The governance gaps under GV.RM and GV.PO, including no formal risk management process, no risk register, and no ISMS scope, correspond to **NIST 800-53 PM (Program Management)** and **RA (Risk Assessment)** families, including PM-9 (Risk Management Strategy), RA-2 (Security Categorization), and RA-3 (Risk Assessment). Under ISO 27001, these gaps impair conformance with **Clause 6.1 (Actions to Address Risks and Opportunities)** and **Clause 6.1.2 (Information Security Risk Assessment)**, both mandatory certification requirements.

Supply chain deficiencies under GV.SC, including no third-party inventory, no contractual requirements, and no supplier monitoring, map to **NIST 800-53 SR (Supply Chain Risk Management)**, including SR-3 (Supply Chain Controls and Processes) and SR-6 (Supplier Assessments and Reviews), and to **ISO 27001 Annex A 5.19 (Information Security in Supplier Relationships)** and **A 5.20 (Addressing Information Security Within Supplier Agreements)**.

The vulnerability management posture, with 576 unmediated Critical/High findings and zero fixed findings, maps to **NIST 800-53 SI-2 (Flaw Remediation)**, **SI-3 (Malicious Code Protection)**, and **RA-5 (Vulnerability Monitoring and Scanning)**. The ISO 27001 equivalent is **Annex A 8.8 (Management of Technical Vulnerabilities)**. The absence of DISA-STIG results weakens alignment with **NIST 800-53 CM-6 (Configuration Settings)** and **ISO 27001 A 8.9 (Configuration Management)**.

Recovery gaps, specifically the undocumented RTO/RPO and untested Azure Backup, align with **NIST 800-53 CP (Contingency Planning)**, including CP-2 (Contingency Plan), CP-4 (Contingency Plan Testing), and CP-9 (System Backup), and with **ISO 27001 Annex A 5.30 (ICT Readiness for Business Continuity)** and **A 8.13 (Information Backup)**.

Addressing the Conditional Access and PIM gaps, for example, would simultaneously satisfy CSF PR.AA, NIST 800-53 IA-2 through IA-5, and ISO 27001 A 8.5, demonstrating the efficiency of framework-aligned remediation planning.

---

*Assessment Standard: NIST Cybersecurity Framework 2.0 (February 2024)*
*Audit Date: March 5, 2026*
*This report reflects conditions observed in a live production environment at the time of assessment. The assessor held read-only access; no changes were made to any system or configuration.*
*Classification: CONFIDENTIAL: Sanitized for Portfolio Publication*
