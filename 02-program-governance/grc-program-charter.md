# GRC Program Charter

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Charter Date:** March 5, 2026
**Effective Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> ⚠️ **Disclaimer:** This is a sanitized version of a GRC Program Charter developed for a live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This GRC Program Charter is the capstone of a complete GRC program buildout conducted on a real, live Azure shared tenant assessed in March 2026. It was not written from a template. Every objective, KPI, risk item, deliverable reference, and governance structure in this charter traces to a confirmed finding or confirmed control gap in the live environment. The program started from a real gap assessment and built its governance structure from that reality.

**Confirmed Findings from the Live Environment That This Charter Addresses (real data):**
- Zero active Sentinel analytics rules at time of assessment. No detection layer existed. (real finding)
- 83,600 of 203,906 sign-in events completed on single-factor authentication. Highest-volume control gap in the program. (real finding)
- 100 percent of 2,315 user accounts had blank Department, Job Title, and Manager attributes. (real finding)
- ProtectionStatus table returned zero rows for all 126 assets. Endpoint telemetry entirely absent. (real finding)
- High and Critical alerts in SecurityAlert table with no evidence of triage or response. (real finding)
- No TPRM program existed. No DPAs documented. A Tier 1 SaaS vendor had agents on 126 production assets with no risk assessment on record. (real finding)
- No GRC program documentation of any kind existed at time of the initial March 2026 assessment. (real finding)
- This charter formalizes the authority and governance structure for 10 GRC deliverables produced during the initial engagement, all grounded in these real findings.

---


**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Charter Date:** March 5, 2026
**Effective Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Document Type:** Program Charter (Authorizing Document for the ABC Corp GRC Program)
**Review Cadence:** Annual, or following any significant organizational change, major incident, or material shift in regulatory obligations

> **Environment Context:** This GRC Program Charter was developed for and is grounded in the actual security posture of the ABC Corp Azure shared tenant, assessed in March 2026. The ABC Corp is a live production Azure environment serving approximately 1,000 or more students and instructors. At time of charter development, a structured multi-framework gap assessment was completed, ten or more formal GRC deliverables were produced, and a Plan of Action and Milestones was established to track remediation of confirmed control gaps. This charter formalizes the authority, structure, and commitments that give that body of work organizational force.

---

## Table of Contents

1. Executive Summary
2. Program Background and Impetus
3. Mission Statement
4. Program Scope and Boundaries
5. Governance Structure and Authority
6. Program Objectives and Strategic Goals
7. GRC Framework and Standards Alignment
8. Program Deliverables Inventory
9. Roles and Responsibilities (RACI)
10. Risk Management Approach
11. Compliance Obligations
12. Program Metrics and Key Performance Indicators
13. Resourcing and Budget Authority
14. Program Communication and Reporting Cadence
15. Program Lifecycle and Sustainability
16. Constraints, Assumptions, and Dependencies
17. Charter Approval and Signatures

---

## 1. Executive Summary

The ABC Corp GRC Program is hereby formally chartered as of March 5, 2026. This charter authorizes the program to establish, operate, and continuously improve the governance, risk management, and compliance function for the ABC Corp Azure cloud tenant environment.

The program was initiated in response to a structured multi-framework security gap assessment completed in March 2026 that identified material deficiencies across six control domains: identity governance, vulnerability management, endpoint protection telemetry, security monitoring, configuration baseline compliance, and third-party risk management. At time of assessment, zero analytics rules were active in Microsoft Sentinel, 83,600 of 203,906 sign-in events completed on single-factor authentication, 100 percent of 2,315 user accounts had blank mandatory identity attributes, the ProtectionStatus table was empty for all 126 assets, and no formal GRC program documentation existed.

This charter establishes the organizational authority for the GRC Analyst role, defines the program's scope across all ten deliverable domains produced during the initial engagement, mandates the framework alignment to NIST CSF 2.0, NIST SP 800-53 Rev 5, CIS Controls v8, ISO/IEC 27001:2022, and MITRE ATT&CK, and commits the program to a defined set of key performance indicators that will be tracked quarterly.

The program sponsor is the Security Lead. The program operator is the GRC Analyst. Charter approval constitutes organizational commitment to resource, support, and enforce the GRC program and its deliverables.

---

## 2. Program Background and Impetus

### 2.1 The ABC Corp Environment

The ABC Corp is a live production Microsoft Azure shared tenant used as an educational and professional development platform for approximately 1,000 or more students and instructors pursuing cybersecurity knowledge and certifications. The environment includes:

- Microsoft Entra ID tenant with 2,315 active user accounts
- Microsoft Sentinel workspace serving as the primary SIEM platform
- Microsoft Defender for Cloud and Defender for Endpoint for endpoint and workload protection
- 126 assets under Tenable.io vulnerability management
- Azure virtual machine infrastructure used for student labs and production workloads
- Microsoft Azure Active Directory B2C or equivalent for identity services
- A shared administrative tenant accessed by instructors, course administrators, and security personnel

The environment carries real risk: student personally identifiable information, authentication credentials, administrative access to cloud infrastructure, and the security monitoring data for all of the above coexist in a single shared tenant. A compromise of this environment would affect hundreds or potentially thousands of individuals.

### 2.2 The Gap Assessment Findings That Drove This Charter

In March 2026, a structured read-only gap assessment was conducted against the live ABC Corp Azure tenant. The assessment used a multi-framework methodology mapping findings to NIST CSF 2.0, NIST SP 800-53 Rev 5, CIS Controls v8, ISO/IEC 27001:2022, and MITRE ATT&CK. Key findings that directly motivated this charter:

**Identity and Access Management:**
- 100 percent of 2,315 user accounts had blank Department, Job Title, and Manager attributes, making access reviews and anomaly detection effectively impossible
- 30 accounts had five or more failed sign-in attempts, with the top account showing 512 failed attempts from a single external source
- 83,600 of 203,906 sign-in events (approximately 41 percent) completed on single-factor authentication
- 2 guest accounts had no sponsorship record and no expiration date
- Zero Conditional Access policies were enforced (CA portal returned HTTP 401 for all policy queries)

**Vulnerability and Endpoint Management:**
- ProtectionStatus table in Microsoft Sentinel returned zero rows for all 126 assets, meaning endpoint protection telemetry was entirely absent from the monitoring platform
- No vulnerability management operational capability existed (no scan schedule, no remediation SLAs, no reporting)
- No Tenable-to-Sentinel integration was configured

**Security Monitoring:**
- Zero analytics rules were active in Microsoft Sentinel
- High and Critical severity alerts existed in the SecurityAlert table with no evidence of triage or response
- Unactioned incidents existed in the SecurityIncident table with no owner, investigation, or closure record
- No alert triage SLAs, escalation paths, or on-call procedures existed

**Configuration Baseline:**
- No Azure Policy assignments were active
- No configuration hardening standard existed for any asset class
- No documented baseline for Sentinel analytics rules, CA policies, or identity attribute requirements

**Third-Party Risk:**
- No TPRM program or vendor risk registry existed
- No Data Processing Agreements were documented for any active vendor relationship
- A Tier 1 SaaS vendor (Tenable.io) had agents deployed on all 126 production assets with no formal risk assessment on record

### 2.3 The GRC Program Response

Between the gap assessment date and charter finalization, the GRC Analyst produced ten formal deliverables addressing each identified gap domain:

1. Identity Lifecycle Management Procedure v1.0
2. Vulnerability Management Policy v1.0
3. Vulnerability Management Procedure Report v1.0
4. Configuration Baseline Standard v1.0
5. Patch Management Procedure v1.0
6. Security Monitoring Policy v1.0
7. KQL Detection Rule Library v1.0 (29 detection rules)
8. SIEM Use Case Documentation v1.0 (18 use cases)
9. Third-Party Risk Assessment Template v1.0
10. GRC Program Charter v1.0 (this document)

This charter is the capstone of that body of work. It does not summarize the deliverables. It authorizes them.

---

## 3. Mission Statement

The mission of the ABC Corp GRC Program is to establish, maintain, and continuously improve a governance, risk management, and compliance function that protects the confidentiality, integrity, and availability of ABC Corp data and systems; ensures that the organization's security posture is measurable, documented, and aligned to industry frameworks; and enables ABC Corp to operate as a model of security maturity appropriate for an organization entrusted with student data, educational infrastructure, and the professional development of the cybersecurity workforce.

The GRC Program exists not to produce documentation for its own sake, but to make security real: detectable, measurable, improvable, and enforceable.

---

## 4. Program Scope and Boundaries

### 4.1 In Scope

The GRC Program has authority and responsibility over the following:

**Technology Scope:**
- All systems, resources, and data within the ABC Corp Azure subscription and Entra ID tenant
- All Azure virtual machines, storage accounts, networking resources, and workloads deployed in the ABC Corp environment
- Microsoft Sentinel, Defender for Cloud, Defender for Endpoint, and all other security tooling integrated with the tenant
- Tenable.io and all other third-party tools that access, process, or receive data from the ABC Corp environment
- All service accounts, managed identities, and application registrations within the tenant

**Organizational Scope:**
- All staff with administrative or privileged access to the ABC Corp environment
- All instructors and course administrators with elevated access
- All third-party vendors with access to or data from the ABC Corp environment
- All policies, procedures, standards, and guidelines applicable to the ABC Corp security program

**Process Scope:**
- Identity lifecycle management (provisioning, access reviews, deprovisioning)
- Vulnerability management and patch management
- Security monitoring, alert triage, and incident response initiation
- Configuration baseline management and drift detection
- Third-party risk assessment and vendor management
- Risk assessment, risk register maintenance, and POA&M tracking
- Compliance mapping and framework alignment
- GRC documentation lifecycle (creation, review, approval, version control, retirement)

### 4.2 Out of Scope

The following are explicitly out of scope for the GRC Program unless a formal scope expansion is approved by the Security Lead:

- Physical security of data center facilities (managed by Microsoft as the cloud provider)
- Microsoft Azure platform-level security (Microsoft's shared responsibility)
- Student personal devices and home networks used to access lab environments
- External systems and applications not integrated with the ABC Corp Azure tenant
- Financial controls and financial compliance (SOX, etc.) unless student payment data enters scope

### 4.3 Shared Responsibility Boundary

The ABC Corp operates on Microsoft Azure. The Azure shared responsibility model means that Microsoft is responsible for the security of the cloud infrastructure (physical hardware, hypervisor, global network). The ABC Corp GRC Program is responsible for everything in the customer responsibility layer: identity and access management, data classification and protection, application security, workload security, and network security controls deployed within the tenant.

---

## 5. Governance Structure and Authority

### 5.1 Program Authority

This charter grants the GRC Analyst formal authority to:

- Develop, publish, and enforce security policies, procedures, standards, and guidelines for the ABC Corp environment
- Conduct security assessments, audits, and gap analyses of any in-scope system or process
- Assign and track remediation items via the Plan of Action and Milestones (POA&M)
- Require evidence of control implementation from any in-scope system owner or administrator
- Escalate unresolved control deficiencies to the Security Lead
- Engage with third-party vendors on security requirements, DPAs, and TPRM assessments
- Approve or recommend approval of exceptions to security policy with appropriate compensating controls

This authority is delegated by the Security Lead and is bounded by this charter. Any expansion of GRC program authority requires Security Lead approval and a charter amendment.

### 5.2 Governance Tiers

| Tier | Role | Responsibilities |
|---|---|---|
| Tier 1: Strategic | Security Lead (Program Sponsor) | Final approval authority for GRC program direction, major policy decisions, charter amendments, risk acceptance for Tier 1 risk items, and escalated vendor risk decisions |
| Tier 2: Operational | GRC Analyst (Program Operator) | Day-to-day GRC program operation, documentation development, assessments, POA&M tracking, vendor TPRM, metrics reporting, and compliance monitoring |
| Tier 3: Technical | Cloud Administrator | Implementation of technical controls mandated by GRC policies and standards; evidence generation for control validation; remediation execution |
| Tier 3: Technical | Identity Administrator | Implementation of Entra ID controls, Conditional Access policies, PIM configuration, and identity lifecycle procedures |
| Tier 3: Technical | Detection Engineer | Development and tuning of Sentinel analytics rules, KQL queries, and detection coverage per the KQL Detection Rule Library and SIEM Use Case Documentation |
| Tier 3: Technical | Security Operations Analyst | Alert triage, incident response initiation, use case execution, and daily monitoring per the Security Monitoring Policy |
| Tier 4: Business | Business Owners | Ownership of specific systems or data domains; participation in risk acceptance decisions; compliance attestation for their systems |

### 5.3 Decision Authority Matrix

| Decision Type | GRC Analyst | Security Lead | Program Sponsor |
|---|---|---|---|
| New policy publication | Recommends | Approves | Informed |
| Policy exception | Documents and recommends | Approves | Informed if Tier 1 risk |
| Tier 1 vendor onboarding | Assesses and recommends | Approves | Informed |
| Tier 2 vendor onboarding | Assesses and approves | Informed | Not required |
| POA&M item extension (30 days) | May approve | Informed | Not required |
| POA&M item extension (greater than 30 days) | Recommends | Approves | Not required |
| Risk acceptance (Moderate or below) | Documents | Approves | Not required |
| Risk acceptance (High or Critical) | Documents | Recommends | Approves |
| Charter amendment | Drafts | Reviews | Approves |
| Program scope expansion | Drafts proposal | Reviews | Approves |

---

## 6. Program Objectives and Strategic Goals

### 6.1 Immediate Objectives (First 90 Days: March 5, 2026 to June 5, 2026)

The following objectives derive directly from the March 2026 gap assessment findings and the POA&M items established during the initial engagement.

| Objective ID | Objective | Target Date | Owner | Success Measure |
|---|---|---|---|---|
| OBJ-001 | Complete Conditional Access policy deployment covering all six required CA policy categories | April 5, 2026 | Identity Administrator | SFA sign-in percentage drops below 5 percent; zero legacy auth successes |
| OBJ-002 | Remediate endpoint protection telemetry gap (POA-004): configure ProtectionStatus reporting for all 126 assets | April 5, 2026 | Cloud Administrator | ProtectionStatus table returns data for all 126 assets |
| OBJ-003 | Deploy minimum 10 Sentinel analytics rules from the KQL Detection Rule Library | April 5, 2026 | Detection Engineer | 10 or more rules active; all Priority 1 (Critical) use cases covered |
| OBJ-004 | Complete mandatory identity attribute remediation for all 2,315 user accounts (Department, Job Title, Manager) | May 4, 2026 | Identity Administrator | Zero accounts with blank mandatory attributes in CP-002 query |
| OBJ-005 | Execute Tier 1 TPRM assessments for Microsoft Corporation and Tenable, Inc. | April 5, 2026 | GRC Analyst | Completed assessment documents on file; DPAs executed or in progress |
| OBJ-006 | Complete asset tier classification for all 126 Tenable-registered assets | April 5, 2026 | Cloud Administrator | All assets classified per CBS Section 3; classification documented in asset inventory |
| OBJ-007 | Establish formal alert triage process and confirm zero unreviewed High or Critical alerts outstanding | April 5, 2026 | Security Operations Analyst | SecurityAlert backlog query (CP-005) returns zero rows at end of each business day |
| OBJ-008 | Deploy all 18 mandatory Sentinel analytics rule categories from the Security Monitoring Policy | June 5, 2026 | Detection Engineer | All 18 rule categories active; documented in KQL Detection Rule Library v1.1 |

### 6.2 Short-Term Goals (90 Days to 6 Months: June to September 2026)

- Complete first quarterly access review cycle for all privileged accounts per ILM Procedure Section 7.1
- Achieve Sentinel analytics rule coverage across all 10 MITRE ATT&CK tactic categories represented in the SIEM Use Case Documentation
- Conduct first full vulnerability remediation cycle: confirm all Critical vulnerabilities identified in the March 2026 Tenable baseline scan are remediated within their SLA (15 days for standard assets, 7 days for Tier 1 assets)
- Complete first monthly SOC performance report per Security Monitoring Policy Section 11
- Establish formal Risk Register with all confirmed gaps from the March 2026 assessment documented as risk items

### 6.3 Long-Term Goals (6 to 18 Months: September 2026 to September 2027)

- Achieve a compliance score of 80 percent or higher against the Configuration Baseline Standard across all in-scope asset classes
- Complete the full MITRE ATT&CK detection coverage matrix with documented test results for each tactic category
- Establish an annual security awareness training program for all ABC Corp administrators and privileged users
- Complete first full annual GRC program review and charter reaffirmation
- Evaluate readiness for a formal external audit against NIST CSF 2.0 or ISO/IEC 27001:2022

---

## 7. GRC Framework and Standards Alignment

The ABC Corp GRC Program is aligned to the following frameworks. All GRC deliverables include compliance mapping to these frameworks.

### 7.1 Primary Frameworks

**NIST Cybersecurity Framework 2.0 (NIST CSF 2.0)**
The overarching framework organizing the GRC program around the six CSF functions: Govern, Identify, Protect, Detect, Respond, and Recover. All ten program deliverables map to one or more CSF functions. The charter itself primarily addresses the Govern function.

**NIST Special Publication 800-53 Rev 5**
The control catalog providing specific control requirements across 20 control families. Used as the primary technical control reference for the Configuration Baseline Standard, Identity Lifecycle Management Procedure, Vulnerability Management Policy, and Security Monitoring Policy.

**CIS Controls v8**
The implementation guidance providing prioritized security actions. Used for prioritization of immediate objectives (Implementation Group 1 controls addressed first) and for baseline configuration requirements in the CBS.

**ISO/IEC 27001:2022**
The international information security management system standard. Used for policy structure, ISMS requirements, and supplier relationship controls in the TPRM program.

**MITRE ATT&CK Enterprise**
The adversary behavior knowledge base. Used as the detection taxonomy for the KQL Detection Rule Library, SIEM Use Case Documentation, and Security Monitoring Policy. All detection rules are tagged with applicable MITRE ATT&CK tactics and techniques.

### 7.2 Supplementary Standards

| Standard | Application |
|---|---|
| NIST SP 800-61 Rev 2 | Incident Response; referenced in the Security Monitoring Policy |
| NIST SP 800-161 Rev 1 | Supply Chain Risk Management; referenced in the TPRM Template |
| NIST SP 800-63B | Digital Identity Guidelines; referenced in the ILM Procedure and CBS |
| NIST SP 800-40 Rev 4 | Patch Management; referenced in the Patch Management Procedure |
| Microsoft Azure Security Benchmark | Azure-specific technical controls; embedded in the CBS |
| OWASP Top 10 | Application security requirements; referenced in the TPRM Template Domain 5 |

### 7.3 Framework Coverage by Deliverable

| Deliverable | NIST CSF 2.0 | NIST 800-53 | CIS v8 | ISO 27001 | MITRE ATT&CK |
|---|---|---|---|---|---|
| ILM Procedure | GV, ID, PR | AC, IA, PS | Controls 5, 6 | A.5.15, A.8.2 | Credential Access, Persistence |
| VM Policy | ID, PR, DE | RA, SI, CM | Controls 7, 12 | A.8.8 | Execution, Exploitation |
| VM Procedure Report | ID, PR | RA, SI | Control 7 | A.8.8 | N/A |
| Config Baseline Standard | PR, DE | CM, SC, IA | Controls 4, 5, 6 | A.8.9 | Defense Evasion |
| Patch Management Procedure | PR, RS | SI, CM | Control 7 | A.8.8 | Exploitation |
| Security Monitoring Policy | DE, RS | SI, IR, AU | Controls 8, 13 | A.8.16, A.5.26 | All Tactics |
| KQL Detection Rule Library | DE, RS | SI, IR, AU | Controls 8, 13 | A.8.16 | 10 Tactics, 29 Techniques |
| SIEM Use Case Documentation | DE, RS | IR, SI | Controls 8, 13 | A.5.26 | 10 Tactics, 18 Scenarios |
| TPRM Template | GV, ID | SA, SR | Control 15 | A.5.19-A.5.22 | Supply Chain |
| GRC Program Charter | GV | PM, PL | Control 1 | Clauses 4-6 | N/A |

---

## 8. Program Deliverables Inventory

All deliverables produced under this GRC Program are cataloged below. Each deliverable has a unique document ID, version, effective date, owner, and review schedule.

| Document ID | Deliverable Name | Version | Effective Date | Owner | Review Schedule | Status |
|---|---|---|---|---|---|---|
| GRC-CHARTER-001 | GRC Program Charter (this document) | 1.0 | March 5, 2026 | GRC Analyst / Security Lead | Annual | Active |
| GRC-POL-001 | Information Security Policy | Pending | TBD | Security Lead | Annual | Planned Q2 2026 |
| GRC-POL-002 | Vulnerability Management Policy | 1.0 | March 5, 2026 | GRC Analyst | Annual | Active |
| GRC-POL-003 | Security Monitoring Policy | 1.0 | March 5, 2026 | GRC Analyst | Annual | Active |
| GRC-PROC-001 | Identity Lifecycle Management Procedure | 1.0 | March 5, 2026 | Identity Administrator | Annual | Active |
| GRC-PROC-002 | Vulnerability Management Procedure Report | 1.0 | March 5, 2026 | GRC Analyst | Quarterly | Active |
| GRC-PROC-003 | Patch Management Procedure | 1.0 | March 5, 2026 | Cloud Administrator | Annual | Active |
| GRC-STD-001 | Configuration Baseline Standard | 1.0 | March 5, 2026 | GRC Analyst | Annual | Active |
| GRC-TEC-001 | KQL Detection Rule Library | 1.0 | March 5, 2026 | Detection Engineer | Quarterly | Active |
| GRC-TEC-002 | SIEM Use Case Documentation | 1.0 | March 5, 2026 | GRC Analyst | Quarterly | Active |
| GRC-TPRM-001 | Third-Party Risk Assessment Template | 1.0 | March 5, 2026 | GRC Analyst | Annual | Active |
| GRC-TPRM-002 | TPRM Assessment: Microsoft Corporation | 1.0 | March 5, 2026 | GRC Analyst | Annual | Active |
| GRC-TPRM-003 | TPRM Assessment: Tenable, Inc. | 1.0 | March 5, 2026 | GRC Analyst | Annual | Active |
| GRC-RISK-001 | Risk Register | Pending | TBD | GRC Analyst | Quarterly | Planned Q2 2026 |
| GRC-RISK-002 | Plan of Action and Milestones (POA&M) | 1.0 | March 5, 2026 | GRC Analyst | Monthly | Active |
| GRC-AUD-001 | Annual GRC Program Review Report | Pending | March 2027 | GRC Analyst | Annual | Planned |

---

## 9. Roles and Responsibilities (RACI)

**RACI Key:** R = Responsible (does the work) | A = Accountable (owns the outcome) | C = Consulted (input required) | I = Informed (kept updated)

### 9.1 GRC Program Activities RACI

| Activity | GRC Analyst | Security Lead | Cloud Admin | Identity Admin | Detection Eng. | SOC Analyst | Business Owner |
|---|---|---|---|---|---|---|---|
| GRC Program Charter maintenance | R/A | A | I | I | I | I | I |
| Security policy development | R | A | C | C | C | C | C |
| Policy exception management | R | A | C | C | I | I | C |
| Risk assessment execution | R/A | C | C | C | C | C | C |
| Risk Register maintenance | R/A | I | I | I | I | I | I |
| POA&M tracking and reporting | R/A | I | R | R | R | R | I |
| POA&M item escalation | R | A | I | I | I | I | I |
| Vendor TPRM assessment | R/A | C | C | C | I | I | C |
| Vendor DPA management | R | A | I | I | I | I | C |
| Framework compliance mapping | R/A | I | C | C | C | C | I |
| Quarterly GRC metrics report | R/A | I | C | C | C | C | I |
| Annual GRC program review | R | A | C | C | C | C | C |
| ILM Procedure enforcement | R | A | R | R | I | I | I |
| CA policy implementation | C | A | I | R | I | I | I |
| Vulnerability scanning | C | I | R | I | I | I | I |
| Vulnerability remediation | C | A | R | R | I | I | I |
| Sentinel rule deployment | C | I | I | I | R | C | I |
| Alert triage | I | A | I | I | C | R | I |
| Incident escalation | C | A | C | C | C | R | I |
| CBS compliance validation | R | A | R | R | C | C | I |
| Annual access review execution | R | A | I | R | I | I | C |

---

## 10. Risk Management Approach

### 10.1 Risk Management Philosophy

The ABC Corp GRC Program uses a risk-based approach to prioritization. Not all control gaps carry equal urgency. The program applies a simple two-dimensional risk model (Likelihood x Impact) to score each identified risk, assign it to a risk tier, and determine the appropriate treatment and escalation path.

No risk is ignored. Every identified gap becomes either a POA&M item with a remediation target date or a formally accepted risk with a documented rationale and compensating controls. Informal risk acceptance ("we know about it but have no plan") is not permitted under this charter.

### 10.2 Risk Scoring Methodology

| Likelihood | Description |
|---|---|
| 5 (Certain) | Threat is actively occurring or virtually certain within the next 30 days |
| 4 (Likely) | Threat is likely within the next 90 days given current control state |
| 3 (Possible) | Threat could occur within the next 180 days |
| 2 (Unlikely) | Threat is possible but would require specific conditions |
| 1 (Rare) | Threat is theoretically possible but highly unlikely |

| Impact | Description |
|---|---|
| 5 (Catastrophic) | Full tenant compromise, mass data breach, complete loss of security visibility |
| 4 (Major) | Significant privilege escalation, large-scale data exposure, major service disruption |
| 3 (Moderate) | Limited unauthorized access, partial data exposure, significant control failure |
| 2 (Minor) | Policy violation, minor unauthorized access, recoverable incident |
| 1 (Negligible) | Minimal impact, quickly remediated, no data exposure |

| Risk Score (L x I) | Risk Tier | Treatment Authority | Default SLA |
|---|---|---|---|
| 20 to 25 | Critical | Security Lead approval required | 7 days |
| 12 to 19 | High | Security Lead notification required | 30 days |
| 6 to 11 | Moderate | GRC Analyst manages | 90 days |
| 1 to 5 | Low | GRC Analyst documents | 180 days |

### 10.3 Current Risk Profile (March 2026 Assessment Baseline)

| Risk ID | Risk Description | Likelihood | Impact | Score | Tier | Treatment | Target |
|---|---|---|---|---|---|---|---|
| RISK-001 | Credential-based account compromise via active SFA sign-in gap (83,600 SFA events confirmed) | 5 | 4 | 20 | Critical | Remediate via CA policy deployment (OBJ-001) | April 5, 2026 |
| RISK-002 | Endpoint compromise undetected due to empty ProtectionStatus table (126 assets blind) | 4 | 5 | 20 | Critical | Remediate via POA-004 (OBJ-002) | April 5, 2026 |
| RISK-003 | Privilege escalation undetected due to zero Sentinel analytics rules | 4 | 5 | 20 | Critical | Remediate via analytics rule deployment (OBJ-003) | April 5, 2026 |
| RISK-004 | Unreviewed High/Critical alert backlog represents active undetected threat activity | 4 | 4 | 16 | High | Remediate via alert triage process establishment (OBJ-007) | April 5, 2026 |
| RISK-005 | Identity lifecycle gaps enable persistent ghost accounts and unauthorized access | 3 | 4 | 12 | High | Remediate via ILM Procedure enforcement (OBJ-004) | May 4, 2026 |
| RISK-006 | Vendor compromise via unassessed Tier 1 vendor with agent access to 126 assets | 2 | 5 | 10 | Moderate | Remediate via TPRM assessment completion (OBJ-005) | April 5, 2026 |
| RISK-007 | Configuration drift creates undetected attack surface expansion | 3 | 3 | 9 | Moderate | Monitor via CBS compliance queries; full remediation ongoing | Ongoing |

### 10.4 Risk Treatment Options

**Remediate:** Implement a control to reduce the likelihood or impact of the risk to an acceptable level. Documents as a POA&M item.

**Accept:** Formally acknowledge the risk and decide not to remediate, with documented rationale and compensating controls. Requires Security Lead approval for Moderate and above. Requires program sponsor approval for High and Critical.

**Transfer:** Shift the financial impact of the risk to a third party (e.g., cyber insurance). Does not eliminate the underlying risk. Must still be documented in the Risk Register.

**Avoid:** Eliminate the risk by removing the activity or system that creates it. Requires business owner and Security Lead agreement.

---

## 11. Compliance Obligations

### 11.1 Regulatory and Legal Obligations

The ABC Corp cloud environment may be subject to the following regulatory obligations depending on the nature of data processed:

| Obligation | Applicability | Current Status | GRC Owner |
|---|---|---|---|
| FERPA (Family Educational Rights and Privacy Act) | Applicable if any student education records are processed in the environment | Under review; data classification in progress | GRC Analyst |
| GDPR (General Data Protection Regulation) | Applicable if any EU student data is processed | Under review; DPA execution in progress with Tier 1 vendors | GRC Analyst |
| CCPA (California Consumer Privacy Act) | Applicable if California resident student data is processed | Under review | GRC Analyst |
| Washington State Privacy Laws (RCW 19.255 et seq.) | Applicable as a Washington-based organization | Under review | GRC Analyst |
| Microsoft Azure Data Processing Terms | Contractually obligated via Microsoft's DPA with all Azure customers | In effect via Microsoft Customer Agreement | GRC Analyst |

### 11.2 Contractual Security Obligations

| Obligation | Source | Requirement | Status |
|---|---|---|---|
| Microsoft Acceptable Use Policy | Microsoft Customer Agreement | Tenant usage must comply with Microsoft AUP | Compliant (no AUP violations identified) |
| Microsoft Shared Responsibility Model | Azure Security Baseline | Customer must implement all customer-layer controls | Partially compliant; gaps documented in POA&M |
| Tenable.io Terms of Service | Tenable Customer Agreement | Scan data must be handled per Tenable's DPA | DPA execution in progress |

### 11.3 Internal Policy Compliance

All staff with access to the ABC Corp environment are required to comply with all GRC program policies and procedures. Non-compliance is escalated to the Security Lead. Repeated or willful non-compliance may result in access revocation.

---

## 12. Program Metrics and Key Performance Indicators

### 12.1 KPI Dashboard (Reported Quarterly)

| KPI ID | Metric Name | Measurement Method | Target | Critical Threshold | Reporting Frequency |
|---|---|---|---|---|---|
| KPI-001 | SFA Sign-In Percentage | CP-001 KQL query (weekly) | Less than 5% of sign-ins on SFA | Greater than 20% triggers escalation | Weekly trending; quarterly report |
| KPI-002 | User Account Attribute Completeness | CP-002 KQL query (monthly) | 0 accounts with blank mandatory attributes | Greater than 5% blank triggers escalation | Monthly; quarterly report |
| KPI-003 | Sentinel Analytics Rule Coverage | Count of active analytics rules by tactic category | 18 rule categories active; all Priority 1 use cases covered | Any Priority 1 use case uncovered triggers escalation | Monthly |
| KPI-004 | Endpoint Protection Reporting Coverage | ProtectionStatus row count / asset count | 100% of assets reporting ProtectionStatus | Less than 90% triggers escalation | Weekly |
| KPI-005 | Critical Vulnerability Mean Time to Remediate | Tenable.io remediation tracking | Less than 15 days for standard; less than 7 days for Tier 1 | Any overdue Critical SLA triggers escalation | Monthly |
| KPI-006 | High Vulnerability Mean Time to Remediate | Tenable.io remediation tracking | Less than 30 days for standard | Any overdue High SLA (greater than 45 days) triggers escalation | Monthly |
| KPI-007 | Alert Backlog (Open Unreviewed Alerts) | CP-005 KQL query (daily) | 0 unreviewed High/Critical alerts | Any unreviewed Critical alert older than 1 hour triggers escalation | Daily |
| KPI-008 | POA&M Items On Track | POA&M tracker | 100% of items meeting target dates | Any Critical item overdue triggers Security Lead escalation | Monthly |
| KPI-009 | Active Privileged Accounts (GA Count) | CP-004 KQL query (monthly) | Maximum 2 Global Administrator assignments | Greater than 4 active GAs triggers escalation | Monthly |
| KPI-010 | Tier 1 Vendor Assessment Currency | TPRM tracker | 100% of Tier 1 vendors with current assessment (less than 12 months old) | Any Tier 1 vendor overdue for reassessment | Annual; quarterly monitoring |
| KPI-011 | CBS Compliance Score | Manual quarterly assessment per CBS scoring methodology | Greater than 75% compliant | Less than 60% triggers Security Lead escalation | Quarterly |
| KPI-012 | Incident Mean Time to Acknowledge (MTTA) | Sentinel incident metrics | Severity 1: less than 30 minutes; Severity 2: less than 1 hour | Any Severity 1 MTTA greater than 1 hour triggers post-incident review | Monthly |

### 12.2 Quarterly GRC Program Scorecard

The GRC Analyst will produce a Quarterly GRC Program Scorecard that includes:

- All 12 KPI current values versus targets versus prior quarter
- POA&M item status summary (On Track, At Risk, Overdue, Closed)
- Risk Register summary (new risks added, risks closed, risk tier distribution)
- Key accomplishments during the quarter
- Top three risk items for Security Lead attention
- Program objectives status (from Section 6)
- Planned activities for the next quarter

The Quarterly Scorecard is reviewed by the Security Lead and filed in the GRC document repository.

---

## 13. Resourcing and Budget Authority

### 13.1 GRC Program Roles Required

The following roles are required for effective GRC program operation. Current staffing status is noted.

| Role | FTE Requirement | Current Status | Gap |
|---|---|---|---|
| GRC Analyst (Program Operator) | 1.0 FTE or equivalent | Filled | None |
| Security Lead (Program Sponsor) | Program oversight; not full-time GRC dedication required | Filled | None |
| Cloud Administrator | 0.5 FTE equivalent for GRC-related remediation and control implementation | Current coverage assessment pending | TBD |
| Identity Administrator | 0.5 FTE equivalent for Entra ID and identity control implementation | Current coverage assessment pending | TBD |
| Detection Engineer | 0.25 FTE equivalent for KQL rule development and tuning | Current coverage assessment pending | TBD |
| Security Operations Analyst | Coverage for alert triage per SMP SLAs (Critical: 1 hour response) | Current coverage assessment pending | TBD |

### 13.2 Tooling and Platform Dependencies

The GRC program depends on the following tooling that is already licensed and operational within the ABC Corp Azure tenant. No new tool purchases are required to operate the program at its current scope.

| Tool | Function | GRC Dependency | Current Status |
|---|---|---|---|
| Microsoft Sentinel | SIEM and security monitoring | KPI measurement, detection rule deployment, alert triage | Active |
| Microsoft Entra ID | Identity governance | ILM Procedure enforcement, CA policy, PIM | Active |
| Tenable.io | Vulnerability management | Vulnerability KPI measurement, scan data, asset inventory | Active |
| Microsoft Defender for Cloud | Cloud security posture | CBS compliance scoring, alert generation | Active |
| Microsoft Defender for Endpoint | Endpoint protection | ProtectionStatus KPI (pending POA-004 remediation) | Partially Active |
| Azure Policy | Configuration enforcement | CBS drift detection | Not yet configured |
| Azure Monitor / Log Analytics | Log data foundation | All KQL-based KPIs | Active |

### 13.3 Budget Authority

The GRC Analyst has authorization to approve expenditures up to a defined threshold for GRC-related tools, assessments, and training without additional approval. Expenditures above that threshold require Security Lead approval. Any new vendor relationship requires both TPRM assessment completion and Security Lead approval regardless of cost.

---

## 14. Program Communication and Reporting Cadence

### 14.1 Regular Reporting Schedule

| Report | Audience | Frequency | Format | Delivered By |
|---|---|---|---|---|
| Daily Alert Backlog Status | Security Operations Analyst, Security Lead | Daily | CP-005 KQL query result | Automated or SOC Analyst |
| Weekly SFA Trending Report | Security Lead, Identity Administrator | Weekly | CP-001 KQL query result with trend line | GRC Analyst |
| Monthly POA&M Status Report | Security Lead | Monthly | Tabular POA&M tracker with status updates | GRC Analyst |
| Monthly SOC Performance Report | Security Lead, Detection Engineer | Monthly | Per SMP Section 11 | Security Operations Analyst |
| Monthly Patch Compliance Report | Security Lead, Cloud Administrator | Monthly | Per PMP Section 11 | Cloud Administrator |
| Quarterly GRC Program Scorecard | Security Lead, Program Sponsor | Quarterly | Full scorecard per Section 12.2 | GRC Analyst |
| Quarterly KQL Rule Review | Detection Engineer, GRC Analyst | Quarterly | Rule effectiveness report, false positive rates | Detection Engineer |
| Annual GRC Program Review | All stakeholders | Annual | Full program review report | GRC Analyst |
| Annual Charter Reaffirmation | Security Lead, Program Sponsor | Annual | Charter amendment or reaffirmation memo | GRC Analyst |

### 14.2 Escalation Communication Protocol

Any of the following conditions require immediate out-of-cycle communication to the Security Lead:

- Any Critical (Severity 1) security incident declared
- Any POA&M Critical-tier item that misses its target date
- Any mandatory risk flag triggered in a TPRM assessment
- Any discovery of a breach or suspected breach involving ABC Corp data
- Any unauthorized modification to a break-glass account or Global Administrator assignment
- Any Critical KPI threshold breach (as defined in Section 12.1)
- Any single-factor authentication on a privileged account (adm- prefix)

The GRC Analyst is the communication coordinator for all escalation events during business hours. The Security Lead is the decision authority for all escalation events.

---

## 15. Program Lifecycle and Sustainability

### 15.1 Annual Review Process

The GRC Program undergoes a formal annual review each March, timed to coincide with the anniversary of the initial gap assessment. The annual review includes:

1. Full update of the risk register with re-scored existing risks and newly identified risks
2. Reassessment of all POA&M items: closed, in-progress, overdue, or carried forward
3. Review and reaffirmation or amendment of all Tier 1 GRC policies and standards
4. TPRM reassessment scheduling for all Tier 1 vendors
5. KPI performance review for the prior 12 months
6. Program objectives update for the coming year
7. Charter reaffirmation or amendment

The annual review concludes with a written Annual GRC Program Review Report submitted to the Security Lead and filed in the GRC document repository.

### 15.2 Document Version Control

All GRC program documents follow this versioning convention:

| Version Type | When Used | Example |
|---|---|---|
| Major version (X.0) | Significant content changes, scope changes, or annual review rewrite | v2.0 |
| Minor version (X.Y) | Clarifications, additions, or corrections that do not change scope or requirements | v1.1 |
| Patch (X.Y.Z) | Typo corrections, formatting fixes, link updates | v1.0.1 |

All published versions are retained in the GRC document repository. Superseded versions are archived and not deleted for a minimum of 7 years.

### 15.3 GRC Program Continuity

If the GRC Analyst role becomes vacant, the Security Lead assumes GRC program accountability on an interim basis. A transition plan including document repository access, current POA&M status, and active vendor assessment status must be maintained by the GRC Analyst and updated quarterly.

---

## 16. Constraints, Assumptions, and Dependencies

### 16.1 Constraints

- The GRC Program operates within a shared tenant environment. Not all Azure tenant configurations are under the direct control of the ABC Corp GRC program (Microsoft manages platform-layer controls).
- Student privacy considerations may constrain the depth of identity attribute collection for non-administrative accounts.
- The shared multi-tenant nature of the environment means that some security controls (network-level isolation, dedicated compute) have cost and architecture implications that require executive approval before implementation.
- All GRC program activities are read-only or advisory with respect to live production systems unless a specific change request has been approved by the Cloud Administrator or Identity Administrator.

### 16.2 Assumptions

- The Security Lead will remain engaged and available for monthly POA&M reviews, quarterly scorecard reviews, and escalation decisions within the SLAs defined in this charter.
- The Cloud Administrator, Identity Administrator, Detection Engineer, and Security Operations Analyst roles will be staffed by qualified individuals who have read and acknowledged the GRC program policies relevant to their roles.
- Microsoft Azure will continue to provide the telemetry sources (AuditLogs, SignInLogs, SecurityAlert, ProtectionStatus) required for KPI measurement.
- Tenable.io will continue to provide vulnerability scan data and asset inventory for the 126-asset management scope.
- No material changes to the Azure tenant architecture will be made without the GRC Analyst being notified and consulted on security implications.

### 16.3 Dependencies

| Dependency | Type | Impact if Unavailable | Mitigation |
|---|---|---|---|
| Microsoft Sentinel workspace operational | Technical | No KPI measurement, no detection, no alert triage | Monitor via MI-002 connector health rule |
| Entra ID AuditLogs and SignInLogs flowing to Sentinel | Technical | KPI-001, KPI-002, KPI-009 unmeasurable | Monitor via MI-002; alert on ingestion gap |
| Tenable.io scans running on schedule | Technical | KPI-005, KPI-006 unmeasurable; vulnerability exposure grows | Monitor scan schedule; alert on missed scan windows |
| ProtectionStatus table populated (POA-004) | Technical | KPI-004 unmeasurable; UC-009/UC-010 cannot detect threats | POA-004 target April 5, 2026 |
| Security Lead availability for approvals | Organizational | Risk acceptance and policy exceptions blocked | Defined SLAs in Section 5.3 with escalation path |

---

## 17. Charter Approval and Signatures

This GRC Program Charter is the authorizing document for the ABC Corp GRC Program. By signing below, the approving parties acknowledge that they have read this charter, understand the program scope and their role within it, and commit to supporting the program's mission, objectives, and governance structure.

### 17.1 Approval Signatures

| Role | Name | Signature | Date |
|---|---|---|---|
| Program Sponsor / Security Lead | [Name] | [Signature] | March 5, 2026 |
| GRC Analyst (Program Operator) | [Name] | [Signature] | March 5, 2026 |
| Cloud Administrator | [Name] | [Signature] | March 5, 2026 |
| Identity Administrator | [Name] | [Signature] | March 5, 2026 |

### 17.2 Charter Amendment Log

| Amendment ID | Amendment Date | Description | Approved By |
|---|---|---|---|
| v1.0 | March 5, 2026 | Initial charter. Authorized by Security Lead. | [Security Lead Name] |
| [Next amendment] | [Date] | [Description] | [Approver] |

### 17.3 Charter Distribution

This charter is classified CONFIDENTIAL. Distribution is limited to:
- Security Lead
- GRC Analyst
- Cloud Administrator
- Identity Administrator
- Detection Engineer
- Security Operations Analyst
- Program Sponsor

Copies may not be shared outside the ABC Corp organization without Security Lead approval.

---

*End of Document*

*This GRC Program Charter was authored as the capstone of a structured GRC program buildout for the ABC Corp Azure shared tenant, grounded in a real multi-framework gap assessment conducted in March 2026. Every objective, KPI, risk item, and deliverable reference in this document traces to a confirmed finding or confirmed control in the live environment. The program did not start from a template. It started from the actual state of a real Azure tenant and built its governance structure from that reality.*
