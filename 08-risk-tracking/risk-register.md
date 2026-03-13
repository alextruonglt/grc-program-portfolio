# Risk Register

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity and Access Governance Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> **Disclaimer:** This is a sanitized version of a Risk Register produced following a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This risk register was built entirely from evidence gathered during a real, read-only gap assessment of a live Microsoft Azure shared tenant. Every risk entry cites actual telemetry counts, Tenable scan results, and Entra ID exports gathered at the time of assessment. This is not a template -- it is a formal risk register produced from documented deficiencies in a production environment.

**Driving Finding:** RA gap -- no formal risk register existed prior to this assessment. NIST SP 800-53 Risk Assessment (RA) control family rated Gap Rating 1 (Not Implemented). NIST CSF 2.0 Identify (ID) function rated Tier 1 (Partial). 1,344 open vulnerability findings existed with no corresponding risk owners or treatment decisions.

**Assessment Standards Applied:** NIST SP 800-53 Rev 5 | NIST CSF 2.0 | ISO/IEC 27001:2022 | SOC 2 TSC

---

## Scoring Methodology

### Likelihood Scale (1 to 5)

| Score | Label | Definition |
|---|---|---|
| 1 | Rare | Unlikely to occur. No historical precedent in this environment. |
| 2 | Unlikely | Could occur but not expected. Limited indicators present. |
| 3 | Possible | May occur. Some indicators or preconditions present. |
| 4 | Likely | Expected to occur. Strong indicators or active findings present. |
| 5 | Almost Certain | Currently occurring or near-certain based on evidence. |

### Impact Scale (1 to 5)

| Score | Label | Definition |
|---|---|---|
| 1 | Negligible | Minimal impact on operations, data, or users. |
| 2 | Minor | Limited impact, recoverable without significant disruption. |
| 3 | Moderate | Measurable impact on availability, integrity, or confidentiality. |
| 4 | Major | Significant disruption, data loss, or reputational damage. |
| 5 | Critical | Severe, potentially irreversible impact on the environment or its users. |

### Risk Score Calculation

**Inherent Risk Score = Likelihood x Impact (before controls applied)**
**Residual Risk Score = Likelihood x Impact (after existing controls applied)**

### Risk Score Bands

| Score Range | Risk Rating |
|---|---|
| 20 to 25 | Critical |
| 12 to 19 | High |
| 6 to 11 | Medium |
| 1 to 5 | Low |

### Treatment Decisions

| Decision | Definition |
|---|---|
| Mitigate | Implement or improve controls to reduce likelihood or impact |
| Accept | Formally accept the residual risk with documented justification |
| Transfer | Shift risk to a third party (e.g., insurance, contractual obligation) |
| Avoid | Eliminate the activity or asset that creates the risk |

---

## Risk Register

### RISK-001: Unauthorized Access via Single-Factor Authentication

| Field | Detail |
|---|---|
| Risk ID | RISK-001 |
| Risk Category | Identity and Access Management |
| Asset | Cloud identity platform, all user accounts |
| Threat | Credential compromise, account takeover, brute-force attack |
| Vulnerability | 41% of sign-ins (83,600 of 203,906) completed on single-factor authentication. Zero Conditional Access policies enforced. CA portal was inaccessible during assessment. |
| Evidence Source (real data) | SIEM SigninLogs: MFA rate 59%, Single-factor 83,600. CA notApplied: 203,906. CA Enforced: 0. |
| Likelihood (Inherent) | 5 -- Almost Certain |
| Impact (Inherent) | 5 -- Critical |
| Inherent Risk Score | 25 -- Critical |
| Current Controls | Security Defaults enabled (provides baseline MFA prompts for some scenarios) |
| Control Effectiveness | Low -- 41% of sign-ins still completing without MFA confirms insufficient coverage |
| Likelihood (Residual) | 4 |
| Impact (Residual) | 5 |
| Residual Risk Score | 20 -- Critical |
| Risk Owner | Identity Admin |
| Treatment Decision | Mitigate |
| Remediation Action | Configure and enforce Conditional Access policy requiring MFA for all users. Block legacy authentication. Validate via SigninLogs post-deployment. |
| POA&M Reference | POA-001 |
| Target Date | 2026-03-20 |
| Status | Open |

