# Third-Party Risk Assessment Template

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Third-Party Risk Management
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> ⚠️ **Disclaimer:** This is a sanitized version of a Third-Party Risk Assessment Template developed for a live production Microsoft Azure environment. All organization names, vendor account references, and domain-specific details have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This TPRM template was built from and validated against the actual vendor landscape of a live Azure shared tenant assessed in March 2026. The template is not generic. The tiering methodology, scoring weights, questionnaire domains, and completed example assessment in Appendix A reflect real vendors actively integrated with the live environment at time of assessment. The two Tier 1 vendors identified during assessment were the Azure cloud platform provider and a vulnerability management SaaS platform with agents deployed on 126 production assets.

**Confirmed Findings from the Live Environment That Shaped This Template (real data):**
- 126 assets under active vulnerability management scanning by a Tier 1 SaaS vendor with agent-based access to production systems. (real finding)
- No formal TPRM program or vendor risk registry existed at time of assessment. (real finding)
- No Data Processing Agreements (DPAs) documented for any vendor relationship at time of assessment. (real finding)
- No sub-processor inventory existed for any active vendor relationship. (real finding)
- Vendor dependency on the cloud platform for Sentinel, Defender for Cloud, and Entra ID meant a single vendor controlled all detection and identity controls. (real finding)
- This template directly implements the supply chain risk management requirements of NIST SP 800-161 Rev 1, NIST SP 800-53 Rev 5 SA-9 and SR-2, and CIS Controls v8 Control 15.

---


**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Third-Party Risk Management
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Document Type:** Template and Procedure (Third-Party Risk Management Program)
**Review Cadence:** Annual, or following any significant vendor security incident, data breach involving a third party, or material change to a vendor's security posture

> **Assessment Context:** This Third-Party Risk Assessment Template was developed for the ABC Corp Azure shared tenant environment. The ABC Corp operates as a live production Azure environment serving approximately 1,000 or more students and instructors. Third parties that integrate with, access, or process data from this environment require formal risk assessment before onboarding and on a periodic basis thereafter. This template is grounded in the actual vendor landscape of the ABC Corp cloud environment, including cloud service providers (Microsoft Azure, Tenable, Sentinel), identity providers, and any future vendor integrations. The scoring methodology and risk tiers are calibrated to the specific data types and access patterns of this environment.

---

## Table of Contents

1. Program Overview and Purpose
2. Scope and Applicability
3. Vendor Tiering Methodology
4. Assessment Process and Workflow
5. Part A: Vendor Intake and Classification
6. Part B: Security Control Domain Questionnaire
   - Domain 1: Organizational Security Governance
   - Domain 2: Data Protection and Privacy
   - Domain 3: Access Control and Identity Management
   - Domain 4: Network and Infrastructure Security
   - Domain 5: Application and Software Security
   - Domain 6: Incident Response and Business Continuity
   - Domain 7: Compliance and Audit
   - Domain 8: Supply Chain and Sub-Processor Risk
7. Part C: Risk Scoring and Rating Calculation
8. Part D: Risk Treatment and Remediation Tracking
9. Part E: Assessment Conclusion and Approval
10. Appendix A: Completed Example Assessment (Tenable.io)
11. Appendix B: Scoring Reference Tables
12. Appendix C: Related Documents and Standards

---

## 1. Program Overview and Purpose

### 1.1 Purpose

The Third-Party Risk Management (TPRM) program establishes a systematic process for identifying, assessing, and managing the security risks introduced by vendors, service providers, contractors, and any other external parties that access, process, transmit, or store data belonging to the ABC Corp or that provide services critical to operations.

This template serves three functions:

First, it is the intake and assessment instrument used by the GRC Analyst to evaluate a specific vendor. All fields are designed to be completed for a single vendor per template instance.

Second, it is the procedural guide defining how a TPRM assessment is conducted, what evidence is acceptable, and how the risk rating is calculated.

Third, it is the audit record. A completed template constitutes the formal documentation of a vendor's assessed risk posture and the risk treatment decision made by the Security Lead.

### 1.2 Why TPRM Matters for ABC Corp Environment

The ABC Corp cloud environment presents a specific third-party risk profile because it is:

- A shared multi-tenant Azure environment where student data, administrative credentials, and security telemetry coexist
- Dependent on Microsoft Azure for its core infrastructure (Entra ID, Sentinel, Defender for Cloud, Azure VMs)
- Integrated with Tenable.io for vulnerability management scanning of 126 assets
- A potential future integrator of additional SaaS tools, identity providers, and security products as the program matures

Any vendor that receives access to this environment, processes data from it, or provides a service that security controls depend on introduces supply-chain risk. The TPRM program ensures that risk is explicitly understood and accepted by the Security Lead before the vendor relationship is established.

---

## 2. Scope and Applicability

### 2.1 Vendors Requiring Assessment

A formal TPRM assessment is required before onboarding any vendor that meets one or more of the following criteria:

| Criterion | Assessment Tier Required |
|---|---|
| Vendor will be granted direct access (any level) to the ABC Corp Azure subscription or Entra ID tenant | Tier 1 (Critical) |
| Vendor processes, stores, or transmits student PII or authentication credentials | Tier 1 (Critical) |
| Vendor provides a security tool or service that ABC Corp security controls depend on (SIEM, EDR, vulnerability scanner, PAM, identity provider) | Tier 1 (Critical) |
| Vendor will be granted read-only access to non-sensitive operational data | Tier 2 (High) |
| Vendor provides a SaaS tool accessed by ABC Corp staff but not directly integrated with ABC Corp systems | Tier 2 (High) |
| Vendor provides professional services (consulting, training, audit) with no system access | Tier 3 (Moderate) |
| Vendor provides commodity goods or services with no data access | Out of scope (no assessment required) |

### 2.2 Vendors Out of Scope

The following vendor categories are out of scope for TPRM assessment:
- Vendors providing purely physical goods with no data or system access
- Vendors whose entire relationship is governed by a Microsoft Azure Marketplace agreement with Microsoft as the contracting party
- Vendors providing publicly available information services with no data submission from ABC Corp

### 2.3 Existing Vendor Reassessment Schedule

| Tier | Reassessment Frequency | Trigger for Out-of-Cycle Reassessment |
|---|---|---|
| Tier 1 (Critical) | Annual | Vendor security breach; material change to data access scope; vendor merger or acquisition; SOC 2 report finding; regulatory action against the vendor |
| Tier 2 (High) | Biennial (every 2 years) | Vendor security breach; material scope change; SOC 2 report finding |
| Tier 3 (Moderate) | Every 3 years | Material scope change |

---

## 3. Vendor Tiering Methodology

Before the detailed questionnaire is administered, the vendor is classified into one of three tiers. The tier determines the depth of assessment required and the risk treatment authority.

### 3.1 Tier Classification Matrix

| Factor | Tier 1 (Critical) | Tier 2 (High) | Tier 3 (Moderate) |
|---|---|---|---|
| Data access | PII, authentication credentials, security telemetry, or financial data | Operational data, pseudonymized data | No sensitive data access |
| System access | Direct API or console access to production systems | Indirect access via data export or report | No system access |
| Security dependency | ABC Corp security controls depend on this vendor | Security-adjacent (logging, monitoring support) | No security function |
| Blast radius | Vendor compromise could expose all ABC Corp data | Vendor compromise could expose limited operational data | Vendor compromise has no data impact |
| Contractual data processing | Vendor is a data processor for personal data | Vendor receives de-identified data | No data processing |

**Tier assignment rule:** Assign the highest applicable tier. If a vendor meets even one Tier 1 criterion, they are a Tier 1 vendor.

### 3.2 Current ABC Corp Vendor Tier Registry

| Vendor | Service | Assigned Tier | Last Assessed |
|---|---|---|---|
| Microsoft Corporation | Azure Cloud Platform, Entra ID, Microsoft Sentinel, Defender for Cloud, Defender for Endpoint | Tier 1 | March 5, 2026 (initial assessment) |
| Tenable, Inc. | Tenable.io Vulnerability Management Platform (scanning 126 assets) | Tier 1 | March 5, 2026 (initial assessment) |
| [Next Vendor] | [Service Description] | [Tier] | [Date] |

---

## 4. Assessment Process and Workflow

### 4.1 Assessment Phases

**Phase 1: Vendor Intake (Part A)**
The GRC Analyst completes the vendor intake form to establish the vendor's identity, data access scope, and tier classification. This phase requires input from the business owner who is proposing the vendor relationship.

**Phase 2: Documentation Collection**
The GRC Analyst requests the following documentation from the vendor:
- Current SOC 2 Type II report (or ISO 27001 certificate with surveillance audit report)
- Penetration test results summary (executive summary acceptable; full report preferred)
- Data Processing Agreement (DPA) or Data Protection Addendum
- Subprocessor list (if applicable)
- Incident response and breach notification policy
- Business continuity and disaster recovery plan summary

For Tier 1 vendors, all documentation is required. For Tier 2 vendors, SOC 2 or ISO 27001 and DPA are minimum requirements. For Tier 3 vendors, a vendor-completed self-assessment questionnaire is acceptable.

**Phase 3: Questionnaire Administration (Part B)**
The GRC Analyst administers the security control domain questionnaire. For Tier 1 vendors, the questionnaire is administered via a vendor meeting or formal RFP process in addition to documentation review. For Tier 2 vendors, a written questionnaire response is acceptable. For Tier 3 vendors, a shortened self-assessment version is used.

**Phase 4: Scoring and Risk Rating (Part C)**
The GRC Analyst scores each questionnaire response using the scoring rubric and calculates the composite risk rating.

**Phase 5: Risk Treatment Decision (Part D)**
The Security Lead reviews the risk rating and determines the risk treatment action: Accept, Accept with Conditions, Escalate for Review, or Reject.

**Phase 6: Conclusion and Approval (Part E)**
The assessment is finalized, signed, and filed. If accepted, the vendor is added to the Vendor Tier Registry and scheduled for reassessment.

### 4.2 Assessment Timeline SLAs

| Vendor Tier | Target Assessment Completion | Maximum Permissible Duration |
|---|---|---|
| Tier 1 | 15 business days from engagement | 30 business days |
| Tier 2 | 10 business days from engagement | 20 business days |
| Tier 3 | 5 business days from engagement | 10 business days |

---

## 5. Part A: Vendor Intake and Classification

**Instructions:** Complete all fields in this section before proceeding to the questionnaire. Fields marked [REQUIRED] must be completed for the assessment to proceed.

---

### A.1 Vendor Identification

