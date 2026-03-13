# Information Security Policy

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity and Access Governance Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Review Cadence:** Annual (next review due March 2027)

> **Disclaimer:** This is a sanitized version of an Information Security Policy produced following a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This policy was produced as a direct deliverable from a real, evidence-based gap assessment of a live Microsoft Azure shared tenant. All policy statements cite actual findings from that assessment, including specific telemetry counts, Tenable scan results, and Entra ID exports gathered at the time of assessment. The policy is not a template -- it is a response to documented deficiencies in a production environment.

**Driving Finding:** PM gap -- no formal information security policy existed at the program level. The gap assessment rated the Program Management (PM) control family at Gap Rating 1 (Not Implemented) and the NIST CSF 2.0 Govern function at Tier 1 (Partial), identifying the absence of a formal security policy as a critical foundational deficiency.

**Assessment Standards Applied:** NIST SP 800-53 Rev 5 | NIST CSF 2.0 | ISO/IEC 27001:2022 | SOC 2 TSC

---

## Table of Contents

1. Purpose
2. Scope
3. Roles and Responsibilities
4. Policy Statements
   - 4.1 Access Control and Identity Management
   - 4.2 Authentication Requirements
   - 4.3 Vulnerability and Patch Management
   - 4.4 Incident Response
   - 4.5 Audit Logging and Monitoring
   - 4.6 Configuration Management
   - 4.7 Data Classification and Protection
   - 4.8 Third-Party and Supplier Security
   - 4.9 Security Awareness and Training
   - 4.10 Business Continuity and Recovery
5. Acceptable Use
6. Consequences of Violation
7. Policy Review and Maintenance
8. Related Documents and References
9. Approval and Sign-Off

---

## 1. Purpose

The purpose of this Information Security Policy is to establish ABC Corp's commitment to protecting the confidentiality, integrity, and availability of its information systems, data, and technology infrastructure. This policy defines the minimum security requirements applicable to all users, systems, and services operating within the ABC Corp cloud environment.

This policy is grounded in evidence from a real gap assessment, which identified 20 open findings across four security frameworks. The most critical of these findings -- including 83,600 single-factor sign-in events out of 203,906 total, 185 critical-severity vulnerabilities with zero remediations, five permanent Global Administrator accounts with no Privileged Identity Management, and the complete absence of a formal security program -- establish the urgency and necessity of this document.

This policy provides the foundational governance layer from which all subordinate standards, procedures, and guidelines derive their authority.

---

## 2. Scope

This policy applies to:

**Systems and Infrastructure:** All systems, virtual machines, cloud resources, and services hosted within or connected to the ABC Corp cloud tenant, including all resources managed through Microsoft Entra ID, Microsoft 365, Azure, and any additional cloud or SaaS platforms onboarded in future.

**Users:** All individuals with access to ABC Corp systems, including administrators, staff, students, guests, and any third parties with delegated or temporary access. This policy applies regardless of whether access is direct, remote, or facilitated through a third-party service.

**Data:** All data created, processed, stored, or transmitted within the ABC Corp environment, including user data, configuration data, security telemetry, and administrative records.

**Out of Scope:** Physical datacenter infrastructure operated by the cloud provider. Physical security controls are inherited from the cloud provider's ISO 27001-certified datacenter operations and are not separately governed by this policy.

---

## 3. Roles and Responsibilities

### 3.1 Cybersecurity Owner / Security Lead

The named cybersecurity owner holds overall accountability for the information security program. Responsibilities include: approving and maintaining this policy, communicating security risk posture to leadership, overseeing remediation of assessment findings, and ensuring this policy is reviewed annually.

### 3.2 GRC Analyst

The GRC Analyst is responsible for: conducting periodic gap assessments and risk assessments, maintaining the risk register and Plan of Action and Milestones (POA&M), producing and updating security documentation, and tracking remediation milestone dates for all open findings.

### 3.3 Identity Administrator