---

### RISK-002: Permanent Global Administrator Accounts Without PIM

| Field | Detail |
|---|---|
| Risk ID | RISK-002 |
| Risk Category | Privileged Access Management |
| Asset | 5 Global Administrator accounts, entire cloud tenant |
| Threat | Privileged account compromise, insider threat, lateral movement to tenant-wide control |
| Vulnerability | 5 of 8 privileged accounts hold permanent Global Administrator assignments. PIM not configured. Zero eligible assignments. Any compromise grants unrestricted tenant-wide control indefinitely. |
| Evidence Source (real data) | Entra ID export: GA count 5, PIM Eligible 0, PIM Active (permanent) 5, PIM configured No. |
| Likelihood (Inherent) | 4 -- Likely |
| Impact (Inherent) | 5 -- Critical |
| Inherent Risk Score | 20 -- Critical |
| Current Controls | Security Defaults enabled. Mixed role assignment method (direct and group). |
| Control Effectiveness | Low -- Security Defaults do not restrict standing privileged access. No JIT controls in place. |
| Likelihood (Residual) | 3 |
| Impact (Residual) | 5 |
| Residual Risk Score | 15 -- High |
| Risk Owner | Identity Admin |
| Treatment Decision | Mitigate |
| Remediation Action | Reduce GA count to maximum 2. Evaluate P2 licensing for PIM. Convert remaining privileged accounts to eligible-only assignments. |
| POA&M Reference | POA-002 |
| Target Date | 2026-04-05 |
| Status | Open |

---

### RISK-003: Critical and High Severity Vulnerabilities Unremediated

| Field | Detail |
|---|---|
| Risk ID | RISK-003 |
| Risk Category | Vulnerability Management |
| Asset | 126 unique assets across the environment |
| Threat | Remote exploitation, privilege escalation, ransomware, data exfiltration |
| Vulnerability | 185 critical and 391 high severity findings across 126 assets. Zero findings remediated. All 805 new findings remain open. Scan schedule ad hoc with no defined cadence. |
| Evidence Source (real data) | Tenable export: Critical 185, High 391, Medium 491, Low 277. Fixed 0. New 805. Schedule: Ad Hoc/Manual. Last scan: 2/25/26. |
| Likelihood (Inherent) | 5 -- Almost Certain |
| Impact (Inherent) | 5 -- Critical |
| Inherent Risk Score | 25 -- Critical |
| Current Controls | Authenticated vulnerability scanning active. |
| Control Effectiveness | Low -- Scanning identifies vulnerabilities but zero remediation means exposure is not being reduced. |
| Likelihood (Residual) | 4 |
| Impact (Residual) | 5 |
| Residual Risk Score | 20 -- Critical |
| Risk Owner | Vulnerability Management |
| Treatment Decision | Mitigate |
| Remediation Action | Establish SLAs: Critical 15 days, High 30 days, Medium 90 days, Low 180 days. Begin Critical finding triage immediately. Transition to weekly scheduled scans. |
| POA&M Reference | POA-003 |
| Target Date | 2026-03-21 |
| Status | Open |

---

### RISK-004: Unknown Endpoint Protection Posture

| Field | Detail |
|---|---|
| Risk ID | RISK-004 |
| Risk Category | Endpoint Security |
| Asset | All endpoints connected to the environment (126 identified assets) |
| Threat | Malware infection, ransomware, persistent threat tooling |
| Vulnerability | Endpoint protection status table in SIEM returned no data. Protection posture across all assets entirely unknown. |
| Evidence Source (real data) | SIEM ProtectionStatus query: No data returned. Total endpoints: No data. Protected: No data. |
| Likelihood (Inherent) | 4 -- Likely |
| Impact (Inherent) | 5 -- Critical |
| Inherent Risk Score | 20 -- Critical |
| Current Controls | None confirmed. No endpoint protection telemetry available. |
| Control Effectiveness | Unknown -- Cannot assess without telemetry. |
| Likelihood (Residual) | 4 |
| Impact (Residual) | 5 |
| Residual Risk Score | 20 -- Critical |
| Risk Owner | Endpoint Admin |
| Treatment Decision | Mitigate |
| Remediation Action | Deploy endpoint protection on all 126 assets. Verify SIEM telemetry populates within 7 days. Report coverage as monthly metric. |
| POA&M Reference | POA-004 |
| Target Date | 2026-04-05 |
| Status | Open |

