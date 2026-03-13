# NIST SP 800-53 Rev 5 Gap Assessment Report
## ABC Corp: Microsoft Azure Tenant

> ⚠️ **Disclaimer:** This is a sanitized version of a NIST SP 800-53 Rev 5 Gap Assessment conducted on a **live production Microsoft Azure environment**. All organization names, account names, domain references, and identifying details have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. The findings, data, and KQL queries reflect a **real assessment performed on an active, production-grade environment**: not a simulation or synthetic dataset. This report is published for portfolio and educational purposes only.

---

| Field | Detail |
|---|---|
| **Organization** | ABC Corp: Microsoft Entra ID Tenant |
| **Assessment Standard** | NIST SP 800-53 Revision 5 |
| **Prepared by** | Alex Truong: Identity & Access Governance Analyst |
| **Audit Date** | March 2, 2026 |
| **Classification** | CONFIDENTIAL |
| **Environment** | Shared Microsoft Azure Tenant (~1,000+ Users) |

---

## About This Report

This assessment was performed on a **live, active Azure environment**: not a lab simulation. The environment was a shared Microsoft Azure tenant with over 1,000 users, running real workloads with real identity and vulnerability data. The assessor operated in a **read-only capacity only**: no changes were made to the environment during or after the assessment. All findings are based on observable evidence gathered through Azure Log Analytics Workspace KQL queries, Microsoft Entra ID review, Tenable.io vulnerability scan data, and governance documentation review.

The data in this report (vulnerability counts, MFA rates, alert volumes, failed sign-in counts) are **real numbers from the live environment**, sanitized only for organizational identity.

---

## 1. Executive Summary

This report presents the findings of a NIST SP 800-53 Revision 5 gap assessment conducted against the ABC Corp Azure tenant, a shared Microsoft Azure environment hosting approximately 1,000 or more users. The assessment was performed in a read-only capacity using Azure Log Analytics Workspace (KQL queries), Microsoft Entra ID, Tenable vulnerability management, and governance documentation review. Twelve control families were evaluated; seven additional families were excluded due to the cloud-only, read-only nature of the environment.

Overall, the environment presents a **high-risk security posture**. Of the 12 families assessed, 7 received a gap rating of 1 (Not Implemented) or 2 (Partially Implemented), yielding a **non-compliance rate of approximately 58%**. Only 5 families achieved a rating of 3 or above.

**Top Three Findings:**

1. **Identity & Access Management (IA), Gap Rating 1:** Multi-factor authentication coverage stands at only 59% (120,306 of 203,906 sign-ins). Conditional Access is entirely non-enforced; all 203,906 sign-ins show a `notApplied` CA status with zero enforced. Five of eight privileged accounts hold permanent Global Administrator assignments with no Privileged Identity Management (PIM) configured. This represents an immediate, critical risk in a shared multi-user environment.

2. **Vulnerability Management / System & Information Integrity (SI), Gap Rating 1:** Tenable authenticated scans reveal 185 critical, 391 high, and 491 medium findings across 126 assets. A DISA-STIG compliance scan exists in configuration only: its status is "Empty" with zero results executed. Zero findings have been remediated, and scanning is ad hoc with no schedule.

3. **Audit & Accountability (AU), Gap Rating 2:** While 30-plus days of audit logs are present and operations such as password resets, user additions, and service principal deletions are captured, the top security alert is PowerShell encoded command execution with 24,905 occurrences. There is no evidence of formal log review, alerting thresholds, or audit reduction tooling.

**Most Important Immediate Action:** Enable and enforce Conditional Access policies requiring MFA for all users, particularly the 8 privileged accounts holding permanent role assignments, and activate PIM to eliminate standing privilege.

---

## 2. Scope and Methodology

### 2.1 Environment Description

The ABC Corp environment is a shared Microsoft Azure tenant hosting approximately 1,000 or more active user accounts, administered through Microsoft Entra ID, Azure Monitor / Log Analytics Workspace, Microsoft Defender for Cloud, and Tenable.io for vulnerability scanning.

### 2.2 Assessment Standard

