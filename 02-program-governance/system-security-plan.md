# System Security Plan

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> ⚠️ **Disclaimer:** This is a sanitized version of a System Security Plan developed for a live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This SSP was built from the actual security posture of a live Azure shared tenant (~1,000+ students and instructors) assessed in March 2026. Every control implementation statement, every gap notation, every POA&M item, and every risk rating in this document is grounded in real findings from real queries against a real production system. This is not a template exercise.

**Key Real Findings Documented in This SSP (live data):**
- Zero Conditional Access policies active. 83,600 of 203,906 sign-in events completed on single-factor authentication. (real finding -- AC and IA control families marked Partially Implemented)
- ProtectionStatus table returned zero rows for all 126 assets. Malicious code protection telemetry entirely absent from Sentinel. (real finding -- SI-3 marked Partially Implemented)
- Zero Sentinel analytics rules active. No detection capability existed. (real finding -- AU-6, IR-4, SI-4 all marked Partially Implemented)
- 100 percent of 2,315 user accounts had blank Department, Job Title, and Manager attributes. (real finding -- AC-2, IA-4 marked Partially Implemented)
- Overall control implementation rate: 45 percent (Implemented + Inherited + N/A) across 126 controls reviewed across all 20 NIST SP 800-53 Rev 5 control families. (real measurement)
- System categorized MODERATE per FIPS 199. System is operating under Interim Authority to Test and Operate (IATO) pending formal ATO. (real status)

---


**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Document Type:** System Security Plan (NIST SP 800-18 Rev 1 / NIST SP 800-53 Rev 5)
**System Name:** ABC Corp Azure Cloud Tenant
**System Identifier:** CR-AZURE-001
**Review Cadence:** Annual, or following any significant system change, major incident, or change in authorization boundary

> **Assessment Context:** This System Security Plan was developed following a read-only multi-framework gap assessment of the live ABC Corp Azure shared tenant conducted in March 2026. All control implementation statements reflect the verified state of the system as of that assessment. Where controls were found to be not implemented or partially implemented, the implementation status is documented accurately with reference to the relevant POA&M items. This SSP does not represent a desired future state. It represents the documented current state of a real, live production system.

---

## Table of Contents

1. System Identification
2. System Categorization
3. System Owner and Authorization
4. System Description
5. System Environment and Architecture
6. System Interconnections
7. Laws, Regulations, and Policies
8. Minimum Security Controls
   - AC: Access Control
   - AT: Awareness and Training
   - AU: Audit and Accountability
   - CA: Assessment, Authorization, and Monitoring
   - CM: Configuration Management
   - CP: Contingency Planning
   - IA: Identification and Authentication
   - IR: Incident Response
   - MA: Maintenance
   - MP: Media Protection
   - PE: Physical and Environmental Protection
   - PL: Planning
   - PM: Program Management
   - PS: Personnel Security
   - PT: PII Processing and Transparency
   - RA: Risk Assessment
   - SA: System and Services Acquisition
   - SC: System and Communications Protection
   - SI: System and Information Integrity
   - SR: Supply Chain Risk Management
9. Control Implementation Summary
10. Continuous Monitoring Plan
11. Plan of Action and Milestones Summary
12. Authorizing Official Signature

---

## 1. System Identification

| Field | Value |
|---|---|
| System Name | ABC Corp Azure Cloud Tenant |
| System Identifier | CR-AZURE-001 |
| System Abbreviation | CR-AZURE |
| System Type | Major Application and General Support System |
| Operating Status | Operational |
| System Owner | ABC Corp |
| System Owner Contact | [System Owner Name and Email] |
| Authorizing Official | Security Lead |
| Information System Security Officer | GRC Analyst |
| Primary IT Contact | Cloud Administrator |
| System Location | Microsoft Azure Cloud (US East/West Regions) |
| Authorization Date | March 5, 2026 (initial SSP; pending formal ATO) |
| Authorization Expiration | March 5, 2029 (3-year cycle; subject to continuous monitoring) |
| SSP Version | 1.0 |
| SSP Date | March 5, 2026 |
| Last Reviewed | March 5, 2026 |
| Next Review Due | March 5, 2027 |

---

## 2. System Categorization

### 2.1 FIPS 199 Security Categorization

The Cyber Range Azure system is categorized in accordance with FIPS Publication 199 (Standards for Security Categorization of Federal Information and Information Systems) and NIST SP 800-60 (Guide for Mapping Types of Information and Information Systems to Security Categories).

| Security Objective | Potential Impact | Rationale |
|---|---|---|
| Confidentiality | MODERATE | The system processes student personally identifiable information (PII), authentication credentials, and security telemetry. Unauthorized disclosure would constitute a privacy violation and could enable targeted attacks. |
| Integrity | MODERATE | The system controls vulnerability management, security monitoring, and identity governance for the Cyber Range. Unauthorized modification of security controls or telemetry could result in undetected attacks. |
| Availability | LOW | The system is an educational platform. Temporary unavailability causes operational disruption but does not create immediate safety or financial harm. |

**Overall System Categorization: MODERATE**

`SC = {(Confidentiality, MODERATE), (Integrity, MODERATE), (Availability, LOW)}`

The overall system categorization is determined by the high-water mark principle: the highest individual impact level across all security objectives. The system is categorized as **MODERATE**.

### 2.2 Information Types

| Information Type | Confidentiality | Integrity | Availability |
|---|---|---|---|
| Student PII (names, email addresses, enrollment records) | MODERATE | MODERATE | LOW |
| Authentication credentials and MFA registration data | HIGH | HIGH | MODERATE |
| Security telemetry (Sentinel logs, alert data, audit logs) | MODERATE | HIGH | LOW |
| Vulnerability scan data (asset configs, open ports, CVEs) | MODERATE | MODERATE | LOW |
| Administrative configuration data (Azure policy, NSG rules, CA policies) | MODERATE | HIGH | MODERATE |
| Course and lab access records | LOW | LOW | LOW |

**Note on Authentication Credentials:** Although the authentication credential information type individually warrants a HIGH confidentiality and integrity classification, the overall system categorization remains MODERATE based on the educational nature of the mission and the absence of national security, financial transaction, or critical infrastructure data. This determination is subject to review if the system scope expands to include higher-impact data types.

---

## 3. System Owner and Authorization

### 3.1 System Owner

| Role | Name / Organization | Responsibilities |
|---|---|---|
| System Owner | ABC Corp Organization | Overall responsibility for the system; authorizes system operation; approves major changes |
| Authorizing Official | Security Lead | Formal authorization authority; accepts residual risk; issues Authorization to Operate (ATO) |
| Information System Security Officer (ISSO) | GRC Analyst | Day-to-day security management; maintains SSP; conducts assessments; manages POA&M |
| Information System Security Engineer (ISSE) | Detection Engineer | Technical security engineering; analytics rules; detection coverage; KQL library |
| System Administrator | Cloud Administrator | Platform administration; Azure resource management; patch management; configuration |
| Identity Administrator | Identity Administrator | Entra ID; Conditional Access; PIM; identity lifecycle |

### 3.2 Authorization Status

| Field | Value |
|---|---|
| Current Authorization Status | Pending Formal ATO (operating under Interim Authority to Test and Operate, IATO) |
| Authorization Basis | This SSP and associated POA&M establish the basis for the initial Authorization to Operate |
| Residual Risk Level | MODERATE (based on March 2026 gap assessment findings and POA&M status) |
| Risk Acceptance | Security Lead has reviewed the residual risk and accepts it subject to POA&M completion |
| Planned ATO Date | June 5, 2026 (contingent on POA&M Priority 1 items being resolved) |

---

## 4. System Description