---

### RISK-005: High-Volume Uninvestigated Security Alerts

| Field | Detail |
|---|---|
| Risk ID | RISK-005 |
| Risk Category | Security Operations / Detection |
| Asset | SIEM, all monitored systems |
| Threat | Active threat actor operating undetected, malicious PowerShell execution, persistence |
| Vulnerability | Top security alert is PowerShell encoded command execution with 24,905 occurrences. No documented triage or investigation evidenced. 50 total alert types present with no formal review cadence. |
| Evidence Source (real data) | SIEM SecurityAlert: PowershellEncoded count 24,905. Total alert types 50. Severity: Medium, High, Informational. |
| Likelihood (Inherent) | 5 -- Almost Certain |
| Impact (Inherent) | 4 -- Major |
| Inherent Risk Score | 20 -- Critical |
| Current Controls | SIEM generating and categorizing alerts. SecurityIncident table active. |
| Control Effectiveness | Partial -- Detection active but signals not converted to actionable response. |
| Likelihood (Residual) | 4 |
| Impact (Residual) | 4 |
| Residual Risk Score | 16 -- High |
| Risk Owner | SOC Analyst |
| Treatment Decision | Mitigate |
| Remediation Action | Investigate and classify 24,905 PowerShell alerts. Determine root cause. Implement suppression rules or automated playbook. Establish weekly alert review cadence. |
| POA&M Reference | POA-005, POA-013 |
| Target Date | 2026-03-20 |
| Status | Open |

---

### RISK-006: No OS-Level Configuration Baseline

| Field | Detail |
|---|---|
| Risk ID | RISK-006 |
| Risk Category | Configuration Management |
| Asset | All endpoints and servers |
| Threat | Configuration drift, unauthorized service enablement, persistence via misconfiguration |
| Vulnerability | SIEM SecurityBaseline table returned no data. DISA-STIG scan never executed (status: Empty). No baseline pass rates available for any OS type. |
| Evidence Source (real data) | SIEM SecurityBaseline query: No data returned. DISA-STIG scan status: Empty -- never executed. |
| Likelihood (Inherent) | 4 -- Likely |
| Impact (Inherent) | 4 -- Major |
| Inherent Risk Score | 16 -- High |
| Current Controls | Authenticated vulnerability scanning provides partial configuration visibility. |
| Control Effectiveness | Partial -- Does not replace OS-level baseline monitoring. |
| Likelihood (Residual) | 3 |
| Impact (Residual) | 4 |
| Residual Risk Score | 12 -- High |
| Risk Owner | Cloud Admin |
| Treatment Decision | Mitigate |
| Remediation Action | Enable security baseline agent on all endpoints. Execute DISA-STIG scan. Document pass rates per OS. Target 90% pass rate. |
| POA&M Reference | POA-006 |
| Target Date | 2026-04-10 |
| Status | Open |

---

### RISK-007: No User Attribute Data Preventing Access Reviews

| Field | Detail |
|---|---|
| Risk ID | RISK-007 |
| Risk Category | Identity Governance |
| Asset | 2,315 user accounts |
| Threat | Excessive access, orphaned accounts, unauthorized privilege accumulation |
| Vulnerability | 100% of 2,315 user accounts have a blank Department field. Role-based or attribute-based access reviews cannot be conducted. |
| Evidence Source (real data) | Entra ID export: Accounts with blank Department = 100% (2,315 of 2,315). |
| Likelihood (Inherent) | 4 -- Likely |
| Impact (Inherent) | 3 -- Moderate |
| Inherent Risk Score | 12 -- High |
| Current Controls | Centralized account management platform in place. Minimal guest accounts. |
| Control Effectiveness | Low -- Platform exists but attribute data gap prevents governance from being exercised. |
| Likelihood (Residual) | 3 |
| Impact (Residual) | 3 |
| Residual Risk Score | 9 -- Medium |
| Risk Owner | Identity Admin |
| Treatment Decision | Mitigate |
| Remediation Action | Launch attribute hygiene campaign. Populate Department, Title, Manager for all users. Enforce mandatory fields for new accounts. |
| POA&M Reference | POA-007 |
| Target Date | 2026-05-15 |
| Status | Open |