This assessment maps findings to **NIST Special Publication 800-53 Revision 5** control families. Each family is assigned a gap rating of 1 through 4 as defined in Section 2.5 below.

### 2.3 Families Assessed (12)

| Family | Abbreviation | Basis for Inclusion |
|---|---|---|
| Access Control | AC | Entra ID role assignments, CA policy review |
| Audit and Accountability | AU | Log Analytics KQL (AuditLogs, SigninLogs) |
| Assessment, Authorization, and Monitoring | CA | Governance documentation, Defender for Cloud |
| Configuration Management | CM | SecurityBaseline KQL, Tenable STIG data |
| Identification and Authentication | IA | Entra ID MFA, PIM, password policy review |
| Incident Response | IR | SecurityAlert, SecurityIncident KQL queries |
| Risk Assessment | RA | Tenable findings, governance documentation |
| System and Services Acquisition | SA | Governance documentation review |
| System and Communications Protection | SC | Tenable network findings, CA/TLS posture |
| System and Information Integrity | SI | Tenable authenticated scan, ProtectionStatus KQL |
| Program Management | PM | Governance documentation, budgeting evidence |
| Planning | PL | SSP, security architecture, system boundaries |

### 2.4 Families Excluded (7)

| Family | Abbreviation | Justification for Exclusion |
|---|---|---|
| Awareness and Training | AT | Requires LMS/training platform data; not accessible in read-only cloud assessment |
| Contingency Planning | CP | RTO/RPO documentation and recovery plan testing require operational access beyond scope |
| Maintenance | MA | Physical/remote maintenance records require infrastructure-level access unavailable here |
| Media Protection | MP | Requires physical media inventory; not applicable to cloud-only environment |
| Physical and Environmental Protection | PE | Cloud-only environment. Physical controls are delegated to Microsoft and are not assessable. |
| Personnel Security | PS | HR records, background check data, and termination procedures are outside scope |
| Personally Identifiable Information Processing | PT | PII processing records and data subject inventory not available in read-only access |
| Supply Chain Risk Management | SR | Supplier contracts and third-party assessment records not available for review |

### 2.5 Gap Rating Scale

| Rating | Label | Definition |
|---|---|---|
| 1 | Not Implemented | No evidence the control exists |
| 2 | Partially Implemented | Control exists but has significant gaps |
| 3 | Implemented with Gaps | Broadly in place; minor gaps or documentation missing |
| 4 | Implemented | Full or near-full implementation with evidence |

### 2.6 Tools and Data Sources

- **Microsoft Azure Log Analytics Workspace:** KQL queries L1 through L8 (AuditLogs, SecurityAlert, SecurityIncident, SecurityBaseline, ProtectionStatus, SigninLogs)
- **Microsoft Entra ID:** User inventory, role assignments, PIM status, Security Defaults, Conditional Access portal
- **Tenable.io:** Authenticated vulnerability scan, DISA-STIG compliance scan review
- **Governance Documentation Review:** Policy inventory, SSP status, risk register, training program, backup configuration

### 2.7 Assessor Constraints

The following constraints were documented and affect findings:

- **Read-only access:** No changes were made to the environment.
- **Conditional Access portal returned HTTP 401:** CA policy details could not be retrieved directly. Evidence was obtained via SigninLogs `ConditionalAccessStatus` field.
- **No Entra ID P1/P2 license:** PIM, advanced CA features, and Identity Protection are unavailable by license. This is a root-cause factor for multiple findings.
- **Shared tenant:** Over 1,000 user accounts share the tenant, amplifying risk for any identity or access control gap.
- **PIM export absent:** PIM eligible and active assignments returned zero results, confirming no PIM configuration.

---

## 3. Summary Findings Table