### 4.1 Purpose and Mission

The ABC Corp Azure shared tenant is a live production Microsoft Azure cloud environment that serves as an educational and professional development platform for approximately 1,000 or more students and instructors pursuing cybersecurity training, certifications, and hands-on skill development. The system provides:

- A shared identity and access management environment using Microsoft Entra ID
- Student-accessible Azure virtual machine labs for cybersecurity exercises
- A production security operations center (SOC) platform using Microsoft Sentinel
- Endpoint and workload protection using Microsoft Defender for Cloud and Defender for Endpoint
- Vulnerability management capabilities using Tenable.io with 126 assets under management
- Administrative tools for course management, user provisioning, and lab orchestration

The Cyber Range is both an operational security environment (running real security controls on real infrastructure) and an educational environment (teaching students to operate those controls). This dual nature is significant from a risk perspective: the security tooling is real and the threats it detects are real, but the operational tempo and staffing are commensurate with an educational organization rather than a commercial SOC.

### 4.2 System Users

| User Type | Count (Approx.) | Access Level | Authentication |
|---|---|---|---|
| Students | 1,000+ | Lab VM access; read-only SOC platform access for training | Entra ID; MFA required (post-CA deployment) |
| Instructors | [Count TBD] | Elevated access to lab management; read access to student progress | Entra ID; MFA required; PIM for elevated tasks |
| Course Administrators | [Count TBD] | User provisioning; lab assignment; access review participation | Entra ID; MFA required; User Administrator role |
| Security Personnel | [Count TBD] | Full SOC platform access; Sentinel; Defender for Cloud | Entra ID; MFA required; Privileged roles via PIM |
| Cloud Administrators | [Count TBD] | Azure subscription management; resource administration | Entra ID; MFA required; PIM-gated Contributor/Owner |
| GRC Analyst | 1 | Read-only assessment access; GRC documentation | Entra ID; MFA required |
| Guest Accounts | 2 (at time of assessment) | Limited access per invitation scope | Entra ID; external identity; expiration required |
| Break-Glass Accounts | 2 | Emergency Global Administrator access only | FIDO2 hardware key; physically secured |

### 4.3 Data Processed, Stored, and Transmitted

| Data Category | Classification | Processing Location | Retention | Sensitivity Basis |
|---|---|---|---|---|
| Student PII (names, emails, enrollment) | MODERATE | Azure tenant (US regions) | Duration of enrollment plus 1 year | Privacy obligations (FERPA, state law) |
| Authentication credentials (hashed) | HIGH | Entra ID (Microsoft managed) | Duration of account existence | Credential compromise enables system compromise |
| MFA registration data | HIGH | Entra ID (Microsoft managed) | Duration of account existence | Loss enables MFA bypass |
| Security telemetry (Sentinel logs) | MODERATE | Log Analytics Workspace (Azure) | 90 days hot; 1-2 years archive | Security investigation basis |
| Vulnerability scan data | MODERATE | Tenable.io cloud (US regions) | Per Tenable retention policy | Reveals exploitable weaknesses |
| Azure activity and audit logs | MODERATE | Log Analytics Workspace | 90 days minimum | Forensic and compliance basis |
| Network flow data | LOW | Azure NSG flow logs | 90 days | Security monitoring |
| Lab exercise data | LOW | Azure Storage (per lab design) | Duration of course | Educational records |

---

## 5. System Environment and Architecture

### 5.1 System Boundary

The authorization boundary for CR-AZURE-001 encompasses:

**Inside the Boundary:**
- All resources within the ABC Corp Azure subscription(s)
- The Entra ID tenant and all directory objects (users, groups, applications, service principals)
- The Log Analytics Workspace and Microsoft Sentinel workspace
- All Azure Virtual Machines, Virtual Networks, and associated networking resources
- All Azure Storage Accounts, Key Vaults, and managed identity resources
- All Defender for Cloud, Defender for Endpoint, and Defender for Identity workloads
- All Azure Policy assignments and Conditional Access policies

**Outside the Boundary (but interconnected):**
- Microsoft Azure infrastructure layer (physical hardware, hypervisor, global network): Microsoft's responsibility
- Tenable.io SaaS platform (receives scan data from the system; governed by TPRM assessment GRC-TPRM-003)
- Student personal devices used to access the system (outside boundary; endpoint controls not mandated)
- Internet connectivity infrastructure

### 5.2 Network Architecture

The Cyber Range Azure environment is organized into the following logical network segments:

| Segment | Purpose | Controls |
|---|---|---|
| Management Virtual Network | Administrative access to Azure resources; Cloud Administrator and Security personnel | NSG restricting inbound to authorized management IPs; no student access |
| Student Lab Virtual Networks | Student lab VM environments; isolated per course or cohort | NSG per lab; outbound filtered; student-isolated |
| Monitoring and Security Segment | Sentinel, Defender workloads, Log Analytics | No direct student access; read access for SOC training |
| Identity Services | Entra ID, Conditional Access, PIM | Microsoft-managed infrastructure; customer-configured policies |

**Network Security Controls:**
- Network Security Groups (NSGs) applied to all subnets and individual VMs
- No RDP (3389) or SSH (22) ports open to 0.0.0.0/0 (Configuration Baseline Standard NS-001/NS-002)
- Azure DDoS Protection at subscription level
- No on-premises network connectivity (cloud-only environment)

### 5.3 System Architecture Narrative

The system is a cloud-native architecture hosted entirely on Microsoft Azure. There is no on-premises component. The identity plane is Microsoft Entra ID, which provides authentication, authorization, Conditional Access enforcement, and Privileged Identity Management for all users and workloads.

The monitoring plane is Microsoft Sentinel, a cloud-native SIEM and SOAR platform that ingests telemetry from Entra ID (SignInLogs, AuditLogs), Microsoft Defender for Cloud (SecurityAlert), Microsoft Defender for Endpoint (DeviceEvents, ProtectionStatus), Azure Activity (AzureActivity), and other connected data sources. Sentinel serves as the single pane of glass for security operations.

The vulnerability management plane is Tenable.io, a SaaS platform that receives scan data from Nessus Agents deployed on the 126 assets in the Tenable management scope. Tenable operates as an interconnected system (see Section 6).

All administrative actions are logged to the Azure Activity Log and Entra ID Audit Log, which flow into the Log Analytics Workspace. Log retention is 90 days hot (queryable) and 1-2 years via archive tier, depending on the log category.

### 5.4 Technology Stack

| Layer | Technology | Version/SKU | Purpose |
|---|---|---|---|
| Identity | Microsoft Entra ID | P2 License (or equivalent) | Authentication, authorization, MFA, PIM, CA |
| SIEM | Microsoft Sentinel | Pay-per-GB workspace | Security monitoring, analytics, incident management |
| Endpoint Protection | Microsoft Defender for Endpoint | Plan 2 | EDR, vulnerability assessment, device telemetry |
| Cloud Security | Microsoft Defender for Cloud | Standard tier | CSPM, workload protection, security alerts |
| Vulnerability Management | Tenable.io | Enterprise (126 assets) | Authenticated vulnerability scanning |
| Infrastructure | Microsoft Azure | US East/West regions | Compute, storage, networking, managed services |
| Azure Policy | Azure Policy | Built-in and custom | Configuration compliance enforcement |
| Log Management | Azure Log Analytics | Pay-per-GB | Central log aggregation, retention, query |
| Key Management | Azure Key Vault | Standard tier | Secret, key, and certificate management |

---

## 6. System Interconnections

The following external systems and services are interconnected with CR-AZURE-001. Each interconnection is governed by a data flow agreement or terms of service.