---

### RISK-008: Potential Credential Stuffing or Brute Force Activity

| Field | Detail |
|---|---|
| Risk ID | RISK-008 |
| Risk Category | Identity Threat Detection |
| Asset | 30 specific user accounts, potentially broader tenant |
| Threat | Credential stuffing, brute-force attack, account takeover |
| Vulnerability | 30 accounts recorded more than 5 failed sign-in attempts. Top account logged 512 failures from an external domain. No automated lockout policy or investigation trigger evidenced. |
| Evidence Source (real data) | SIEM SigninLogs: 30 accounts with >5 failed sign-ins. Top account: [sanitized external domain] with 512 failures. |
| Likelihood (Inherent) | 5 -- Almost Certain |
| Impact (Inherent) | 4 -- Major |
| Inherent Risk Score | 20 -- Critical |
| Current Controls | Security Defaults enabled. AuditLogs capturing failed sign-in events. |
| Control Effectiveness | Low -- Security Defaults did not prevent 512 repeated failures against a single account. No lockout triggered. |
| Likelihood (Residual) | 4 |
| Impact (Residual) | 4 |
| Residual Risk Score | 16 -- High |
| Risk Owner | Identity Admin |
| Treatment Decision | Mitigate |
| Remediation Action | Investigate all 30 accounts with excessive failures. Implement account lockout policy. Escalate 512-failure account for immediate review. |
| POA&M Reference | POA-008 |
| Target Date | 2026-03-20 |
| Status | Open |

---

### RISK-009: No Formal Incident Response Capability

| Field | Detail |
|---|---|
| Risk ID | RISK-009 |
| Risk Category | Incident Response |
| Asset | Entire environment and all users |
| Threat | Uncontrolled incident escalation, delayed containment, regulatory exposure |
| Vulnerability | No formal Incident Response Plan exists. SecurityIncident records are present but no triage procedures, escalation paths, or response SLAs are documented. |
| Evidence Source (real data) | Documentation review: IR plan = Not found. SIEM SecurityIncident statuses present: New, Active, Closed, Deleted. |
| Likelihood (Inherent) | 3 -- Possible |
| Impact (Inherent) | 5 -- Critical |
| Inherent Risk Score | 15 -- High |
| Current Controls | SIEM generating incidents. Named cybersecurity owner present. |
| Control Effectiveness | Low -- Detection exists but without a response process incidents cannot be managed structurally. |
| Likelihood (Residual) | 3 |
| Impact (Residual) | 4 |
| Residual Risk Score | 12 -- High |
| Risk Owner | GRC Analyst |
| Treatment Decision | Mitigate |
| Remediation Action | Develop and approve formal IR plan covering severity definitions, team roles, triage, containment, recovery, post-incident review, and escalation matrix. |
| POA&M Reference | POA-009 |
| Target Date | 2026-04-15 |
| Status | Open |

---

### RISK-010: No Formal Risk Management Process

| Field | Detail |
|---|---|
| Risk ID | RISK-010 |
| Risk Category | Governance and Risk Management |
| Asset | Security program and all dependent systems |
| Threat | Unidentified risks accumulating, uninformed resource allocation, regulatory noncompliance |
| Vulnerability | No formal risk management process existed prior to this assessment. 1,344 open findings had no risk owners or treatment decisions. Risks communicated informally. |
| Evidence Source (real data) | Governance review: Risk register = No. Risk management process = No. 1,344 total open Tenable findings. |
| Likelihood (Inherent) | 4 -- Likely |
| Impact (Inherent) | 4 -- Major |
| Inherent Risk Score | 16 -- High |
| Current Controls | Named cybersecurity owner. Cybersecurity policy exists. Risks communicated informally. |
| Control Effectiveness | Low -- Informal risk communication without a structured process does not constitute risk management. |
| Likelihood (Residual) | 3 |
| Impact (Residual) | 3 |
| Residual Risk Score | 9 -- Medium |
| Risk Owner | GRC Analyst |
| Treatment Decision | Mitigate |
| Remediation Action | Adopt this risk register as the active risk management tool. Document risk methodology aligned to ISO 27005 or NIST SP 800-39. |
| POA&M Reference | POA-010, POA-015 |
| Target Date | 2026-04-30 |
| Status | Open |