| Control Family | Abbrev | Controls Reviewed | Gap Rating | Label | Priority | Key Gap |
|---|---|---|---|---|---|---|
| Identification and Authentication | IA | IA-2, IA-5, IA-8, IA-12 | **1** | Not Implemented | Critical | 41% of sign-ins without MFA; CA not enforced; 5 permanent GAs; no PIM |
| System and Information Integrity | SI | SI-2, SI-3, SI-7 | **1** | Not Implemented | Critical | 185 critical / 391 high vulns; STIG scan empty; 0 fixes; no endpoint protection data |
| Configuration Management | CM | CM-2, CM-6, CM-7 | **1** | Not Implemented | Critical | SecurityBaseline returns no data; DISA-STIG scan not executed |
| Risk Assessment | RA | RA-3, RA-5, RA-7 | **2** | Partially Implemented | High | Tenable scans exist but are ad hoc; no risk register; no formal RA process |
| Access Control | AC | AC-2, AC-3, AC-6, AC-17 | **2** | Partially Implemented | High | 100% of accounts missing Department; no RBAC hygiene; CA not enforced |
| Audit and Accountability | AU | AU-2, AU-3, AU-6, AU-9 | **2** | Partially Implemented | High | Logs present 30+ days; 24,905 PowerShell alerts; no evidence of formal review |
| Incident Response | IR | IR-4, IR-5, IR-6, IR-8 | **2** | Partially Implemented | High | Alerts and incidents exist; no documented IR plan; no formal triage evidence |
| System and Communications Protection | SC | SC-5, SC-7, SC-8 | **2** | Partially Implemented | Medium | High/Critical network findings in Tenable; CA not enforcing access controls |
| Assessment, Authorization, and Monitoring | CA | CA-2, CA-7, CA-9 | **2** | Partially Implemented | Medium | No SSP; no formal authorization; Tenable provides partial CA-7 evidence |
| System and Services Acquisition | SA | SA-3, SA-8, SA-9 | **2** | Partially Implemented | Medium | No third-party inventory; no supplier security requirements in contracts |
| Planning | PL | PL-1, PL-2, PL-8 | **3** | Implemented with Gaps | Medium | Security architecture documented; system boundaries defined; no formal SSP |
| Program Management | PM | PM-1, PM-2, PM-9, PM-10 | **3** | Implemented with Gaps | Medium | Named owner; policy exists; no budget evidence; no formal risk management process |

---

## 4. Detailed Findings: All 12 Families

### 4.1 Identification and Authentication (IA) (Gap Rating 1): Not Implemented

**Controls Reviewed:** IA-2, IA-5, IA-8, IA-12

**Evidence of Implementation:**
- Security Defaults are enabled in Microsoft Entra ID.
- Entra ID provides a centralized identity platform for 2,315 users.
- Eight accounts hold privileged roles.

**Identified Gaps:**

- **MFA coverage is critically insufficient.** Of 203,906 total sign-ins, only 120,306 (59%) used multi-factor authentication, leaving 83,600 sign-ins (41%) single-factor.
- **Conditional Access is entirely non-enforced.** All 203,906 sign-ins show `notApplied` CA status: zero enforced, zero failed.
- **Five Global Administrator accounts exist as permanent active assignments** with no PIM configuration and no just-in-time access mechanism.
- **No Entra ID P1/P2 license**: PIM, Identity Protection, and advanced CA are unavailable.
- **Password policies could not be located.**
- **100% of user accounts have a blank Department field**, undermining identity governance.

**Risk Statement:**
The combination of 41% single-factor sign-ins, zero enforced Conditional Access, and five permanent Global Administrator accounts creates a critical identity risk. A single compromised credential could pivot to privileged access with no adaptive authentication barrier.

**Recommendations:**
1. Immediately enable Conditional Access policy requiring MFA for all users.
2. Upgrade to Entra ID P1 or P2 to enable PIM and advanced CA features.
3. Convert all five Global Administrator permanent assignments to PIM eligible-only.
4. Establish an authenticator management policy documenting password complexity and expiration.
5. Implement user attribute hygiene to populate Department and organizational fields for all 2,315 accounts.

---

### 4.2 System and Information Integrity (SI) (Gap Rating 1): Not Implemented

**Controls Reviewed:** SI-2, SI-3, SI-7

**Evidence of Implementation:**
- Tenable.io authenticated scans are operational across 126 assets.
- A DISA-STIG compliance scan configuration exists in Tenable.
- SecurityAlert data is present with 50 distinct alert types.

**Identified Gaps:**