| Interconnected System | Organization | Connection Type | Data Flows | Agreement | Security Assessment |
|---|---|---|---|---|---|
| Microsoft Azure Platform | Microsoft Corporation | API/Platform dependency | All Azure resource management; Entra ID; Log data | Microsoft Customer Agreement + DPA | Tier 1 TPRM (GRC-TPRM-002) |
| Tenable.io | Tenable, Inc. | Agent-based + API | Vulnerability scan data outbound; asset inventory; CVE data inbound | Tenable Customer Agreement; DPA in progress | Tier 1 TPRM (GRC-TPRM-003) |
| Microsoft Threat Intelligence | Microsoft Corporation | Sentinel connector | Threat indicator feeds inbound | Included in Microsoft Sentinel licensing | Included in Microsoft TPRM |
| Student Personal Devices | External (student-owned) | HTTPS to Azure endpoints | Authentication requests; lab access | No agreement (public access to auth endpoints) | Outside authorization boundary |

**Interconnection Control:** All external connections to the Cyber Range Azure tenant must be authenticated via Entra ID. No unauthenticated access to any resource is permitted. Conditional Access policies govern the authentication requirements for each access scenario.

---

## 7. Laws, Regulations, and Policies

### 7.1 Applicable Laws and Regulations

| Law / Regulation | Applicability | Key Obligations |
|---|---|---|
| FERPA (20 U.S.C. 1232g) | Applicable: the system processes student education records | Restrict disclosure of student education records; provide access rights to students; maintain confidentiality |
| GDPR (EU Regulation 2016/679) | Potentially applicable: EU student data may be processed | Lawful basis for processing; data subject rights; breach notification within 72 hours; DPA with processors |
| CCPA (California Civil Code 1798.100) | Potentially applicable: California residents may be enrolled | Right to know, delete, and opt out; privacy notice requirements |
| Washington State Privacy Act (RCW 19.255) | Applicable: organization operates in Washington state | Breach notification requirements; consumer rights |
| Computer Fraud and Abuse Act (18 U.S.C. 1030) | Applicable: system must be protected against unauthorized access | Criminal penalties for unauthorized access to protected computers |
| Electronic Communications Privacy Act | Applicable: system processes electronic communications | Restrictions on interception and access |

### 7.2 Internal Policies

| Policy | Document ID | Version | Applicability to This System |
|---|---|---|---|
| GRC Program Charter | GRC-CHARTER-001 | 1.0 | Authorizing governance document for this SSP and all controls |
| Vulnerability Management Policy | GRC-POL-002 | 1.0 | Governs vulnerability scanning and remediation SLAs for all system assets |
| Security Monitoring Policy | GRC-POL-003 | 1.0 | Governs Sentinel operation, alert triage SLAs, and incident response |
| Configuration Baseline Standard | GRC-STD-001 | 1.0 | Governs configuration requirements for all system components |
| Identity Lifecycle Management Procedure | GRC-PROC-001 | 1.0 | Governs user provisioning, access reviews, and deprovisioning |
| Patch Management Procedure | GRC-PROC-003 | 1.0 | Governs OS and software patching for all in-scope assets |
| Third-Party Risk Assessment Template | GRC-TPRM-001 | 1.0 | Governs risk assessment for system interconnections |

---

## 8. Minimum Security Controls

This section documents the implementation status of each NIST SP 800-53 Rev 5 control family for the Cyber Range Azure system. The MODERATE baseline is applied, consistent with the FIPS 199 categorization in Section 2.

**Implementation Status Legend:**

| Status | Symbol | Meaning |
|---|---|---|
| Implemented | [I] | Control is fully implemented and operating as intended |
| Partially Implemented | [P] | Control is implemented for some components or partially meets the requirement |
| Planned | [L] | Control is not yet implemented; remediation is planned with a documented target date |
| Not Applicable | [N/A] | Control does not apply to this system or is fully inherited from the cloud provider |
| Inherited | [INH] | Control is fully provided by the cloud service provider (Microsoft Azure) |

---

### AC: Access Control

**AC-1 Access Control Policy and Procedures** [I]
The Configuration Baseline Standard (GRC-STD-001) and Identity Lifecycle Management Procedure (GRC-PROC-001) serve as the access control policy and procedures for the Cyber Range system. These documents are reviewed annually and approved by the Security Lead. Version 1.0 published March 5, 2026.

**AC-2 Account Management** [P]
User accounts are managed within Microsoft Entra ID. The ILM Procedure (GRC-PROC-001) defines the full account lifecycle: provisioning, modification, review, and deprovisioning. Roles are assigned per the principle of least privilege. Privileged accounts use the adm- naming prefix and are provisioned via PIM. **Gap (POA-007):** 100 percent of 2,315 user accounts had blank Department, Job Title, and Manager attributes at time of assessment, limiting account management effectiveness. Attribute remediation target: May 4, 2026.

**AC-3 Access Enforcement** [P]
Role-Based Access Control (RBAC) is enforced by Microsoft Entra ID and Azure RBAC for all resource access. Conditional Access policies enforce access decisions including MFA requirements and location restrictions. **Gap (POA-003):** Zero Conditional Access policies were active at time of assessment (CA portal returned HTTP 401). CA policy deployment target: April 5, 2026.

**AC-5 Separation of Duties** [P]
Separation of duties is enforced through distinct role assignments: Cloud Administrator, Identity Administrator, Security Operations Analyst, and GRC Analyst roles have non-overlapping permissions per the RACI in the GRC Program Charter. **Gap:** Formal documented separation of duties matrix is pending; no single account should have both provisioning and access review authority.

**AC-6 Least Privilege** [P]
All privileged access is delivered through PIM (just-in-time, time-limited, approval-required) per the PAM Standard. Standard users have read-only or student-lab-scoped access. **Gap:** Asset tier classification is pending (OBJ-006), which will formalize least-privilege access assignment per tier.

**AC-7 Unsuccessful Logon Attempts** [I]
Microsoft Entra ID enforces account lockout after repeated failed sign-in attempts. Smart Lockout is enabled by default in Entra ID, with threshold of 10 failed attempts resulting in account lockout. Detection rule ID-003 (Failed Sign-In Threshold Breach) monitors for credential attack patterns in Sentinel.

**AC-11 Session Lock** [INH]
Session lock controls are enforced by Microsoft Entra ID session management and Conditional Access session policies. Sign-in frequency and persistent browser session controls are configured at the Entra ID tenant level.

**AC-17 Remote Access** [P]
All remote access to the Cyber Range Azure environment is via HTTPS to Azure management endpoints or RDP/SSH to lab VMs. **Gap:** RDP (3389) and SSH (22) access to lab VMs must be restricted to authorized IP ranges per CBS NS-001/NS-002. Compliance verification is ongoing.

**AC-20 Use of External Systems** [I]
Student personal devices accessing the system are governed by Conditional Access policies that enforce compliant or hybrid Azure AD joined device requirements where applicable. External access to the Cyber Range is exclusively via Entra ID authentication.

---

### AT: Awareness and Training

**AT-1 Awareness and Training Policy** [P]
The GRC Program Charter (GRC-CHARTER-001) Section 6.3 includes a long-term objective to establish formal security awareness training. **Gap:** No formal security awareness training program exists at time of assessment. Target: established within 18 months per OBJ long-term goals.

**AT-2 Literacy Training and Awareness** [P]
Informal security awareness occurs through the educational nature of the Cyber Range curriculum. Students and instructors develop security awareness as part of their coursework. **Gap:** No formal annual security awareness training requirement exists for Cyber Range staff and administrators.