---

### RISK-011: No Internal Audit or Management Review Process

| Field | Detail |
|---|---|
| Risk ID | RISK-011 |
| Risk Category | Governance and Compliance |
| Asset | Information security program |
| Threat | Undetected control failures, regulatory nonconformity, program stagnation |
| Vulnerability | No internal audit program. No management review of the security program documented. No nonconformity or corrective action process in place. |
| Evidence Source (real data) | Documentation review: Internal audit = No. Management review = No. Nonconformity process = No. |
| Likelihood (Inherent) | 4 -- Likely |
| Impact (Inherent) | 3 -- Moderate |
| Inherent Risk Score | 12 -- High |
| Current Controls | Gap assessment conducted (this assessment is the first evidence of structured review). |
| Control Effectiveness | Low -- One-time ad hoc assessment without a repeating cycle is insufficient. |
| Likelihood (Residual) | 3 |
| Impact (Residual) | 3 |
| Residual Risk Score | 9 -- Medium |
| Risk Owner | GRC Analyst |
| Treatment Decision | Mitigate |
| Remediation Action | Develop internal audit plan. Schedule first audit within 90 days. Establish annual management review cadence with documented minutes. |
| POA&M Reference | POA-011 |
| Target Date | 2026-06-05 |
| Status | Open |

---

### RISK-012: Unmanaged Third-Party Cloud Service Providers

| Field | Detail |
|---|---|
| Risk ID | RISK-012 |
| Risk Category | Third-Party and Supply Chain Risk |
| Asset | Data processed by four known cloud and SaaS services |
| Threat | Third-party data breach, supply chain compromise, service disruption |
| Vulnerability | Four cloud and SaaS services in active use with no formal inventory, no contractual security requirements, and no monitoring process. |
| Evidence Source (real data) | Governance review: Third-party inventory = No. Supplier requirements in contracts = No. Monitoring = No. Known services: Microsoft 365, Azure, Tenable, and one additional SaaS platform. |
| Likelihood (Inherent) | 3 -- Possible |
| Impact (Inherent) | 4 -- Major |
| Inherent Risk Score | 12 -- High |
| Current Controls | Primary cloud providers maintain ISO 27001 and SOC 2 certifications. |
| Control Effectiveness | Partial -- Provider certifications provide assurance but cannot substitute for contractual obligations. |
| Likelihood (Residual) | 2 |
| Impact (Residual) | 4 |
| Residual Risk Score | 8 -- Medium |
| Risk Owner | GRC Analyst |
| Treatment Decision | Mitigate |
| Remediation Action | Create formal third-party inventory. Review agreements for security terms. Establish minimum requirements per provider. Implement annual review. |
| POA&M Reference | POA-012 |
| Target Date | 2026-05-01 |
| Status | Open |

---

### RISK-013: Insufficient Log Review Process

| Field | Detail |
|---|---|
| Risk ID | RISK-013 |
| Risk Category | Audit and Accountability |
| Asset | SIEM log data and audit trail |
| Threat | Failure to detect breaches, inability to support forensic investigation |
| Vulnerability | 30+ days of audit log data present with no review cadence, no alert thresholds, no retention policy, and 24,905 high-volume alerts generating no documented review. |
| Evidence Source (real data) | SIEM AuditLogs: 30+ days of data. No review procedure. Log retention policy: Not documented. |
| Likelihood (Inherent) | 4 -- Likely |
| Impact (Inherent) | 3 -- Moderate |
| Inherent Risk Score | 12 -- High |
| Current Controls | SIEM collecting log data. SecurityAlert table active. |
| Control Effectiveness | Partial -- Collection functioning but without review, logs only provide forensic value after an incident. |
| Likelihood (Residual) | 3 |
| Impact (Residual) | 3 |
| Residual Risk Score | 9 -- Medium |
| Risk Owner | SOC Analyst |
| Treatment Decision | Mitigate |
| Remediation Action | Establish weekly audit log review cadence. Document retention policy (minimum 90 days online). Create automated alerting rules for high-frequency alert types. |
| POA&M Reference | POA-013 |
| Target Date | 2026-04-15 |
| Status | Open |