- **1,344 total findings with zero remediated.** 185 critical, 391 high, 491 medium, and 277 low findings: all 805 new findings remain open.
- **DISA-STIG compliance scan status is "Empty"**: the template exists but has never been executed.
- **Scanning is ad hoc with no schedule.**
- **ProtectionStatus table returned no data**: endpoint malicious code protection status is completely unknown.
- **Top security alert (PowerShell encoded commands) occurred 24,905 times** with no documented response.

**Risk Statement:**
With 185 critical and 391 high vulnerabilities open, zero remediations, no endpoint protection visibility, and near-25,000 PowerShell encoding alerts, the environment's integrity posture is unmanaged.

**Recommendations:**
1. Execute the DISA-STIG compliance scan and document baseline results.
2. Establish a patch SLA: critical within 15 days, high within 30 days.
3. Enable Microsoft Defender for Endpoint on all 126 assets.
4. Investigate and triage the 24,905 PowerShell encoded command alerts.
5. Transition Tenable to a scheduled weekly cadence.

---

### 4.3 Configuration Management (CM) (Gap Rating 1): Not Implemented

**Controls Reviewed:** CM-2, CM-6, CM-7

**Evidence of Implementation:**
- Tenable provides partial asset discovery and configuration awareness.
- DISA-STIG scan configuration exists in Tenable.

**Identified Gaps:**

- **SecurityBaseline returned no data**: OS-level baseline compliance is unknown.
- **SecurityBaselineSummary returned no data.**
- **DISA-STIG scan has never executed**: no compliance results available.
- **No formal asset inventory** outside Tenable exists.
- **No documented hardening standards or least-functionality policy.**

**Risk Statement:**
Without a configuration baseline, the organization cannot detect unauthorized system modifications, unneeded services, or configuration drift: enabling adversary persistence.

**Recommendations:**
1. Enable Defender for Cloud security baseline agent to populate SecurityBaseline data.
2. Execute the DISA-STIG scan and establish baseline OS metrics.
3. Develop configuration baseline standards per OS type.
4. Implement Azure Policy for configuration drift detection.
5. Establish a CMDB reconciling Tenable-discovered assets against Entra ID registered devices.

---

### 4.4 Risk Assessment (RA) (Gap Rating 2): Partially Implemented

**Controls Reviewed:** RA-3, RA-5, RA-7

**Evidence of Implementation:**
- Tenable authenticated scans cover 126 assets with quantitative vulnerability data.
- Cybersecurity policy and strategy exist; risks are communicated to leadership.

**Identified Gaps:**

- **No formal risk management process**: no documented methodology, acceptance criteria, or treatment workflow.
- **No risk register**: 1,344 open findings have no corresponding risk register entries or risk owners.
- **Scanning is ad hoc** with no defined frequency.
- **Zero findings remediated**: risk response is non-functional.
- **No cybersecurity budgeting evidence.**
- **RTO/RPO not documented**; recovery plans not tested.

**Recommendations:**
1. Establish a risk register capturing finding ID, risk owner, treatment decision, and target remediation date.
2. Transition to scheduled weekly vulnerability scanning.
3. Document a formal risk management process.
4. Assign risk owners to all Critical and High findings.
5. Document RTO/RPO targets aligned with Azure Backup enrollment.

---

### 4.5 Access Control (AC) (Gap Rating 2): Partially Implemented

**Controls Reviewed:** AC-2, AC-3, AC-6, AC-17

**Evidence of Implementation:**
- Entra ID provides centralized account management for 2,315 users.
- Eight accounts hold privileged roles; 2 guests with no privileged roles.
- Security Defaults provide baseline enforcement.

**Identified Gaps:**

- **100% of accounts have a blank Department field**: no basis for attribute-based access reviews.
- **Conditional Access not enforced for any sign-in**: 203,906 `notApplied`, 0 enforced.
- **Five permanent Global Administrator assignments** violate least privilege; no access review evidence.
- **No password expiration or complexity policy** was located.
- **No data classification policy**: access control decisions cannot be mapped to data sensitivity.
- **30 accounts with >5 failed sign-ins**: top account: [hashed domain]: 512 failures.