The Identity Administrator is responsible for: managing user accounts and role assignments in Microsoft Entra ID, enforcing MFA and Conditional Access policies, maintaining the provisioning and deprovisioning process for all accounts, and conducting access reviews on the cadence defined in Section 4.1.

### 3.4 Cloud / Infrastructure Administrator

The Cloud Administrator is responsible for: maintaining security baseline configuration of all cloud resources, implementing and monitoring network security group rules, enabling and reviewing security telemetry in the SIEM, and managing backup configuration and recovery testing.

### 3.5 Vulnerability Management

The Vulnerability Management function is responsible for: operating the vulnerability scan program, triaging and assigning remediation ownership for all identified findings, tracking remediation against SLAs defined in Section 4.3, and reporting vulnerability metrics to leadership monthly.

### 3.6 SOC Analyst

The SOC Analyst is responsible for: reviewing security alerts and incidents generated by the SIEM, conducting triage of high-volume alert types, escalating confirmed incidents per the Incident Response Plan, and maintaining audit log review on the cadence defined in Section 4.5.

### 3.7 All Users

All users are responsible for: complying with acceptable use requirements in Section 5, completing security awareness training within 30 days of onboarding and annually thereafter, reporting suspected security incidents to the Security Lead or SOC Analyst, and not sharing credentials or bypassing authentication controls.

---

## 4. Policy Statements

### 4.1 Access Control and Identity Management

**4.1.1** All user accounts must be provisioned through the formal identity lifecycle process. Account creation requires a defined business justification, a named account owner, and completion of all mandatory directory attributes including Department, Job Title, and Manager.

**Driving Evidence (real assessment finding):** 100% of 2,315 user accounts had blank Department fields at time of assessment, preventing access reviews and role-based access management.

**4.1.2** Access must be granted on the principle of least privilege. Users must be assigned only the permissions required to perform their defined role. Privileged roles must be justified, documented, and reviewed quarterly.

**4.1.3** The number of Global Administrator accounts must not exceed two. Any account holding the Global Administrator role must be documented with named ownership, business justification, and activation controls per Section 4.2.

**Driving Evidence (real assessment finding):** Five permanent Global Administrator accounts were identified with no PIM, no just-in-time access, and no approval workflow.

**4.1.4** Access reviews must be conducted quarterly for privileged accounts and annually for all standard user accounts. Accounts that cannot be matched to an active user or documented business requirement must be disabled within 5 business days and deleted within 30 days.

**4.1.5** Guest accounts must be inventoried, justified, and reviewed quarterly. Guest accounts must not hold privileged roles without documented exception approval from the Security Lead.

**4.1.6** Accounts with repeated failed sign-in attempts exceeding 5 failures within any 24-hour period must be flagged for investigation. Accounts with more than 20 failures must be temporarily suspended pending review.

**Driving Evidence (real assessment finding):** 30 accounts were identified with more than 5 failed sign-in attempts, including one account with 512 failures from an external domain with no lockout response observed.

### 4.2 Authentication Requirements

**4.2.1** Multi-factor authentication (MFA) is required for all user accounts without exception. Single-factor authentication is not permitted for any account with access to the environment.

**Driving Evidence (real assessment finding):** 83,600 of 203,906 sign-in events (41%) completed on single-factor authentication, with zero Conditional Access policies enforced at time of assessment.

**4.2.2** Conditional Access policies must be configured and enforced in Microsoft Entra ID. At minimum, one baseline policy must require MFA for all sign-ins and block legacy authentication protocols.

**4.2.3** Privileged Access Management must be implemented for all accounts holding directory roles. Privileged role activations must use just-in-time access with a defined maximum session duration. Permanent active role assignments are not permitted except for break-glass accounts.

**Driving Evidence (real assessment finding):** PIM was not configured at time of assessment. Zero eligible assignments existed. All five Global Administrator assignments were permanent and active.