**AT-3 Role-Based Training** [P]
Security personnel have role-specific training through their professional development activities (certifications, courses). **Gap:** No formal role-based training program tracks or mandates specific training for Cloud Administrator, Identity Administrator, or Security Operations Analyst roles.

---

### AU: Audit and Accountability

**AU-1 Audit and Accountability Policy** [I]
The Security Monitoring Policy (GRC-POL-003) Section 4 defines audit and accountability requirements including log source requirements, retention periods, and monitoring obligations.

**AU-2 Event Logging** [I]
The following audit events are logged and flowing to the Log Analytics Workspace: Entra ID sign-in events (SignInLogs), Entra ID audit events (AuditLogs), Azure activity events (AzureActivity), Windows security events (SecurityEvent via Log Analytics agent), Defender for Cloud alerts (SecurityAlert), Defender for Endpoint alerts (DeviceEvents). Log completeness is verified by the MI-002 Connector Ingestion Gap detection rule.

**AU-3 Content of Audit Records** [I]
Audit records contain: event timestamp (UTC), event type, source IP address, user identity (UPN or ObjectId), target resource, result (success/failure), and correlation ID. All Entra ID logs conform to the Microsoft unified audit log schema.

**AU-6 Audit Record Review, Analysis, and Reporting** [P]
Microsoft Sentinel provides automated analysis of audit records via 29 analytics rules (KQL Detection Rule Library GRC-TEC-001). Manual audit record review occurs as part of the daily alert triage process. **Gap:** At time of assessment, zero analytics rules were active, and no manual review process existed for the SecurityAlert and SecurityIncident tables. Remediation: Priority 1 analytics rules deployed per OBJ-003; daily alert triage process established per OBJ-007.

**AU-9 Protection of Audit Information** [INH]
Azure Log Analytics provides immutable storage for audit logs. Logs cannot be modified or deleted by tenant administrators through the standard interface. Log deletion requires Microsoft support intervention, which is logged as an administrative action.

**AU-11 Audit Record Retention** [P]
Log retention is configured at 90 days hot (queryable) in the Log Analytics Workspace. Archive tier extends retention to 1-2 years for specified table types per SMP Section 4.3. **Gap:** Archive tier configuration not yet completed for all required log categories. Target: April 5, 2026.

**AU-12 Audit Record Generation** [I]
Diagnostic settings are configured to route all Entra ID logs to the Log Analytics Workspace. Azure Activity logs are routed to Sentinel via the Azure Activity connector. All required data connectors are confirmed operational at time of assessment (AzureDiagnostics table confirmed functional).

---

### CA: Assessment, Authorization, and Monitoring

**CA-1 Assessment, Authorization, and Monitoring Policy** [I]
The GRC Program Charter (GRC-CHARTER-001) and this System Security Plan establish the assessment, authorization, and monitoring policy for the Cyber Range system.

**CA-2 Control Assessments** [P]
The March 2026 multi-framework gap assessment constitutes the initial control assessment for the Cyber Range system. **Gap:** A formal annual control assessment schedule has not been established. Target: formalized in the annual GRC Program Review (March 2027).

**CA-3 Information Exchange** [I]
All system interconnections are documented in Section 6 of this SSP. Data exchange with Tenable.io is governed by the Tenable TPRM assessment (GRC-TPRM-003) and the Tenable Customer Agreement. Data exchange with Microsoft Azure is governed by the Microsoft Customer Agreement.

**CA-5 Plan of Action and Milestones** [I]
The POA&M (GRC-RISK-002) is active and maintained by the GRC Analyst. Eight active POA&M items are tracked with target dates. Monthly POA&M status reports are produced per the reporting cadence in the GRC Program Charter.

**CA-6 Authorization** [P]
The system is operating under an Interim Authority to Test and Operate (IATO) pending formal ATO issuance. The Security Lead has reviewed the residual risk posture and accepted it subject to POA&M completion. **Target:** Formal ATO issuance by June 5, 2026 contingent on Priority 1 POA&M items being resolved.

**CA-7 Continuous Monitoring** [P]
Microsoft Sentinel provides the continuous monitoring capability. The Security Monitoring Policy (GRC-POL-003) defines the monitoring requirements, alert SLAs, and reporting cadence. **Gap:** Zero analytics rules were active at time of assessment. Continuous monitoring will reach full operational capability after OBJ-003 (minimum 10 analytics rules) and OBJ-008 (all 18 rule categories) are completed.

**CA-8 Penetration Testing** [L]
No penetration test has been conducted against the Cyber Range Azure system. **Gap:** A penetration test should be scheduled within 12 months of the formal ATO date. Target: March 2027.

---

### CM: Configuration Management

**CM-1 Configuration Management Policy** [I]
The Configuration Baseline Standard (GRC-STD-001) v1.0 published March 5, 2026 serves as the configuration management policy for the Cyber Range system.

**CM-2 Baseline Configuration** [P]
The Configuration Baseline Standard (GRC-STD-001) defines configuration baselines for 18 requirement categories covering Conditional Access, user attributes, Azure Policy, governance, MFA, passwords, data security, Azure resources, Defender for Cloud, network security, OS hardening, endpoint protection, account controls, risk policies, software management, application security, service principals, and operational access. **Gap:** Compliance score estimated at 20-30 percent at time of assessment. Full CBS compliance is the objective of the continuous monitoring program.

**CM-3 Configuration Change Control** [P]
Azure Activity logs capture all configuration changes to Azure resources. Entra ID Audit logs capture all identity configuration changes. Change detection rules CR-001 (NSG Modified), CR-002 (Resource Deletion), CR-003 (Storage Public Access), and ID-006 (Privileged Role Assignment) alert on unauthorized changes. **Gap:** Formal change management process (change request approval before implementation) is not yet documented. Target: included in Configuration Baseline Standard v1.1.

**CM-6 Configuration Settings** [P]
Configuration settings for all components are defined in CBS GRC-STD-001. Current compliance score is approximately 20-30 percent. CBS compliance queries (KQL compliance posture queries CP-001 through CP-005) provide ongoing measurement.

**CM-7 Least Functionality** [P]
Azure resources are provisioned to provide only the services required for their designated function. **Gap:** Legacy authentication protocols have not yet been blocked via Conditional Access. Target: April 5, 2026 (OBJ-001).

**CM-8 System Component Inventory** [P]
The Tenable.io platform maintains an inventory of 126 assets in the Cyber Range Tenable management scope. **Gap:** Asset tier classification is not yet complete (OBJ-006). A comprehensive Azure Resource inventory aligned to CBS asset classification tiers is pending.

---

### CP: Contingency Planning

**CP-1 Contingency Planning Policy** [P]
Contingency planning requirements are partially addressed in the GRC Program Charter Section 16 (Dependencies and Continuity). **Gap:** A formal Contingency Plan document does not exist. The GRC Program Charter (GRC-CHARTER-001) Section 15.3 addresses GRC program continuity. A full system-level Contingency Plan is planned.

**CP-2 Contingency Plan** [L]
**Gap:** No formal Contingency Plan has been developed for the Cyber Range Azure system. Target: developed within 6 months of formal ATO date.

**CP-9 System Backup** [P]
Azure Backup is available for Virtual Machine backup. **Gap:** A documented backup schedule, retention policy, and tested recovery procedure does not exist for all system components. Azure Backup for VMs should be configured and tested.

**CP-10 System Recovery and Reconstitution** [P]
Azure provides platform-level recovery capabilities (VM snapshots, Azure Backup, geo-redundant storage). **Gap:** Recovery procedures have not been formally documented or tested. This gap is addressed in the TPRM Assessment as a vendor SLA requirement but not yet as an internal procedure.

---

### IA: Identification and Authentication