| Field | Response |
|---|---|
| Vendor Legal Name | [REQUIRED - Legal entity name as it appears on the contract] |
| Vendor DBA / Brand Name | [If different from legal name] |
| Vendor Website | [URL] |
| Vendor Headquarters | [City, State/Province, Country] |
| Primary Contact Name | [REQUIRED - Name of vendor's security or compliance contact] |
| Primary Contact Title | [Title] |
| Primary Contact Email | [Email address] |
| Primary Contact Phone | [Phone number] |
| Account Manager / Sales Contact | [Name and email] |
| Vendor Contract Reference | [Contract number or Master Services Agreement ID] |
| Assessment Date | [REQUIRED - Date assessment was initiated] |
| Assessor Name | [REQUIRED - GRC Analyst conducting the assessment] |
| Business Owner | [REQUIRED - ABC Corp staff member who proposed this vendor relationship] |

---

### A.2 Service Description and Data Access Scope

| Field | Response |
|---|---|
| Service Name and Description | [REQUIRED - Describe what service this vendor provides to ABC Corp] |
| Integration Method | [API, SSO/SAML, agent-based, manual data export, web portal, other] |
| Access Type | [Read-only, Read/Write, Administrative, No direct access] |
| Systems Accessed | [List all ABC Corp systems, environments, or data stores this vendor will access] |
| Data Categories Accessed or Processed | [Select all that apply: Student PII, Authentication credentials, Security telemetry, Vulnerability scan data, Network configuration data, Email/calendar data, Financial data, None] |
| Data Volume | [Approximate volume of data processed or transmitted per month] |
| Data Residency | [Where will the vendor store ABC Corp data? Country/region required] |
| Sub-processors | [Will this vendor share ABC Corp data with any sub-processors? If yes, list them.] |
| Contractual Basis | [Master Services Agreement, SaaS Terms of Service, Data Processing Agreement, Other] |
| DPA Executed | [Yes / No / In Progress] |

---

### A.3 Tier Classification

**Instructions:** Review each criterion below and mark whether it applies to this vendor. The highest applicable tier determines the vendor's assigned tier.

| Tier 1 Criteria | Applies? |
|---|---|
| Vendor will be granted direct access to the ABC Corp Azure subscription or Entra ID tenant | Yes / No |
| Vendor processes, stores, or transmits student PII or authentication credentials | Yes / No |
| Vendor provides a security tool that ABC Corp security controls depend on | Yes / No |
| Vendor compromise could result in exposure of all ABC Corp data | Yes / No |

| Tier 2 Criteria | Applies? |
|---|---|
| Vendor will be granted read-only access to non-sensitive operational data | Yes / No |
| Vendor provides a SaaS tool accessed by ABC Corp staff | Yes / No |
| Vendor is security-adjacent (supports logging, monitoring, or compliance functions) | Yes / No |

| Tier 3 Criteria | Applies? |
|---|---|
| Vendor provides professional services with no system access | Yes / No |
| Vendor receives de-identified or aggregated data only | Yes / No |

**Assigned Vendor Tier:** [ ] Tier 1 (Critical)  [ ] Tier 2 (High)  [ ] Tier 3 (Moderate)  [ ] Out of Scope

**Tier Assignment Rationale:** [Explain which criteria drove the tier assignment]

**Business Owner Acknowledgment:** [Name and date]

---

### A.4 Vendor Documentation Received

| Document | Required For | Received? | Date Received | Version / Date of Document |
|---|---|---|---|---|
| SOC 2 Type II Report | Tier 1, Tier 2 | Yes / No / Requested | | |
| ISO 27001 Certificate | Alternative to SOC 2 | Yes / No / N/A | | |
| ISO 27001 Surveillance Audit Report | If ISO 27001 | Yes / No / N/A | | |
| Penetration Test Executive Summary | Tier 1 | Yes / No / Requested | | |
| Data Processing Agreement (DPA) | Tier 1, Tier 2 | Yes / No / In Progress | | |
| Subprocessor List | Tier 1, Tier 2 | Yes / No / N/A | | |
| Incident Response / Breach Notification Policy | Tier 1 | Yes / No / Requested | | |
| Business Continuity / DR Plan Summary | Tier 1 | Yes / No / Requested | | |
| Information Security Policy (summary or public version) | All Tiers | Yes / No / Requested | | |
| Vendor Self-Assessment Questionnaire | Tier 3 | Yes / No / N/A | | |

**Documentation Gap Notes:** [List any required documents not yet received and the plan to obtain them]

---

## 6. Part B: Security Control Domain Questionnaire

**Instructions for Scoring:** Each question has a maximum point value shown in brackets. Score each response 0 to the maximum based on the rubric in Section 8. Leave the Score column blank and complete it in Part C. For yes/no questions, a Yes is the full score and a No is zero unless the rubric specifies otherwise.

**For Tier 3 vendors:** Complete only the questions marked [T3] in each domain. All other questions are optional.

---

### Domain 1: Organizational Security Governance

**Domain Weight: 12% of total score**

| # | Question | Max Points | Score | Evidence Reviewed |
|---|---|---|---|---|
| 1.1 [T3] | Does the vendor have a documented Information Security Policy that is reviewed and approved by senior management at least annually? | 6 | | |
| 1.2 | Does the vendor have a designated Chief Information Security Officer (CISO) or equivalent role with formal security accountability? | 4 | | |
| 1.3 | Does the vendor conduct regular security awareness training for all employees? If yes, describe the frequency and format. | 4 | | |
| 1.4 [T3] | Does the vendor perform background screening on employees who will have access to ABC Corp data or systems? | 6 | | |
| 1.5 | Does the vendor have a formal vendor/third-party risk management program of their own to manage their supply chain? | 4 | | |
| 1.6 | Does the vendor maintain a documented asset inventory for systems that will process ABC Corp data? | 4 | | |
| 1.7 | Has the vendor had any regulatory actions, fines, or enforcement proceedings related to information security or data privacy in the last three years? If yes, provide details. | 6 | | |
| 1.8 | Does the vendor carry cyber liability insurance? If yes, provide coverage amount. | 4 | | |

**Domain 1 Total (Maximum: 38 points):** ___ / 38

---

### Domain 2: Data Protection and Privacy

**Domain Weight: 18% of total score**

| # | Question | Max Points | Score | Evidence Reviewed |
|---|---|---|---|---|
| 2.1 [T3] | Does the vendor have a documented data classification policy that categorizes data by sensitivity? | 5 | | |
| 2.2 [T3] | Is data at rest encrypted using industry-standard encryption (AES-256 or equivalent)? If yes, describe the implementation. | 8 | | |
| 2.3 [T3] | Is data in transit encrypted using TLS 1.2 or higher for all data flows involving ABC Corp data? | 8 | | |
| 2.4 | Does the vendor use encryption key management practices that ensure ABC Corp cannot be denied access to their own data? Describe the key management model (e.g., customer-managed keys). | 6 | | |
| 2.5 [T3] | Does the vendor have a documented data retention and disposal policy? Does disposal use certified data destruction methods? | 6 | | |
| 2.6 | Will ABC Corp data be stored or processed outside the country or region agreed upon in the DPA? | 6 | | |
| 2.7 | Does the vendor have a formal process for handling data subject access requests (DSARs) if applicable to the data types being processed? | 4 | | |
| 2.8 | Does the vendor have a documented procedure for notifying customers of a data breach? What is the committed notification timeline? | 8 | | |
| 2.9 | Has the vendor experienced a data breach involving customer data in the last three years? If yes, describe the incident and remediation. | 8 | | |
| 2.10 | Does the vendor conduct Data Protection Impact Assessments (DPIAs) for processing activities involving personal data? | 4 | | |

**Domain 2 Total (Maximum: 63 points):** ___ / 63

---

### Domain 3: Access Control and Identity Management

**Domain Weight: 16% of total score**

| # | Question | Max Points | Score | Evidence Reviewed |
|---|---|---|---|---|
| 3.1 [T3] | Does the vendor enforce multi-factor authentication (MFA) for all employee access to systems that process ABC Corp data? | 8 | | |
| 3.2 [T3] | Does the vendor use the principle of least privilege for all access to systems and data? Describe the implementation. | 8 | | |
| 3.3 | Does the vendor enforce privileged access management (PAM) controls for administrative accounts (e.g., just-in-time access, session recording, privileged account vaulting)? | 8 | | |
| 3.4 | Does the vendor perform periodic access reviews for accounts with access to ABC Corp data? What is the review frequency? | 6 | | |
| 3.5 | Does the vendor have a formal offboarding process that ensures access is revoked within a defined SLA when an employee leaves? | 6 | | |
| 3.6 | Does the vendor disable or delete dormant accounts after a defined inactivity period? What is the threshold? | 4 | | |
| 3.7 | Does the vendor use single sign-on (SSO) with centralized identity governance for internal systems? | 4 | | |
| 3.8 | Are service accounts and API credentials managed with rotation policies and stored in a credential vault? | 6 | | |

**Domain 3 Total (Maximum: 50 points):** ___ / 50

---

### Domain 4: Network and Infrastructure Security

**Domain Weight: 14% of total score**

| # | Question | Max Points | Score | Evidence Reviewed |
|---|---|---|---|---|
| 4.1 [T3] | Does the vendor segment networks to isolate systems that process ABC Corp data from other customer environments? | 8 | | |
| 4.2 | Does the vendor use Web Application Firewalls (WAF) and/or DDoS protection for internet-facing services? | 5 | | |
| 4.3 | Does the vendor perform regular network vulnerability scanning? What is the scan frequency? | 6 | | |
| 4.4 | Does the vendor conduct annual penetration tests on systems that process ABC Corp data? Will they share results? | 8 | | |
| 4.5 | Does the vendor maintain firewall and network access control rule sets, and are these reviewed at least annually? | 5 | | |
| 4.6 | Does the vendor use intrusion detection/prevention systems (IDS/IPS) in environments processing ABC Corp data? | 5 | | |
| 4.7 | Does the vendor have a process for patching critical and high severity vulnerabilities in internet-facing systems? What is the SLA? | 6 | | |
| 4.8 | Are cloud environments (if applicable) configured using infrastructure-as-code with security baselines enforced? | 5 | | |

**Domain 4 Total (Maximum: 48 points):** ___ / 48

---

### Domain 5: Application and Software Security

**Domain Weight: 12% of total score**

| # | Question | Max Points | Score | Evidence Reviewed |
|---|---|---|---|---|
| 5.1 | Does the vendor have a documented Secure Software Development Lifecycle (SSDLC) that includes security requirements, threat modeling, code review, and security testing? | 8 | | |
| 5.2 | Does the vendor perform Static Application Security Testing (SAST) and/or Dynamic Application Security Testing (DAST) as part of the development pipeline? | 6 | | |
| 5.3 | Does the vendor perform dependency and open-source library scanning to identify vulnerable components? | 6 | | |
| 5.4 | Does the vendor have a vulnerability disclosure program or bug bounty program? | 4 | | |
| 5.5 | Does the vendor use code signing to verify the integrity of software delivered to customers? | 4 | | |
| 5.6 | Does the vendor conduct security testing before every major release? Describe the process. | 6 | | |
| 5.7 | Has the vendor's application been assessed for OWASP Top 10 vulnerabilities? When was the last assessment? | 6 | | |

**Domain 5 Total (Maximum: 40 points):** ___ / 40

---

### Domain 6: Incident Response and Business Continuity

**Domain Weight: 14% of total score**

| # | Question | Max Points | Score | Evidence Reviewed |
|---|---|---|---|---|
| 6.1 [T3] | Does the vendor have a documented Incident Response Plan that has been tested in the last 12 months? | 8 | | |
| 6.2 [T3] | What is the vendor's committed breach notification timeline to customers? Is this contractually guaranteed? | 8 | | |
| 6.3 | Does the vendor have a 24/7 security operations function or on-call incident response capability? | 6 | | |
| 6.4 | Does the vendor have a documented Business Continuity Plan (BCP) and Disaster Recovery Plan (DRP) that are tested at least annually? | 8 | | |
| 6.5 | What is the vendor's contractually committed Recovery Time Objective (RTO) and Recovery Point Objective (RPO) for the service being provided? | 6 | | |
| 6.6 | Does the vendor maintain a published Service Level Agreement (SLA) with uptime guarantees? What is the committed uptime? | 5 | | |
| 6.7 | Has the vendor experienced a major service outage in the last 12 months? If yes, describe the incident and resolution. | 5 | | |

**Domain 6 Total (Maximum: 46 points):** ___ / 46

---

### Domain 7: Compliance and Audit

**Domain Weight: 8% of total score**

| # | Question | Max Points | Score | Evidence Reviewed |
|---|---|---|---|---|
| 7.1 [T3] | Does the vendor hold a current SOC 2 Type II report or ISO 27001 certification? If yes, what is the scope? | 10 | | |
| 7.2 | Does the vendor's SOC 2 report or ISO 27001 scope include the systems and services being provided to ABC Corp? | 8 | | |
| 7.3 | Are there any qualified opinions, exceptions, or material findings in the vendor's most recent SOC 2 or ISO 27001 audit report? If yes, describe. | 8 | | |
| 7.4 | Does the vendor comply with applicable data privacy regulations (GDPR, CCPA, FERPA if student data is involved)? | 6 | | |
| 7.5 | Does the vendor permit customer audit rights or third-party assessments of their security controls? | 5 | | |

**Domain 7 Total (Maximum: 37 points):** ___ / 37

---

### Domain 8: Supply Chain and Sub-Processor Risk

**Domain Weight: 6% of total score**

| # | Question | Max Points | Score | Evidence Reviewed |
|---|---|---|---|---|
| 8.1 | Does the vendor have a formal process for assessing the security posture of their own sub-processors and suppliers who will have access to ABC Corp data? | 8 | | |
| 8.2 | Does the vendor maintain a current, complete list of all sub-processors that may access ABC Corp data? Will they provide this list? | 6 | | |
| 8.3 | Does the vendor notify customers of material changes to their subprocessor list? What is the advance notice period? | 5 | | |
| 8.4 | Does the vendor contractually require their sub-processors to meet equivalent security standards to the vendor's own commitments? | 6 | | |

**Domain 8 Total (Maximum: 25 points):** ___ / 25

---

## 7. Part C: Risk Scoring and Rating Calculation

**Instructions:** Enter the domain scores from Part B, apply the domain weights, and calculate the composite score.

### C.1 Domain Score Summary

| Domain | Description | Max Points | Raw Score | Domain Weight | Weighted Score |
|---|---|---|---|---|---|
| Domain 1 | Organizational Security Governance | 38 | ___ | 12% | ___ |
| Domain 2 | Data Protection and Privacy | 63 | ___ | 18% | ___ |
| Domain 3 | Access Control and Identity Management | 50 | ___ | 16% | ___ |
| Domain 4 | Network and Infrastructure Security | 48 | ___ | 14% | ___ |
| Domain 5 | Application and Software Security | 40 | ___ | 12% | ___ |
| Domain 6 | Incident Response and Business Continuity | 46 | ___ | 14% | ___ |
| Domain 7 | Compliance and Audit | 37 | ___ | 8% | ___ |
| Domain 8 | Supply Chain and Sub-Processor Risk | 25 | ___ | 6% | ___ |
| **Total** | | **347** | **___** | **100%** | **___** |

**Weighted Score Calculation:**
Weighted Score for each domain = (Raw Score / Max Points) x Domain Weight x 100
Composite Score = Sum of all Weighted Scores

### C.2 Composite Risk Score Interpretation

| Composite Score | Risk Rating | Interpretation | Default Risk Treatment |
|---|---|---|---|
| 85 to 100 | Low Risk | Vendor demonstrates strong security controls across all domains. Risk is acceptable without conditions. | Accept |
| 70 to 84 | Low-Moderate Risk | Vendor demonstrates adequate security controls with minor gaps. Risk is acceptable with documented acknowledgment. | Accept with Documentation |
| 55 to 69 | Moderate Risk | Vendor has notable gaps in one or more domains. Risk is conditionally acceptable pending remediation commitments. | Accept with Conditions |
| 40 to 54 | High Risk | Vendor has significant security control deficiencies. Onboarding should be paused pending material remediation. | Escalate for Review |
| Below 40 | Critical Risk | Vendor has critical security control failures. Onboarding should not proceed without Security Lead and executive approval. | Reject or Escalate |

**Composite Score: ___**
**Risk Rating: ___**

### C.3 Mandatory Risk Flags

The following conditions, regardless of the composite score, require automatic escalation to the Security Lead and cannot be overridden by a favorable composite score:

| Risk Flag | Condition | Flag Triggered? |
|---|---|---|
| Recent Breach | Vendor has experienced a data breach involving customer data in the last 24 months (question 2.9) | Yes / No |
| No MFA | Vendor does not enforce MFA on systems that process ABC Corp data (question 3.1 scored 0) | Yes / No |
| No Encryption at Rest | Vendor does not encrypt ABC Corp data at rest (question 2.2 scored 0) | Yes / No |
| Adverse Audit Finding | Vendor's most recent SOC 2 report has qualified opinion or material exception (question 7.3) | Yes / No |
| No Breach Notification SLA | Vendor has no contractual breach notification commitment (question 6.2 scored 0) | Yes / No |
| Regulatory Action | Vendor has been subject to regulatory enforcement action in the last 3 years (question 1.7) | Yes / No |
| Sub-Processor Opacity | Vendor refuses to disclose sub-processor list (question 8.2 scored 0) | Yes / No |

**Number of Mandatory Risk Flags Triggered: ___**
**Note:** Any mandatory flag requires Security Lead sign-off regardless of the composite score.

### C.4 Assessor Risk Narrative

[Required for all Tier 1 and Tier 2 assessments. The assessor should provide a 3 to 5 paragraph narrative summarizing: the vendor's overall security posture, the key strengths identified, the most significant gaps or concerns, the most material risk flags if any, and the assessor's recommendation.]

---

## 8. Part D: Risk Treatment and Remediation Tracking

### D.1 Risk Treatment Decision

**Risk Treatment Options:**

| Option | When Used | Authority |
|---|---|---|
| Accept | Composite score of 70 or higher, no mandatory risk flags | GRC Analyst can accept on behalf of Security Lead for Tier 3; Security Lead approval required for Tier 1 and Tier 2 |
| Accept with Conditions | Composite score of 55 to 69, or score above 70 with 1 to 2 risk flags | Security Lead approval required |
| Accept with Conditions and Remediation SLA | Composite score of 55 to 69 with specific remediable gaps | Security Lead approval required; remediation tracked in POA&M |
| Escalate for Review | Composite score of 40 to 54, or 3 or more mandatory risk flags | Security Lead must escalate to program owner or executive sponsor |
| Reject | Composite score below 40, or any non-negotiable risk flag (e.g., no encryption at rest) | Security Lead approval required |

**Risk Treatment Decision:** [Select one: Accept / Accept with Documentation / Accept with Conditions / Accept with Conditions and Remediation SLA / Escalate for Review / Reject]

**Basis for Decision:** [Explain the rationale for the risk treatment decision]

**Approval Authority:** [Name and title of individual who approved the risk treatment decision]

**Approval Date:** [Date]

### D.2 Conditions and Remediation Items

If the risk treatment is "Accept with Conditions" or "Accept with Conditions and Remediation SLA," complete this section.

| Item ID | Gap Description | Relevant Question | Remediation Required | Vendor Commitment | SLA Deadline | Status |
|---|---|---|---|---|---|---|
| VR-[ID]-001 | [Description of the security gap] | [Question number] | [What the vendor must do to remediate] | [Vendor's stated remediation commitment] | [Target date] | Open |
| VR-[ID]-002 | [Description] | | | | | |

**Condition for Full Approval:** [Describe what must be true before this vendor is considered fully approved. For example: SOC 2 Type II report received and reviewed, or specific remediation items confirmed complete.]

**Condition Verification Date:** [Date by which conditions must be verified by the GRC Analyst]

### D.3 Ongoing Monitoring Requirements

| Monitoring Activity | Frequency | Responsible Party | Next Due Date |
|---|---|---|---|
| Reassessment (full TPRM assessment) | Per Section 2.3 based on tier | GRC Analyst | [Date based on tier] |
| SOC 2 / ISO 27001 report review (annual audit cycle) | Annual | GRC Analyst | [Date vendor's next audit report is expected] |
| Sub-processor list review | Annual | GRC Analyst | [Date] |
| Vendor security news and breach monitoring | Ongoing | Security Operations Analyst | Continuous |
| Contract renewal security review | At each renewal | GRC Analyst and Business Owner | [Contract renewal date] |

---

## 9. Part E: Assessment Conclusion and Approval

### E.1 Assessment Summary

| Field | Value |
|---|---|
| Vendor Name | [Legal name] |
| Assessment Completion Date | [Date] |
| Assessor | [Name and title] |
| Assigned Tier | [Tier 1 / Tier 2 / Tier 3] |
| Composite Risk Score | [Score] / 100 |
| Risk Rating | [Low / Low-Moderate / Moderate / High / Critical] |
| Mandatory Risk Flags | [Number of flags triggered; list flag names if any] |
| Risk Treatment Decision | [Decision] |
| Conditions (if any) | [Summary of conditions] |
| Next Reassessment Due | [Date] |

### E.2 Signatures

| Role | Name | Signature | Date |
|---|---|---|---|
| GRC Analyst (Assessor) | [Name] | [Signature] | [Date] |
| Business Owner | [Name] | [Signature] | [Date] |
| Security Lead (Approval Authority) | [Name] | [Signature] | [Date] |

### E.3 Document Control

| Field | Value |
|---|---|
| Assessment ID | TPRM-[YYYY]-[NNN] |
| Document Version | 1.0 |
| Supersedes | N/A (initial assessment) or [Previous Assessment ID] |
| Retention Period | 7 years from vendor relationship end date |
| Storage Location | [GRC document repository path] |

---

## 10. Appendix A: Completed Example Assessment (Tenable.io)

This appendix demonstrates a completed assessment for Tenable, Inc. / Tenable.io, one of the active Tier 1 vendors in the ABC Corp cloud environment. This is a representative assessment based on publicly available information and Tenable's published security documentation. It demonstrates how the template is applied in practice.

---

### Completed A.1: Vendor Identification

| Field | Response |
|---|---|
| Vendor Legal Name | Tenable, Inc. |
| Vendor DBA / Brand Name | Tenable.io (cloud platform) |
| Vendor Website | https://www.tenable.com |
| Vendor Headquarters | Columbia, Maryland, USA |
| Service | Tenable.io vulnerability management platform. Scanning 126 assets in the ABC Corp Azure subscription. Tenable.io scans assets and reports discovered vulnerabilities, asset inventory, and risk scores via the cloud platform. |
| Integration Method | Agent-based (Nessus Agent deployed on scanned assets) and credentialed API-based scanning |
| Access Type | Read (vulnerability scan data); agent-based access to asset configurations |
| Data Categories | Vulnerability scan data, asset inventory, configuration data, network topology information |
| Data Residency | United States (Tenable.io cloud platform hosted on AWS US East/West regions) |
| Assigned Tier | Tier 1 (Critical) - ABC Corp security controls (Vulnerability Management Program) depend on this service; agent installed on 126 production assets |

---

### Completed A.2: Tier Classification Rationale

Tenable.io is assigned Tier 1 (Critical) for the following reasons:

1. The Tenable Nessus Agent is installed on all 126 assets in ABC Corp Tenable management scope, giving Tenable agent software execution capability on production systems.
2. Tenable.io processes vulnerability scan data that includes detailed configuration information, open port details, installed software versions, and potential vulnerability details for all scanned assets. This constitutes sensitive security telemetry.
3. The Vulnerability Management Program (POA-004, POA-005) directly depends on Tenable.io for its operational capability. A compromise of the Tenable platform or a malicious Tenable agent update could affect all 126 assets simultaneously.
4. Tenable.io is a cloud-hosted SaaS platform. ABC Corp scan data is transmitted to and stored on Tenable's infrastructure.

---

### Completed Part B Summary: Domain Scores (Representative Assessment)

| Domain | Max Points | Representative Score | Notes |
|---|---|---|---|
| Domain 1: Security Governance | 38 | 34 | SOC 2 Type II and ISO 27001 certified. CISO present. Strong governance posture. Minor gap: vendor's own TPRM program depth not independently verified. |
| Domain 2: Data Protection | 63 | 55 | AES-256 encryption at rest, TLS 1.2 or higher in transit. Customer-managed encryption key option available at higher license tier. Data residency in US by default. Breach notification SLA of 72 hours per DPA. |
| Domain 3: Access Control | 50 | 44 | MFA enforced for customer-facing platform and internal systems per SOC 2 evidence. PAM for administrative access documented. Access review frequency documentation not publicly available. |
| Domain 4: Network Security | 48 | 42 | Annual penetration tests conducted. Network segmentation in place (multi-tenant SaaS). Pen test results available to enterprise customers under NDA. Vulnerability management is Tenable's core product so infrastructure security posture is strong. |
| Domain 5: Application Security | 40 | 35 | SSDLC documented. SAST/DAST in pipeline per SOC 2. Bug bounty program exists. OWASP Top 10 assessment history not independently verified outside SOC 2 scope. |
| Domain 6: Incident Response | 46 | 40 | Incident response program documented in SOC 2. Business continuity plan exists. Breach notification SLA contractually stated. Published SLA of 99.5 percent uptime. |
| Domain 7: Compliance | 37 | 35 | SOC 2 Type II (current, in-scope for Tenable.io). ISO 27001 certified. No qualified opinions in most recent audit review. Compliant with GDPR and CCPA. |
| Domain 8: Supply Chain | 25 | 19 | Subprocessor list available publicly (AWS as primary cloud provider). Advance notice of subprocessor changes not specifically committed contractually in standard agreement. |

**Composite Score (Representative):**
- Domain 1: (34/38) x 12 = 10.7
- Domain 2: (55/63) x 18 = 15.7
- Domain 3: (44/50) x 16 = 14.1
- Domain 4: (42/48) x 14 = 12.3
- Domain 5: (35/40) x 12 = 10.5
- Domain 6: (40/46) x 14 = 12.2
- Domain 7: (35/37) x 8 = 7.6
- Domain 8: (19/25) x 6 = 4.6
- **Composite Score: 87.7 (Low Risk)**

**Mandatory Risk Flags Triggered: 0**

---

### Completed Part D: Risk Treatment (Representative)

**Risk Treatment Decision:** Accept

**Basis for Decision:** Tenable, Inc. demonstrates a strong and mature security posture appropriate for a Tier 1 security tool vendor. SOC 2 Type II and ISO 27001 certifications with no adverse findings provide high confidence in the operational effectiveness of their security controls. The composite score of 87.7 places the vendor in the Low Risk tier with no mandatory risk flags triggered.

**Monitoring Requirements:**
- Annual reassessment (next due March 2027)
- Annual review of Tenable SOC 2 Type II report when issued
- Monitor Tenable security advisories and agent update release notes for any supply-chain risk events
- Confirm Nessus Agent update process: updates should be tested in a non-production asset before broad deployment

---

## 11. Appendix B: Scoring Reference Tables

### Question Scoring Rubric

Use the following rubric to score individual questionnaire responses:

**Full Score:** The vendor provides documented evidence that the control is fully implemented, tested, and operating effectively. Evidence may include SOC 2 control descriptions, audit findings, policy documentation, or direct demonstration.

**75 percent of Max Score:** The vendor provides credible evidence that the control is substantially implemented but not fully mature or tested. Minor gaps are present that do not materially affect risk.

**50 percent of Max Score:** The vendor provides partial evidence that the control is partially implemented. Significant gaps are present that require attention.

**25 percent of Max Score:** The vendor acknowledges the control is planned or in progress but not currently implemented. Compensating controls may exist.

**Zero Score:** The vendor does not have the control, refuses to answer, or the control is clearly ineffective based on available evidence.

### Domain Weight Rationale

| Domain | Weight | Rationale |
|---|---|---|
| Data Protection and Privacy | 18% | Highest weight because data protection failures have direct and immediate impact on individuals and the organization. Encryption, breach notification, and data residency controls are foundational. |
| Access Control and Identity Management | 16% | Second highest because access control failures are the most common initial vector in vendor-related breaches (compromised credentials, excessive access). |
| Incident Response and Business Continuity | 14% | High weight because even a secure vendor will eventually face an incident. How they respond determines the impact on ABC Corp. |
| Network and Infrastructure Security | 14% | Equal weight with IR/BC because infrastructure security determines the attacker's ability to reach ABC Corp data in the first place. |
| Organizational Security Governance | 12% | Governance determines whether all other controls are sustainable. A vendor without governance will eventually fail their technical controls. |
| Application and Software Security | 12% | Critical for SaaS vendors where the application is the primary attack surface. Less critical for hardware or physical service vendors. |
| Compliance and Audit | 8% | Independent verification (SOC 2, ISO 27001) provides evidence that other domain scores are accurate. But compliance without substance is insufficient. |
| Supply Chain and Sub-Processor Risk | 6% | Important but secondary. Sub-processor risk is partially mitigated by contractual requirements on the primary vendor. |

---

## 12. Appendix C: Related Documents and Standards

| Document | Relationship |
|---|---|
| Information Security Policy v1.0 | Parent policy. TPRM program operates under the ISP's vendor management requirements. |
| Configuration Baseline Standard v1.0 | Vendor-operated systems that connect to the ABC Corp cloud environment must meet or exceed the CBS requirements. |
| Data Processing Agreement Template | The DPA executed with each Tier 1 and Tier 2 vendor. |
| Incident Response Plan v1.0 | Third-party incidents trigger the IRP. Vendor breach notification SLAs feed into the IRP timeline. |
| Risk Register | Each vendor assessment result (and any residual risks from accepted vendor relationships) is recorded in the Risk Register. |
| Plan of Action and Milestones | Vendor remediation commitments from "Accept with Conditions" decisions may be tracked as POA&M items. |

**Standards and Frameworks Referenced:**
- NIST Special Publication 800-161 Rev 1 (Cybersecurity Supply Chain Risk Management)
- NIST Special Publication 800-53 Rev 5 (SA-9: External System Services; SR-2: Supply Chain Risk Management Plan)
- ISO/IEC 27001:2022 Annex A 5.19, 5.20, 5.21, 5.22 (Supplier relationships and information security)
- NIST Cybersecurity Framework 2.0 (GV.SC: Cybersecurity Supply Chain Risk Management)
- CIS Controls v8 Control 15 (Service Provider Management)
- SOC 2 Trust Services Criteria (as a reference for evaluating vendor audit reports)
- Cloud Security Alliance CAIQ (Cloud Controls Matrix) (reference for cloud vendor assessments)

---

*End of Document*

*This Third-Party Risk Assessment Template was developed for the ABC Corp Azure shared tenant based on the vendor landscape confirmed during the March 2026 gap assessment. The current Tier 1 vendor registry includes Microsoft Corporation (Azure platform) and Tenable, Inc. (Tenable.io vulnerability management). The completed example assessment in Appendix A demonstrates how the template is applied to a real vendor in the ABC Corp cloud environment.*