**Recommendations:**
1. Enforce Conditional Access requiring MFA and blocking legacy authentication.
2. Populate Department, Job Title, and Manager for all 2,315 users.
3. Conduct formal access review of all 8 privileged accounts.
4. Implement account lockout policy and investigate the 30 high-failure accounts.
5. Develop a data classification policy.

---

### 4.6 Audit and Accountability (AU) (Gap Rating 2): Partially Implemented

**Controls Reviewed:** AU-2, AU-3, AU-6, AU-9

**Evidence of Implementation:**
- AuditLogs present with 30+ days of history.
- Operations captured: Update user, Add service principal, Remove service principal, Hard delete service principal, Update PasswordProfile, Add member to group, Reset user password, Add user.
- SecurityAlert: 50 distinct alert types; SecurityIncident: New, Active, Closed, Deleted statuses.

**Identified Gaps:**

- **24,905 PowerShell encoded command alerts** with no documented investigation.
- **No formal audit log review process**: no alert thresholds, review workflows, or escalation procedures.
- **30 accounts with >5 failed sign-ins visible in data** but no formal response.
- **No SIEM playbooks or automated alerting rules** documented.
- **Log retention policy beyond 30+ days is not documented.**

**Recommendations:**
1. Establish weekly audit log review cadence with documented outcomes.
2. Create automated alerting rules for PowerShell encoded execution and similar high-frequency threats.
3. Document log retention policy (recommend minimum 90 days online, 1 year archived).
4. Triage the 30 high-failure accounts and implement lockout policy.
5. Implement a dashboard or scheduled report for leadership security event summaries.

---

### 4.7 Incident Response (IR) (Gap Rating 2): Partially Implemented

**Controls Reviewed:** IR-4, IR-5, IR-6, IR-8

**Evidence of Implementation:**
- SecurityIncident data with New, Active, Closed, Deleted statuses.
- 50 SecurityAlert types including high-severity events.
- Named cybersecurity owner and oversight evidence.

**Identified Gaps:**

- **No documented Incident Response Plan** (IR-8).
- **No structured triage, escalation, or containment procedures** documented.
- **24,905 PowerShell alerts not converted to investigated incidents.**
- **Recovery plan not tested.**
- **No external incident reporting procedures** documented.

**Recommendations:**
1. Develop a formal IR Plan covering detection, analysis, containment, eradication, recovery, and post-incident review.
2. Define incident severity classifications with response SLAs (e.g., Critical: 1 hour, High: 4 hours).
3. Conduct a tabletop exercise simulating a compromised Global Administrator account.
4. Formally investigate and close or escalate the 24,905 PowerShell alerts.
5. Define external reporting procedures for incidents affecting users or stakeholders.

---

### 4.8 System and Communications Protection (SC) (Gap Rating 2): Partially Implemented

**Controls Reviewed:** SC-5, SC-7, SC-8

**Evidence of Implementation:**
- Azure platform-level DDoS and NSG controls provide baseline SC-5/SC-7.
- Microsoft 365 and Azure services use TLS in transit by default.
- Tenable findings provide SC-relevant vulnerability visibility.

**Identified Gaps:**

- **391 high and 185 critical Tenable findings** include unpatched network and communications vulnerabilities.
- **Conditional Access not enforcing network or device-compliance controls.**
- **No network segmentation documentation** beyond Azure defaults.
- **SC posture cannot be fully assessed**: CA portal returned HTTP 401, ProtectionStatus returned no data.

**Recommendations:**
1. Review Tenable findings for SC-relevant CVEs and prioritize remediation.
2. Implement CA policies enforcing compliant device and location requirements.
3. Document network architecture, NSG rulesets, and boundary protection controls.
4. Enable and resolve Defender for Cloud network security recommendations.
5. Verify TLS configurations for all externally accessible services.

---

### 4.9 Assessment, Authorization, and Monitoring (CA) (Gap Rating 2): Partially Implemented

**Controls Reviewed:** CA-2, CA-7, CA-9

**Evidence of Implementation:**
- Current assessment constitutes CA-2 activity.
- Tenable scanning and Log Analytics provide a CA-7 monitoring foundation.
- Security oversight evidence and named cybersecurity owner present.