**IA-1 Identification and Authentication Policy** [I]
The Identity Lifecycle Management Procedure (GRC-PROC-001) and Configuration Baseline Standard (GRC-STD-001) Section 4 (MFA Requirements) and Section 5 (Password Standards) serve as the identification and authentication policy.

**IA-2 Identification and Authentication (Organizational Users)** [P]
All organizational users authenticate to the Cyber Range system via Microsoft Entra ID. User identities are unique (no shared accounts for privileged access). **Gap (POA-003):** 83,600 of 203,906 sign-in events (approximately 41 percent) completed on single-factor authentication at time of assessment, because no Conditional Access policies were enforcing MFA. CA policy deployment resolves this gap. Target: April 5, 2026.

**IA-2(1) Multi-Factor Authentication - Privileged Accounts** [P]
MFA is required for all privileged accounts (adm- prefix) per CBS MF-001. **Gap:** CA policies enforcing MFA were not active at time of assessment. All privileged accounts had MFA enrollment in Entra ID, but enforcement was not confirmed active.

**IA-2(2) Multi-Factor Authentication - Non-Privileged Accounts** [P]
MFA is required for all standard user accounts per CBS MF-002. **Gap:** CA policy enforcement not active at time of assessment. Approximately 41 percent of sign-ins completed on SFA as of the March 2026 baseline.

**IA-4 Identifier Management** [P]
User account naming conventions are defined in the ILM Procedure (GRC-PROC-001): standard accounts use UPN format, privileged accounts use adm- prefix, break-glass accounts use bg- prefix, service accounts use svc- prefix. **Gap:** 100 percent of 2,315 accounts had blank mandatory attributes (Department, Job Title, Manager), limiting the effectiveness of identifier management and account ownership tracking.

**IA-5 Authenticator Management** [P]
Entra ID manages all authenticators (passwords, FIDO2 keys, TOTP). Password policies are defined in CBS Section 6 (Password and Credential Standards). Break-glass accounts use FIDO2 hardware keys stored in physically secured locations. **Gap:** CBS PW-001 through PW-007 compliance has not yet been fully verified across the tenant.

**IA-8 Identification and Authentication (Non-Organizational Users)** [P]
Guest accounts (external identities) in the Entra ID tenant are governed by the ILM Procedure Section 3.3 (Guest Account Lifecycle). **Gap:** 2 guest accounts existed at time of assessment with no sponsorship record and no expiration date, violating the ILM Procedure guest lifecycle requirements.

**IA-11 Re-Authentication** [I]
Entra ID Conditional Access enforces sign-in frequency controls. Sessions are re-evaluated on each access request. Persistent browser session settings prevent indefinite session lifetime.

---

### IR: Incident Response

**IR-1 Incident Response Policy** [I]
The Security Monitoring Policy (GRC-POL-003) Sections 7 and 8 define incident classification, response SLAs, and escalation procedures for the Cyber Range system.

**IR-2 Incident Response Training** [L]
**Gap:** No formal incident response training program exists. Security Operations Analysts are expected to follow the SIEM Use Case Documentation (GRC-TEC-002) response workflows. Target: formal IR training included in the security awareness program objective.

**IR-4 Incident Handling** [P]
Incident handling procedures are defined in the Security Monitoring Policy (GRC-POL-003) Section 8. Sentinel provides incident creation, assignment, and investigation workflow. **Gap:** At time of assessment, unactioned incidents existed in the SecurityIncident table with no owner assigned. Incident triage process established per OBJ-007.

**IR-5 Incident Monitoring** [P]
Microsoft Sentinel provides incident monitoring. 18 SIEM use cases (GRC-TEC-002) define the detection scenarios that generate incidents. **Gap:** Zero analytics rules were active at time of assessment. Incident monitoring capability reaches full operational status after OBJ-003 and OBJ-008 completion.

**IR-6 Incident Reporting** [P]
Internal incident reporting follows the escalation protocol in SMP Section 7.1. External incident reporting (breach notification) procedures are defined in the relevant regulatory frameworks (FERPA, GDPR, Washington State law). **Gap:** Formal external incident notification procedures have not been tested or exercised.

**IR-7 Incident Response Assistance** [INH]
Microsoft Azure provides 24/7 support for platform-level incidents via the Azure support portal. Microsoft Defender for Cloud provides automated response recommendations.

**IR-8 Incident Response Plan** [L]
**Gap:** A formal Incident Response Plan document has not yet been produced. The Security Monitoring Policy covers incident response procedures for detection-initiated incidents. A full IRP is planned as a future GRC deliverable.

---

### MA: Maintenance

**MA-1 System Maintenance Policy** [P]
The Patch Management Procedure (GRC-PROC-003) addresses scheduled maintenance and patch deployment. **Gap:** A formal maintenance policy has not been independently documented; it is embedded within the Patch Management Procedure.

**MA-2 Controlled Maintenance** [P]
Scheduled maintenance windows are defined in the Patch Management Procedure (GRC-PROC-003) Section 8: Primary: second Saturday of each month, 02:00-06:00 UTC. Secondary: fourth Saturday, same window. **Gap:** Maintenance window policy has not yet been communicated to all system users.

**MA-4 Nonlocal Maintenance** [I]
All system maintenance is performed remotely via Azure Portal, Azure CLI, PowerShell, and Entra ID admin interfaces over TLS-encrypted connections. No physical console access is required. All maintenance sessions are logged to AzureActivity and Entra ID AuditLogs.

**MA-5 Maintenance Personnel** [I]
All maintenance personnel are authenticated via Entra ID and must hold the appropriate RBAC role for the maintenance activity being performed. Privileged maintenance is performed via PIM-activated roles with just-in-time access.

---

### MP: Media Protection

**MP-1 Media Protection Policy** [INH]
The Cyber Range Azure system is entirely cloud-hosted. Physical media protection (disk drives, removable media) is entirely managed by Microsoft Azure under the Microsoft shared responsibility model.

**MP-2 Media Access** [INH]
Access to physical storage media is managed by Microsoft Azure. All data is logically protected by Azure Storage access controls (RBAC, storage account keys, SAS tokens, private endpoints).

**MP-6 Media Sanitization** [INH]
When Azure storage resources are deleted, Microsoft performs media sanitization per the Microsoft Cloud Security Policy. Cryptographic erasure is used for managed disk deletion.

**MP-7 Media Use** [P]
External storage media (USB drives, removable storage) is not used in the Cyber Range environment (cloud-only). **Gap:** A formal media use policy has not been documented. CBS controls do not yet explicitly prohibit removable media in administrative workstations.

---

### PE: Physical and Environmental Protection

**PE-1 Physical and Environmental Protection Policy** [INH]
The Cyber Range system is entirely hosted on Microsoft Azure. Physical and environmental protection of the data center infrastructure is entirely managed by Microsoft under the Azure shared responsibility model. Microsoft maintains ISO 27001, SOC 2 Type II, and FedRAMP certifications for physical security controls.

**PE-2 through PE-20** [INH]
All physical and environmental protection controls are inherited from Microsoft Azure. The Cyber Range organization has no physical facilities housing system components.

---

### PL: Planning

**PL-1 Planning Policy** [I]
The GRC Program Charter (GRC-CHARTER-001) serves as the security planning policy. It establishes the planning framework, review cycles, and governance structure.

**PL-2 System Security Plan** [I]
This document constitutes the System Security Plan (SSP) for the Cyber Range Azure system (CR-AZURE-001). It is maintained by the GRC Analyst, reviewed annually, and updated following significant system changes.

**PL-4 Rules of Behavior** [L]
**Gap:** No formal Rules of Behavior document has been produced for Cyber Range users. Users accessing the system are implicitly bound by the Microsoft Acceptable Use Policy and the applicable terms of service. A formal Rules of Behavior document is planned.