---

### RISK-014: No System Security Plan or Formal Authorization Boundary

| Field | Detail |
|---|---|
| Risk ID | RISK-014 |
| Risk Category | Planning and Authorization |
| Asset | Entire system boundary |
| Threat | Undefined scope, unmanaged systems, unassigned responsibility |
| Vulnerability | No SSP exists. Security architecture and boundaries are documented but not codified into an SSP. System has not been formally authorized. |
| Evidence Source (real data) | Documentation review: SSP = No. ISMS scope document = No. Security architecture = Yes. Boundaries = Yes. |
| Likelihood (Inherent) | 3 -- Possible |
| Impact (Inherent) | 3 -- Moderate |
| Inherent Risk Score | 9 -- Medium |
| Current Controls | Security architecture documented. System boundaries defined. Named cybersecurity owner. |
| Control Effectiveness | Partial -- Architecture exists informally but without SSP structure cannot anchor formal authorization. |
| Likelihood (Residual) | 2 |
| Impact (Residual) | 3 |
| Residual Risk Score | 6 -- Medium |
| Risk Owner | GRC Analyst |
| Treatment Decision | Mitigate |
| Remediation Action | Develop SSP using existing architecture and boundary documentation. Map implemented controls to NIST 800-53 families. |
| POA&M Reference | POA-014 |
| Target Date | 2026-06-30 |
| Status | Open |

---

### RISK-015: Unvalidated Backup and Recovery Capability

| Field | Detail |
|---|---|
| Risk ID | RISK-015 |
| Risk Category | Business Continuity and Recovery |
| Asset | All systems enrolled in cloud backup |
| Threat | Unrecoverable data loss following ransomware, accidental deletion, or system failure |
| Vulnerability | Cloud backup enrolled but no RTO or RPO documented, no recovery plan, no testing performed. A backup that has never been tested cannot be relied upon. |
| Evidence Source (real data) | Governance review: Backup enrolled = Yes. RTO/RPO = No. Recovery plan tested = No. |
| Likelihood (Inherent) | 3 -- Possible |
| Impact (Inherent) | 4 -- Major |
| Inherent Risk Score | 12 -- High |
| Current Controls | Cloud backup enrolled. Geo-redundant storage by default. |
| Control Effectiveness | Partial -- Infrastructure exists but actual recoverability is unvalidated. |
| Likelihood (Residual) | 2 |
| Impact (Residual) | 4 |
| Residual Risk Score | 8 -- Medium |
| Risk Owner | IT / Cloud Admin |
| Treatment Decision | Mitigate |
| Remediation Action | Define and document RTO and RPO targets. Develop recovery plan. Conduct at least one restoration test annually. |
| POA&M Reference | POA-019 |
| Target Date | 2026-06-15 |
| Status | Open |

---

### RISK-016: No Security Awareness Training Completion Tracking

| Field | Detail |
|---|---|
| Risk ID | RISK-016 |
| Risk Category | Human Risk / Security Culture |
| Asset | All users |
| Threat | Phishing, social engineering, credential compromise from untrained users |
| Vulnerability | Security awareness training program exists but completion is not tracked. No evidence that users have received training. |
| Evidence Source (real data) | Governance review: Training program = Yes. Completion tracked = No. |
| Likelihood (Inherent) | 3 -- Possible |
| Impact (Inherent) | 3 -- Moderate |
| Inherent Risk Score | 9 -- Medium |
| Current Controls | Security awareness training program in place. |
| Control Effectiveness | Partial -- Program exists but coverage and effectiveness cannot be demonstrated. |
| Likelihood (Residual) | 2 |
| Impact (Residual) | 3 |
| Residual Risk Score | 6 -- Medium |
| Risk Owner | HR / GRC |
| Treatment Decision | Mitigate |
| Remediation Action | Implement completion tracking. Define requirements by role. Set 90% annual completion target. Report to leadership quarterly. |
| POA&M Reference | POA-018 |
| Target Date | 2026-05-15 |
| Status | Open |