**4.2.4** Password complexity and expiration requirements must be documented and enforced. Minimum requirements: 12 characters, complexity enforced (uppercase, lowercase, number, special character), no reuse of the previous 10 passwords.

**4.2.5** Legacy authentication protocols must be blocked at the Conditional Access layer. Exceptions require written approval from the Security Lead and a documented compensating control.

### 4.3 Vulnerability and Patch Management

**4.3.1** All assets within scope must be included in the authenticated vulnerability scan program. Unauthenticated scanning alone is not acceptable as the standard method.

**4.3.2** Vulnerability scans must be executed on a scheduled, recurring basis. Ad hoc or manual-only scanning is not permitted as the standard operating cadence.

**Driving Evidence (real assessment finding):** Scan schedule was confirmed as Ad Hoc/Manual at time of assessment, with no defined cadence.

**4.3.3** Remediation of identified vulnerabilities must comply with the following SLA schedule:

| Severity | Remediation SLA |
|---|---|
| Critical | 15 calendar days from identification |
| High | 30 calendar days from identification |
| Medium | 90 calendar days from identification |
| Low | 180 calendar days from identification |

**Driving Evidence (real assessment finding):** 185 Critical and 391 High findings were identified across 126 assets with zero remediations and no SLA framework in place at time of assessment.

**4.3.4** Compliance and configuration scanning (DISA-STIG or CIS Benchmark) must be executed at minimum quarterly. Results must be documented and tracked through the POA&M.

**Driving Evidence (real assessment finding):** A DISA-STIG compliance scan configuration existed but had never been executed. Status was Empty at time of assessment.

**4.3.5** Exceptions to remediation SLAs must be submitted to the Security Lead in writing, include a risk acceptance rationale, and be reviewed no less than every 30 days until resolved.

### 4.4 Incident Response

**4.4.1** A formal Incident Response Plan must be maintained and approved by the Security Lead. The plan must be reviewed annually and tested through tabletop exercise at minimum once per year.

**Driving Evidence (real assessment finding):** No formal Incident Response Plan existed at time of assessment despite 50 active alert types and 24,905 PowerShell encoded command alerts recorded in the SIEM.

**4.4.2** All security incidents must be classified using the following severity definitions:

| Severity | Definition |
|---|---|
| Critical | Active exploitation, confirmed breach, or compromise of a privileged account |
| High | Credible threat indicator requiring containment within 4 hours |
| Medium | Suspicious activity requiring investigation within 24 hours |
| Low | Informational event requiring review within 72 hours |

**4.4.3** Incidents must be logged in the incident tracking system with status transitions documented. Post-incident reviews must be conducted for all Critical and High severity incidents within 5 business days of closure.

**4.4.4** The Security Lead must be notified of all Critical and High severity incidents within 1 hour of detection.

**4.4.5** High-volume alert types not triaged within 72 hours of first occurrence must be escalated to the Security Lead for prioritization decision.

### 4.5 Audit Logging and Monitoring

**4.5.1** Audit logging must be enabled for all identity and access events, including user creation and deletion, role assignment changes, password resets, MFA registration changes, and service principal modifications.

**4.5.2** Log data must be retained for a minimum of 90 days in the SIEM with at least 1 year of archived retention. Log data must not be modified or deleted during the retention period.

**4.5.3** Security alerts must be reviewed by the SOC Analyst on a minimum weekly basis. High and Critical severity alerts must be reviewed within 24 hours of generation.

**Driving Evidence (real assessment finding):** 30+ days of audit log data was present in the SIEM with no documented review cadence and 24,905 high-volume PowerShell alerts generating no formal triage response.

**4.5.4** Failed sign-in patterns must be monitored using defined detection rules. Accounts exceeding the thresholds in Section 4.1.6 must be reviewed within 24 hours.

**4.5.5** Endpoint protection telemetry must be integrated into the SIEM. Gaps in endpoint protection coverage must be reported and tracked through the POA&M.