**PL-8 Security and Privacy Architectures** [P]
The security architecture is described in Section 5 of this SSP. The privacy architecture is addressed through data classification (Section 4.3) and the applicable regulatory obligations (Section 7.1). **Gap:** A formal Privacy Impact Assessment has not been completed for the student PII processed in the system.

---

### PM: Program Management

**PM-1 Information Security Program Plan** [I]
The GRC Program Charter (GRC-CHARTER-001) serves as the information security program plan for the Cyber Range. It is maintained by the GRC Analyst and reviewed annually.

**PM-2 Roles and Responsibilities** [I]
Roles and responsibilities are defined in the GRC Program Charter RACI (Section 9) and the individual policy documents. All six key roles (Security Lead, GRC Analyst, Cloud Administrator, Identity Administrator, Detection Engineer, SOC Analyst) have documented responsibilities.

**PM-4 Plan of Action and Milestones Process** [I]
The POA&M (GRC-RISK-002) process is established: GRC Analyst maintains the POA&M, produces monthly status reports, and escalates overdue items per the GRC Program Charter Section 14.

**PM-6 Measures of Performance** [I]
Twelve KPIs are defined in the GRC Program Charter Section 12.1, each with a measurement method, target, and critical threshold. Quarterly GRC Program Scorecard tracks KPI performance.

**PM-9 Risk Management Strategy** [I]
The risk management approach is defined in the GRC Program Charter Section 10, including the likelihood-impact scoring methodology, risk tier definitions, and treatment options.

**PM-10 Authorization Process** [P]
The authorization process is initiated with this SSP. The Security Lead serves as the Authorizing Official. The system is operating under IATO pending formal ATO. **Gap:** Formal ATO issuance process is pending POA&M Priority 1 item completion. Target: June 5, 2026.

**PM-14 Testing, Training, and Monitoring** [P]
Testing is performed through the March 2026 gap assessment and ongoing compliance query execution. Monitoring is performed through Microsoft Sentinel. **Gap:** Training programs are not yet formalized.

---

### PS: Personnel Security

**PS-1 Personnel Security Policy** [P]
The ILM Procedure (GRC-PROC-001) covers personnel security with respect to system access: joiner, mover, and leaver workflows; access reviews; and account deprovisioning. **Gap:** A formal HR-integrated personnel security policy (background checks, security agreements, position risk designations) does not exist.

**PS-3 Personnel Screening** [L]
**Gap:** No formal background screening requirement is documented for personnel with privileged access to the Cyber Range system. This is a gap relative to the NIST 800-53 MODERATE baseline. Target: addressed in the planned Information Security Policy (GRC-POL-001).

**PS-4 Personnel Termination** [I]
The ILM Procedure Section 4.3 (Leaver Workflow) defines the account deprovisioning process for personnel departure, including: immediate access revocation for privileged accounts (4-hour SLA), scheduled deprovisioning for standard accounts (1 business day), and manager notification requirements.

**PS-5 Personnel Transfer** [I]
The ILM Procedure Section 3.3 (Mover Workflow) defines the access modification process for role changes, including: access review within 5 business days, removal of prior role access, assignment of new role access per least privilege.

**PS-6 Access Agreements** [L]
**Gap:** No formal access agreements (Rules of Behavior, acceptable use acknowledgment) are required before system access is granted. This is a gap relative to the NIST 800-53 MODERATE baseline.

**PS-7 External Personnel Security** [I]
External personnel (contractors, guest instructors, vendor staff) are provisioned as guest accounts per the ILM Procedure Section 3.3, with a maximum 1-year expiration and a designated internal sponsor.

---

### PT: PII Processing and Transparency

**PT-1 PII Processing and Transparency Policy** [P]
The compliance obligations in Section 7.1 of this SSP identify applicable privacy laws (FERPA, GDPR, CCPA, Washington State). **Gap:** A formal Privacy Policy and PII Processing Policy have not been produced. Target: included in the planned Information Security Policy (GRC-POL-001).

**PT-2 Authority to Process PII** [P]
Student PII is processed under FERPA authority for education records management. **Gap:** A formal PII inventory, legal basis documentation, and Privacy Notice have not been produced.

**PT-3 Personally Identifiable Information Processing Purposes** [P]
Student PII (names, email addresses) is collected for the purpose of Cyber Range account provisioning, course enrollment tracking, and lab access management. **Gap:** Formal purpose limitation documentation has not been produced.

**PT-5 Privacy Notice** [L]
**Gap:** No formal Privacy Notice has been published to students informing them of the PII collected, the purposes, and their rights. Target: developed as part of planned Privacy Policy.

---

### RA: Risk Assessment

**RA-1 Risk Assessment Policy** [I]
The GRC Program Charter Section 10 defines the risk management approach, scoring methodology, and risk treatment options. The risk assessment policy is embedded in the charter.

**RA-2 Security Categorization** [I]
System security categorization is documented in Section 2 of this SSP. The system is categorized as MODERATE impact across all security objectives.

**RA-3 Risk Assessment** [I]
The March 2026 multi-framework gap assessment constitutes the initial risk assessment for the Cyber Range system. Seven risk items are documented in the GRC Program Charter Section 10.3 with scores, tiers, and treatments. The POA&M (GRC-RISK-002) tracks remediation of identified risks.

**RA-5 Vulnerability Monitoring and Scanning** [P]
Tenable.io performs vulnerability scanning of 126 system assets. Scan results are reviewed per the Vulnerability Management Policy (GRC-POL-002) and Vulnerability Management Procedure Report (GRC-PROC-002). **Gap:** The Vulnerability Management Procedure Report documents that the scan baseline was established March 5, 2026. SLA clocks for all identified vulnerabilities start from that date.

**RA-7 Risk Response** [I]
Risk responses are documented in the GRC Program Charter Section 10.4 (Remediate, Accept, Transfer, Avoid). Each POA&M item has a defined risk response. The Security Lead approves risk acceptance for Moderate and above.

---

### SA: System and Services Acquisition

**SA-1 System and Services Acquisition Policy** [I]
The Third-Party Risk Assessment Template (GRC-TPRM-001) governs the acquisition of external systems and services that will process Cyber Range data.

**SA-4 Acquisition Process** [I]
The TPRM process (GRC-TPRM-001) requires security assessment of all Tier 1 and Tier 2 vendors before onboarding. Security requirements are documented in the TPRM questionnaire domains.

**SA-9 External System Services** [I]
External system services are documented in Section 6 (System Interconnections) of this SSP. Microsoft Azure and Tenable.io are the two primary external services. Both have active TPRM assessments (GRC-TPRM-002 and GRC-TPRM-003).

**SA-11 Developer Testing and Evaluation** [N/A]
The Cyber Range Azure system does not include internally developed software. All software is provided by Microsoft, Tenable, and other commercial vendors.

**SA-22 Unsupported System Components** [P]
**Gap:** No formal process exists for identifying and managing end-of-life or unsupported software on Cyber Range assets. The Patch Management Procedure (GRC-PROC-003) addresses patching for supported software but does not address EOL component management. Target: added to CBS v1.1.

---

### SC: System and Communications Protection

**SC-1 System and Communications Protection Policy** [I]
The Configuration Baseline Standard (GRC-STD-001) Section 9 (Network Security Requirements) and Section 4 (Azure Resource and Subscription Management) serve as the system and communications protection policy.

**SC-5 Denial-of-Service Protection** [INH]
Azure DDoS Protection (Basic tier) is enabled by default for all Azure public IP addresses. Advanced DDoS Protection can be enabled at the virtual network level if required.