---

### RISK-017: No Data Classification Policy

| Field | Detail |
|---|---|
| Risk ID | RISK-017 |
| Risk Category | Data Protection |
| Asset | All data within the environment |
| Threat | Unintended data exposure, regulatory noncompliance, inadequate DLP controls |
| Vulnerability | No data classification policy exists. DLP controls cannot be appropriately designed or enforced without a classification framework. |
| Evidence Source (real data) | Governance review: Data classification policy = Unknown. No DLP configuration observed. |
| Likelihood (Inherent) | 3 -- Possible |
| Impact (Inherent) | 3 -- Moderate |
| Inherent Risk Score | 9 -- Medium |
| Current Controls | None confirmed specific to data classification. |
| Control Effectiveness | None -- Cannot assess data protection without a classification framework. |
| Likelihood (Residual) | 3 |
| Impact (Residual) | 3 |
| Residual Risk Score | 9 -- Medium |
| Risk Owner | GRC Analyst |
| Treatment Decision | Mitigate |
| Remediation Action | Establish data classification policy (Confidential, Internal, Public). Deploy information protection controls. Define DLP policies based on classification outcomes. |
| POA&M Reference | POA-020 |
| Target Date | 2026-07-01 |
| Status | Open |

---

## Risk Summary Dashboard

### Inherent Risk Distribution

| Risk Rating | Count |
|---|---|
| Critical (20 to 25) | 5 |
| High (12 to 19) | 7 |
| Medium (6 to 11) | 5 |
| Low (1 to 5) | 0 |

### Residual Risk Distribution (After Existing Controls)

| Risk Rating | Count |
|---|---|
| Critical (20 to 25) | 3 |
| High (12 to 19) | 4 |
| Medium (6 to 11) | 8 |
| Low (1 to 5) | 2 |

### Framework Alignment

| Risk ID | NIST 800-53 | NIST CSF 2.0 | ISO 27001 | SOC 2 TSC |
|---|---|---|---|---|
| RISK-001 | IA-2, IA-2(1) | PR.AA | A 8.5 | CC6.1, CC6.3 |
| RISK-002 | AC-6, IA-2 | PR.AA | A 8.2 | CC6.2, CC6.3 |
| RISK-003 | SI-2, RA-5 | PR.PS, ID.RA | A 8.8 | CC7.1 |
| RISK-004 | SI-3 | PR.IR | A 8.7 | CC6.8 |
| RISK-005 | AU-6, IR-4 | DE.AE | A 8.16 | CC7.2, CC7.3 |
| RISK-006 | CM-2, CM-6 | PR.PS | A 8.9 | CC7.1 |
| RISK-007 | AC-2, AC-6 | ID.AM | A 8.3 | CC6.2 |
| RISK-008 | AC-2, AU-6 | PR.AA | A 8.5 | CC6.1, CC6.7 |
| RISK-009 | IR-8, IR-4 | RS.MA | A 5.26 | CC7.3, CC7.4 |
| RISK-010 | RA-3, PM-9 | GV.RM | Clause 6.1.2 | CC3.2 |
| RISK-011 | CA-2, CA-7 | GV.OV | Clause 9.2 | CC4.1 |
| RISK-012 | SA-9, SR-3 | GV.SC | A 5.19, A 5.20 | CC9.2 |
| RISK-013 | AU-2, AU-6 | DE.CM | A 8.15 | CC7.2 |
| RISK-014 | PL-2, CA-9 | GV.PO | Clause 4.3 | CC2.1 |
| RISK-015 | CP-2, CP-9 | RC.RP | A 8.13 | A1.2 |
| RISK-016 | AT-2, PM-13 | PR.AT | A 6.3 | CC2.2 |
| RISK-017 | SC-28, RA-2 | PR.DS | A 8.12 | CC6.7 |

---

*End of Document*

*Organization: ABC Corp | Microsoft Entra ID Tenant. This risk register was produced from a real gap assessment conducted against a live Azure cloud environment. All risk entries cite actual telemetry counts and findings from that environment. All organization names and identifiers have been sanitized. Published for portfolio and educational purposes only.*