**Driving Evidence (real assessment finding):** The endpoint protection status table returned no data at time of assessment, meaning protection posture across all 126 scanned assets was entirely unknown.

### 4.6 Configuration Management

**4.6.1** A documented configuration baseline must exist for each OS type present in the environment. Baselines must reference CIS Benchmark Level 1 or DISA STIG as the source standard.

**4.6.2** The target configuration baseline pass rate is 90% per OS type. Environments below this threshold must have an active remediation plan tracked through the POA&M.

**Driving Evidence (real assessment finding):** The security baseline SIEM table returned no data at time of assessment. No OS-level baseline compliance data was available.

**4.6.3** Configuration compliance scans must be executed at minimum quarterly. Results must be documented and tracked.

**4.6.4** A formal asset inventory must be maintained, reconciling scan-discovered assets against directory-registered devices. The inventory must be reviewed and updated monthly.

### 4.7 Data Classification and Protection

**4.7.1** A data classification policy must be established and maintained. At minimum, data must be classified into: Confidential (access restricted to named individuals), Internal (accessible to authenticated users within scope), and Public (no access restriction required).

**4.7.2** Data Loss Prevention controls must be implemented, aligned to the classification tiers in 4.7.1. DLP policy exceptions must be documented and approved by the Security Lead.

**4.7.3** User data must be handled in compliance with all applicable privacy regulations. The Security Lead is responsible for identifying applicable legal and regulatory requirements annually.

### 4.8 Third-Party and Supplier Security

**4.8.1** All cloud and SaaS services in use must be formally inventoried. The inventory must include: service name, data classification of hosted data, applicable security controls, contract renewal date, and named security contact.

**Driving Evidence (real assessment finding):** Four cloud and SaaS services were in active use with no formal inventory, no contractual security requirements, and no third-party monitoring process at time of assessment.

**4.8.2** Agreements with third-party providers must include minimum security requirements covering: data handling obligations, incident notification timelines (maximum 72 hours), access control requirements, and the right to audit or request compliance evidence.

**4.8.3** Third-party security posture must be reviewed annually and tracked through the risk register.

**4.8.4** New third-party services must undergo a security assessment before onboarding. The assessment must be approved by the Security Lead.

### 4.9 Security Awareness and Training

**4.9.1** All users must complete security awareness training within 30 days of account creation and annually thereafter.

**4.9.2** Training completion must be tracked and reported to the Security Lead quarterly. Minimum acceptable completion rate is 90% of active users per reporting period.

**Driving Evidence (real assessment finding):** A security awareness training program was confirmed in place but completion was not tracked, providing no evidence of effectiveness.

**4.9.3** Training content must be reviewed and updated annually. Minimum topics: phishing and social engineering, credential security and MFA, incident reporting procedures, and acceptable use requirements.

**4.9.4** Users with privileged access must complete additional role-based training covering privileged account responsibilities, PIM activation procedures, and insider threat awareness.

### 4.10 Business Continuity and Recovery

**4.10.1** Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO) must be defined and documented for all business-critical systems and data stores.

**Driving Evidence (real assessment finding):** Cloud backup was confirmed enrolled but no RTO or RPO had been documented and no recovery plan testing had been performed at time of assessment.

**4.10.2** A formal recovery plan must be maintained and reviewed annually. The plan must document backup configuration, restoration sequencing, responsible parties, and stakeholder communication procedures.

**4.10.3** Recovery procedures must be tested at minimum once per year. Results must be documented and gaps tracked through the POA&M.

---

## 5. Acceptable Use

All users accessing ABC Corp systems are bound by the following requirements:

**5.1** Systems and accounts are provided for authorized business purposes only. Use for personal gain or activities unrelated to the organization's mission is prohibited.

**5.2** Users must not share credentials, authentication tokens, or session cookies with any other individual. Each user is responsible for all activity performed under their account.