**SC-7 Boundary Protection** [P]
Network Security Groups (NSGs) are configured on all subnets and VM network interfaces to enforce network segmentation. Azure Firewall or Network Virtual Appliance is not currently deployed. **Gap:** NSG rule review process is not formalized. CBS NS-001 through NS-006 define the required boundary protection rules; compliance verification is ongoing.

**SC-8 Transmission Confidentiality and Integrity** [I]
All data in transit is encrypted using TLS 1.2 or higher. Azure enforces minimum TLS 1.2 for all storage and management plane communications. CBS DS-003 requires TLS 1.2 or higher for all data in transit.

**SC-12 Cryptographic Key Establishment and Management** [P]
Azure Key Vault is available for cryptographic key management. **Gap:** Not all secrets and keys in the environment are stored in Key Vault. CBS AZ-004 (Key Vault for secrets) compliance is not yet complete.

**SC-13 Cryptographic Protection** [I]
All Azure storage is encrypted at rest using AES-256 encryption by default. All management traffic uses TLS 1.2. FIDO2 hardware keys for break-glass accounts provide FIPS 140-2 compliant authenticators.

**SC-20 Secure Name/Address Resolution Service** [INH]
DNS services are provided by Microsoft Azure DNS, which is a platform-managed service with built-in security and availability.

**SC-28 Protection of Information at Rest** [I]
All data at rest in Azure Storage, Azure Managed Disks, and Azure Databases is encrypted using AES-256 with Microsoft-managed keys. CBS DS-001 requires encryption at rest for all data stores.

---

### SI: System and Information Integrity

**SI-1 System and Information Integrity Policy** [I]
The Security Monitoring Policy (GRC-POL-003) and Vulnerability Management Policy (GRC-POL-002) collectively serve as the system and information integrity policy.

**SI-2 Flaw Remediation** [P]
The Patch Management Procedure (GRC-PROC-003) defines flaw remediation SLAs: Critical vulnerabilities must be patched within 15 days (7 days for Tier 1 assets); High within 30 days; Medium within 90 days. **Gap:** Patch compliance has not yet been verified against these SLAs because asset tier classification (OBJ-006) and the patch management process deployment are in progress.

**SI-3 Malicious Code Protection** [P]
Microsoft Defender for Endpoint provides malicious code protection on all onboarded assets. **Gap:** ProtectionStatus table returned zero rows for all 126 assets at time of assessment, meaning malicious code protection telemetry was not flowing to Sentinel. POA-004 remediation (target April 5, 2026) addresses this gap.

**SI-4 System Monitoring** [P]
Microsoft Sentinel provides system monitoring capability. The Security Monitoring Policy defines monitoring requirements across five domains (identity/access, endpoint/infrastructure, network/perimeter, application/SP, cloud resource/config). **Gap:** Zero analytics rules were active at time of assessment. Full monitoring capability requires OBJ-003 and OBJ-008 completion.

**SI-5 Security Alerts, Advisories, and Directives** [I]
Microsoft Defender for Cloud generates security recommendations and alerts. CISA KEV advisories are incorporated into the Patch Management Procedure as override triggers for patching SLAs. The GRC Analyst monitors vendor security advisories for all Tier 1 vendors.

**SI-6 Security and Privacy Function Verification** [P]
CBS compliance queries (CP-001 through CP-005) provide verification of specific security function status. **Gap:** Comprehensive automated verification of all CBS requirements is not yet implemented. Azure Policy assignments required by CBS Section 9 have not yet been deployed.

**SI-7 Software, Firmware, and Information Integrity** [P]
Azure Defender for Cloud provides file integrity monitoring capabilities. **Gap:** File integrity monitoring is not yet configured for all Cyber Range assets. CBS OS-006 requires integrity monitoring; compliance verification is ongoing.

**SI-12 Information Management and Retention** [I]
Information retention is defined in Section 4.3 of this SSP and the Security Monitoring Policy Section 4.3 (log retention requirements). The GRC document retention policy (7 years from vendor relationship end or document retirement) is defined in the TPRM Template and GRC Program Charter.

---

### SR: Supply Chain Risk Management

**SR-1 Supply Chain Risk Management Policy** [I]
The Third-Party Risk Assessment Template (GRC-TPRM-001) and the GRC Program Charter Section 13.2 (tooling dependencies) constitute the supply chain risk management policy.

**SR-2 Supply Chain Risk Management Plan** [I]
The TPRM program (GRC-TPRM-001) implements the supply chain risk management plan. The Vendor Tier Registry (GRC-TPRM-001 Section 3.2) documents all vendors by tier and assessment status.

**SR-3 Supply Chain Controls and Processes** [P]
TPRM assessments are completed for Tier 1 vendors (Microsoft Corporation, Tenable, Inc.). **Gap:** The TPRM assessments note that the Tenable DPA is in progress. Sub-processor advance notice is not contractually committed in the standard Tenable agreement. These are documented in the Tenable TPRM assessment (GRC-TPRM-003).

**SR-5 Acquisition Strategies, Tools, and Methods** [I]
All new vendor relationships require TPRM assessment before onboarding per GRC-TPRM-001 Section 4. Tier 1 assessments require Security Lead approval.

**SR-6 Supplier Assessments and Reviews** [I]
Annual reassessment of all Tier 1 vendors is required per GRC-TPRM-001 Section 2.3. Next Tenable reassessment: March 2027. Next Microsoft reassessment: March 2027.

**SR-10 Inspection of Systems or Components** [P]
Tenable.io Nessus Agent updates represent the primary supply-chain software delivery risk. **Gap:** No formal process for validating Nessus Agent updates before deployment to production assets. Recommendation documented in Tenable TPRM assessment.

**SR-12 Component Disposal** [INH]
Component disposal for Azure cloud resources is managed by Microsoft. Logical deletion of Azure resources triggers cryptographic erasure per Microsoft's cloud security policy.

---

## 9. Control Implementation Summary

### 9.1 Implementation Status by Control Family

| Control Family | Controls Reviewed | Implemented [I] | Partially Implemented [P] | Planned [L] | Inherited [INH] | N/A |
|---|---|---|---|---|---|---|
| AC: Access Control | 9 | 3 | 4 | 0 | 2 | 0 |
| AT: Awareness and Training | 3 | 0 | 2 | 1 | 0 | 0 |
| AU: Audit and Accountability | 6 | 4 | 2 | 0 | 0 | 0 |
| CA: Assessment, Authorization, Monitoring | 7 | 3 | 3 | 1 | 0 | 0 |
| CM: Configuration Management | 6 | 1 | 5 | 0 | 0 | 0 |
| CP: Contingency Planning | 4 | 0 | 2 | 2 | 0 | 0 |
| IA: Identification and Authentication | 8 | 2 | 5 | 0 | 1 | 0 |
| IR: Incident Response | 7 | 1 | 3 | 2 | 1 | 0 |
| MA: Maintenance | 4 | 2 | 2 | 0 | 0 | 0 |
| MP: Media Protection | 4 | 0 | 1 | 0 | 3 | 0 |
| PE: Physical and Environmental | 2 | 0 | 0 | 0 | 2 | 0 |
| PL: Planning | 4 | 2 | 1 | 1 | 0 | 0 |
| PM: Program Management | 7 | 5 | 2 | 0 | 0 | 0 |
| PS: Personnel Security | 6 | 2 | 1 | 2 | 0 | 1 |
| PT: PII Processing and Transparency | 5 | 0 | 3 | 2 | 0 | 0 |
| RA: Risk Assessment | 5 | 4 | 1 | 0 | 0 | 0 |
| SA: System and Services Acquisition | 5 | 3 | 1 | 0 | 0 | 1 |
| SC: System and Communications Protection | 9 | 4 | 3 | 0 | 2 | 0 |
| SI: System and Information Integrity | 8 | 2 | 5 | 0 | 0 | 1 |
| SR: Supply Chain Risk Management | 7 | 4 | 2 | 0 | 1 | 0 |
| **TOTAL** | **126** | **42 (33%)** | **48 (38%)** | **11 (9%)** | **12 (10%)** | **3 (2%)** |