**Identified Gaps:**

- **No System Security Plan**: no formal authorization to operate.
- **No ISMS scope document or Statement of Applicability.**
- **No internal audit program**: ad hoc assessment is insufficient for CA-2.
- **No management review of the ISMS** and no nonconformity process.
- **Continuous monitoring is partial**: no documented monitoring strategy, metrics, or reporting cadence.

**Recommendations:**
1. Develop a System Security Plan documenting boundaries, data types, controls, and responsible parties.
2. Establish a formal annual internal audit schedule.
3. Document a continuous monitoring strategy with metrics and review frequency.
4. Implement a nonconformity tracking process.
5. Schedule and document an annual management review of the security program.

---

### 4.10 System and Services Acquisition (SA) (Gap Rating 2): Partially Implemented

**Controls Reviewed:** SA-3, SA-8, SA-9

**Evidence of Implementation:**
- Known cloud/SaaS services identified: Microsoft 365, Azure, Tenable, and one additional platform.
- Security architecture documented; system boundaries defined.

**Identified Gaps:**

- **No formal third-party service inventory** with security requirements or risk ratings.
- **No supplier security requirements in contracts**: no right-to-audit clauses or incident notification terms.
- **No third-party monitoring process.**
- **No SDLC documentation.**

**Recommendations:**
1. Create a formal third-party service inventory with data shared, risk rating, and contract terms.
2. Review existing agreements for security terms; pursue amendments where feasible.
3. Establish annual third-party security reviews for top-risk providers.
4. Document an SDLC policy incorporating security at each phase.
5. Implement a pre-adoption security evaluation process for new third-party services.

---

### 4.11 Planning (PL) (Gap Rating 3): Implemented with Gaps

**Controls Reviewed:** PL-1, PL-2, PL-8

**Evidence of Implementation:**
- Information security policy exists.
- Security roles documented; security architecture documented.
- System boundaries defined; named cybersecurity owner present.

**Identified Gaps:**

- **No formal System Security Plan** despite existing architecture and boundary documentation.
- **No ISMS scope document or Statement of Applicability.**
- **Policy completeness not fully verifiable** in read-only assessment.

**Recommendations:**
1. Develop an SSP using existing architecture and boundary documents as inputs.
2. Formalize the ISMS scope document.
3. Establish a policy review cycle with documented approval and version control.
4. Develop a Statement of Applicability with exclusion justifications.
5. Ensure annual leadership review and formal acceptance of the SSP.

---

### 4.12 Program Management (PM) (Gap Rating 3): Implemented with Gaps

**Controls Reviewed:** PM-1, PM-2, PM-9, PM-10

**Evidence of Implementation:**
- Named cybersecurity owner in place.
- Cybersecurity policy and strategy exist.
- Risks communicated to leadership; security oversight evidence present.
- Security awareness training program exists.

**Identified Gaps:**

- **No formal risk management process** documented.
- **No cybersecurity budgeting evidence**: resource allocation cannot be demonstrated as risk-informed.
- **Training completion not tracked.**
- **No formal authorization process documentation.**

**Recommendations:**
1. Document a formal risk management process covering identification through review.
2. Establish and document a cybersecurity budget line item.
3. Implement training completion tracking with a defined annual completion target.
4. Develop a formal Program Management Plan documenting structure, roles, and governance.
5. Link the awareness program content to top threats identified in this assessment.

---

## 5. Remediation Roadmap

### 5.1 Immediate: 0 to 30 Days (Rating 1 Families)

| Action | Family | Effort |
|---|---|---|
| Enable and enforce CA policy requiring MFA for all sign-ins | IA | Low |
| Convert 5 permanent GA assignments to PIM eligible (pending P2 decision) | IA | Medium |
| Execute DISA-STIG compliance scan in Tenable; document baseline | SI / CM | Low |
| Enable Microsoft Defender for Endpoint on all 126 assets | SI | Medium |
| Investigate and triage 24,905 PowerShell encoded command alerts | SI / IR | Medium |
| Enable Defender for Cloud security baseline agent | CM | Low |
| Begin remediation of 185 Critical Tenable findings | SI | High |
| Evaluate Entra ID P1/P2 licensing | IA | Low (decision) |