**5.3** Users must not attempt to access systems, data, or accounts beyond their explicitly authorized scope. Accidental access to out-of-scope data must be reported to the Security Lead within 24 hours.

**5.4** Users must not disable, bypass, or interfere with any security control, including MFA prompts, endpoint protection agents, or network security rules, unless explicitly authorized by the Security Lead.

**5.5** Users must not install unauthorized software on any system. Software installation requests must be submitted to the Cloud Administrator for review.

**5.6** Users must report any suspected security incident, policy violation, or unusual system behavior promptly. There is no penalty for good-faith reporting.

**5.7** Users must complete all required security awareness training within the timeframes defined in Section 4.9. Failure to complete training may result in account suspension pending completion.

---

## 6. Consequences of Violation

**6.1** Violations of this policy may result in disciplinary action including suspension or revocation of system access and referral to appropriate institutional or legal authorities depending on the nature and severity of the violation.

**6.2** Violations will be assessed based on: intent, scope of impact, history of prior violations, and cooperation during investigation.

**6.3** The Security Lead is responsible for investigating suspected violations. Investigations will preserve evidence and protect the rights of all parties involved.

**6.4** The following violations are considered severe and may result in immediate access revocation:

- Intentional sharing of privileged credentials
- Deliberate bypass of authentication controls
- Unauthorized access to or exfiltration of data
- Introduction of malware or malicious code
- Interference with security monitoring systems

---

## 7. Policy Review and Maintenance

**7.1** This policy must be reviewed annually by the Security Lead and GRC Analyst. The review must assess whether policy statements remain appropriate given changes to the environment, threat landscape, or applicable regulatory requirements.

**7.2** The policy must also be reviewed following: a Critical or High severity security incident, a significant change to the technology environment, a change in applicable legal or regulatory requirements, or identification of a material gap through a formal assessment.

**7.3** All revisions must be version-controlled. Version history must be maintained for a minimum of 3 years. Material changes must be communicated to all users within 30 days of approval.

**7.4** This policy is effective as of March 5, 2026. Next scheduled review date: March 5, 2027.

---

## 8. Related Documents and References

| Document | Status | Purpose |
|---|---|---|
| Plan of Action and Milestones (POA&M) | Active | Tracks remediation of all open findings that gave rise to this policy |
| Risk Register | Pending | Formalizes risks identified in the gap assessment |
| Incident Response Plan | Pending | Operationalizes Section 4.4 of this policy |
| Access Control Policy | Pending | Provides detailed requirements for Sections 4.1 and 4.2 |
| Privileged Access Management Standard | Pending | Provides detailed requirements for Section 4.2.3 |
| Vulnerability Management Policy | Pending | Provides detailed requirements for Section 4.3 |
| Security Monitoring Policy | Pending | Provides detailed requirements for Section 4.5 |
| System Security Plan | Pending | Documents inherited and implemented controls per NIST PL-2 |

**Frameworks Referenced:**

- NIST Special Publication 800-53 Revision 5
- NIST Cybersecurity Framework 2.0
- ISO/IEC 27001:2022
- SOC 2 Trust Services Criteria

---

## 9. Approval and Sign-Off

| Role | Name | Signature | Date |
|---|---|---|---|
| Security Lead / Cybersecurity Owner | [To be completed] | [Signature required] | March 5, 2026 |
| GRC Analyst | Alex Truong | [Signature required] | March 5, 2026 |
| Leadership / Management Sponsor | [To be completed] | [Signature required] | March 5, 2026 |

**Policy Effective Date:** March 5, 2026
**Next Review Date:** March 5, 2027
**Version:** 1.0
**Classification:** CONFIDENTIAL

---

*End of Document*

*Organization: ABC Corp | Microsoft Entra ID Tenant. This policy was produced as a direct output of a real, evidence-based security gap assessment conducted against a live Azure cloud environment. All organization names and identifiers have been sanitized for portfolio publication. Policy statements and driving evidence citations reflect actual findings from the live assessment.*