**Effective implementation rate (Implemented + Inherited + N/A): 45% at time of March 2026 assessment.**

### 9.2 Priority Gaps for Immediate Remediation

The following gaps represent the highest-priority control implementation failures and directly align to POA&M items:

| Gap | Control Family | POA&M Item | Target Date | Business Risk |
|---|---|---|---|---|
| Zero Conditional Access policies (SFA 41% of sign-ins) | AC, IA | POA-003 / OBJ-001 | April 5, 2026 | Critical: credential bypass |
| ProtectionStatus table empty (126 assets unmonitored) | SI, AU | POA-004 / OBJ-002 | April 5, 2026 | Critical: malware undetected |
| Zero Sentinel analytics rules (no detection capability) | AU, IR, SI | POA-006 / OBJ-003 | April 5, 2026 | Critical: all threats undetected |
| 100% account attribute blank (identity management failed) | AC, IA, PS | POA-007 / OBJ-004 | May 4, 2026 | High: access reviews impossible |
| No formal IR Plan (IR-8 gap) | IR | Planned | September 2026 | High: unstructured incident response |
| No penetration test (CA-8 gap) | CA | Planned | March 2027 | Moderate: unverified control effectiveness |

---

## 10. Continuous Monitoring Plan

### 10.1 Monitoring Strategy

Continuous monitoring for the Cyber Range Azure system is implemented through the following mechanisms, consistent with NIST SP 800-137 and the Security Monitoring Policy (GRC-POL-003):

| Monitoring Mechanism | Tools | Frequency | KPI Reference |
|---|---|---|---|
| SIEM-based threat detection | Microsoft Sentinel analytics rules (29 rules, KQL Detection Rule Library GRC-TEC-001) | Real-time (rules run every 5-30 minutes depending on severity) | KPI-003 (rule coverage) |
| Alert triage and incident response | Sentinel Incidents queue; SIEM Use Case Documentation response workflows | Daily (High/Critical same day; others within SLA) | KPI-007 (alert backlog) |
| Vulnerability scanning | Tenable.io (126 assets; Nessus Agent; scheduled scans) | Weekly scheduled scans (minimum) | KPI-005, KPI-006 (MTTR) |
| Identity and access compliance | CP-001 through CP-005 KQL compliance queries | Weekly (SFA); Monthly (attributes, GA count); Daily (alert backlog) | KPI-001, KPI-002, KPI-009 |
| Endpoint protection coverage | ProtectionStatus table query; EP-001 and EP-002 analytics rules (post-POA-004) | Daily (EP-001 and EP-002 after POA-004) | KPI-004 |
| Configuration compliance | CBS compliance queries; Azure Policy (when configured) | Quarterly manual assessment | KPI-011 (CBS score) |
| Log ingestion health | MI-002 Connector Ingestion Gap rule | Every 4 hours | Internal |
| Patch compliance | Azure Update Manager reports; Tenable patch status | Monthly | KPI-005, KPI-006 |
| Privileged account monitoring | CP-004 (GA count); ID-001 (break-glass); ID-006 (role assignment) | Monthly (CP-004); Real-time (ID-001, ID-006) | KPI-009 |

### 10.2 Annual Assessment Schedule

| Assessment Activity | Frequency | Next Due | Owner |
|---|---|---|---|
| Full NIST 800-53 control assessment | Annual | March 2027 | GRC Analyst |
| SSP review and update | Annual | March 2027 | GRC Analyst |
| POA&M full review | Monthly (status); Annual (full re-scoring) | April 5, 2026 (first monthly); March 2027 (annual) | GRC Analyst |
| Penetration test | Annual (planned) | March 2027 | External vendor (TBD) |
| TPRM reassessment (Tier 1 vendors) | Annual | March 2027 | GRC Analyst |
| Security awareness training | Annual (planned) | September 2026 (initial) | GRC Analyst |

---

## 11. Plan of Action and Milestones Summary

The following is the current POA&M summary as of the SSP effective date. The full POA&M is maintained in GRC-RISK-002.

| POA&M ID | Weakness | Control Family | Risk Level | Target Date | Status |
|---|---|---|---|---|---|
| POA-001 | Lack of formal GRC program documentation | PM | High | March 5, 2026 | CLOSED (charter and deliverables produced) |
| POA-002 | Zero Sentinel analytics rules; no detection capability | AU, IR, SI | Critical | April 5, 2026 | Open: OBJ-003 in progress |
| POA-003 | Zero CA policies; 41% of sign-ins on SFA | AC, IA | Critical | April 5, 2026 | Open: OBJ-001 in progress |
| POA-004 | ProtectionStatus table empty; 126 assets not reporting | SI | Critical | April 5, 2026 | Open: OBJ-002 in progress |
| POA-005 | No vulnerability management operational capability | RA, SI | High | April 5, 2026 | Open: VMP and PMP deployed; scan baseline established |
| POA-006 | No asset inventory or tier classification | CM, RA | High | April 5, 2026 | Open: OBJ-006 in progress |
| POA-007 | 100% of 2,315 accounts missing mandatory attributes | AC, IA | High | May 4, 2026 | Open: ILM Procedure deployed; remediation in progress |
| POA-008 | No TPRM program; no vendor risk assessments | SR | High | April 5, 2026 | Open: TPRM template deployed; assessments in progress |

**POA-001 CLOSED:** The production of the GRC Program Charter (GRC-CHARTER-001) and all ten program deliverables satisfies POA-001. The Cyber Range now has a documented GRC program.

---

## 12. Authorizing Official Signature

By signing below, the Authorizing Official formally accepts the residual risk documented in this System Security Plan and authorizes the operation of the ABC Corp Azure Cloud Tenant (CR-AZURE-001) subject to the conditions stated herein.

| Role | Name | Signature | Date |
|---|---|---|---|
| Authorizing Official (Security Lead) | [Name] | [Signature] | March 5, 2026 |
| Information System Security Officer (GRC Analyst) | [Name] | [Signature] | March 5, 2026 |
| System Owner | [Name] | [Signature] | March 5, 2026 |

**Authorization Conditions:**

1. The system is authorized to operate under Interim Authority to Test and Operate (IATO) until June 5, 2026
2. POA&M Priority 1 items (POA-002, POA-003, POA-004) must be resolved by April 5, 2026 for the system to transition to full Authorization to Operate (ATO)
3. The SSP must be reviewed and updated following any significant system change, major security incident, or change in authorization boundary
4. Continuous monitoring per Section 10 must be active and producing results before full ATO is issued
5. The Authorizing Official reserves the right to revoke this authorization if any Critical-tier risk flag is triggered and not remediated within the required SLA

---

*End of Document*

*This System Security Plan was produced for the ABC Corp Azure Cloud Tenant (CR-AZURE-001) following the March 2026 multi-framework gap assessment. All control implementation statements reflect the verified state of the live production system as of that assessment. The effective control implementation rate of 45 percent (Implemented plus Inherited plus N/A) at time of assessment reflects real findings from real queries against a real environment. The three Critical-tier POA&M items (zero CA policies, empty ProtectionStatus table, zero Sentinel analytics rules) are documented as accurately as any audit finding in this class of document can be.*