### 5.2 Short-Term: 30 to 90 Days (Rating 2 Families)

| Action | Family | Effort |
|---|---|---|
| Develop formal Incident Response Plan | IR | Medium |
| Establish risk register for Critical and High Tenable findings | RA | Medium |
| Account attribute hygiene to populate Department/Title/Manager for 2,315 users | AC | High |
| Conduct formal access review of 8 privileged accounts | AC | Low |
| Investigate 30 accounts with >5 failed sign-ins; implement lockout policy | AC / AU | Low |
| Establish weekly audit log review cadence | AU | Low |
| Create automated alerting rules for high-frequency alert types | AU / IR | Medium |
| Create formal third-party service inventory | SA | Low |
| Define and enforce log retention policy | AU | Low |
| Document network architecture and boundary protection controls | SC | Medium |
| Transition Tenable to scheduled weekly scans | RA | Low |
| Conduct IR tabletop exercise | IR | Medium |

### 5.3 Ongoing: 90+ Days (Rating 3 Families and Program Maturation)

| Action | Family | Effort |
|---|---|---|
| Develop System Security Plan | PL / CA | High |
| Document formal risk management process | PM / RA | Medium |
| Establish ISMS scope document and Statement of Applicability | PL / CA | Medium |
| Implement annual internal audit schedule | CA | Medium |
| Document cybersecurity budget | PM | Low |
| Implement training completion tracking | PM | Low |
| Establish continuous monitoring strategy with metrics | CA | Medium |
| Conduct annual management review of security program | PM / CA | Low |
| Document RTO/RPO targets and test recovery plans | RA | Medium |

---

## 6. Appendix: KQL Queries Used

All queries were executed against the ABC Corp Log Analytics Workspace.

| Query Name | Purpose | Control Families Evidenced |
|---|---|---|
| L1: Audit Log Coverage | Quantifies daily event counts and unique operation types; establishes audit history depth | AU-2, AU-3 |
| L2: Security Alerts | Summarizes alert names, counts, and severities from SecurityAlert; identifies top threat indicators | IR-4, IR-5, SI-3 |
| L3: Security Incidents | Aggregates SecurityIncident records by status, severity, and classification | IR-4, IR-5, IR-6 |
| L4: Configuration Baseline | Queries SecurityBaseline for OS-level pass/fail rates; evidences configuration compliance | CM-2, CM-6 |
| L5: Endpoint Protection Status | Queries ProtectionStatus for endpoint count and protection type | SI-3, SI-7 |
| L6: MFA Rate | Calculates MFA vs. single-factor sign-in rates from SigninLogs | IA-2, IA-5 |
| L7: CA Enforcement Status | Counts ConditionalAccessStatus values (notApplied, success, failure) from SigninLogs | AC-3, AC-17, IA-2 |
| L8: Failed Sign-In Patterns | Identifies accounts with >5 failed sign-ins by user, application, and failure reason | AC-2, AU-6, IR-4 |

---

## Key Statistics at a Glance

| Metric | Value |
|---|---|
| Total Users | 2,315 |
| Privileged Accounts | 8 |
| Global Administrators (permanent) | 5 |
| Total Sign-Ins Analyzed | 203,906 |
| MFA Rate | 59% |
| CA Enforced Sign-Ins | 0 (0%) |
| Critical Vulnerabilities | 185 |
| High Vulnerabilities | 391 |
| Total Open Findings | 1,344 |
| Findings Remediated | 0 |
| Top Alert Volume | 24,905 (PowerShell encoded) |
| Accounts with >5 Failed Sign-ins | 30 |
| Families Rated 1 (Not Implemented) | 3 |
| Families Rated 2 (Partially Implemented) | 7 |
| Families Rated 3 (Implemented with Gaps) | 2 |

---

*End of Report*

*Assessment conducted in read-only capacity on a live production environment. No changes were made. All findings are based on data observable through the designated toolset at the time of assessment. Organization name and identifying details have been sanitized for public release.*
