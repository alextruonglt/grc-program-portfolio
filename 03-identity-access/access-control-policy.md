# Access Control Policy

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity and Access Governance Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> **Disclaimer:** This is a sanitized version of an Access Control Policy produced following a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This Access Control Policy was produced as a direct deliverable from a real, evidence-based gap assessment of a live Microsoft Azure shared tenant. Every requirement in this policy traces to a specific deficiency confirmed through live telemetry from Microsoft Sentinel, Tenable vulnerability scan exports, and Microsoft Entra ID exports gathered at assessment time. This is not a generic template -- each requirement closes a documented gap in a production environment.

**Driving Findings:** AC and IA control families rated Gap Rating 1 (Not Implemented) across multiple controls. Confirmed findings: 83,600 single-factor sign-in events out of 203,906 total, zero Conditional Access enforcement, five permanent Global Administrator accounts with no PIM, 100% blank Department fields across 2,315 accounts, and 512 failed sign-in attempts against a single account from an external domain with no lockout. This policy directly remediates POA-001, POA-002, POA-007, and POA-008.

**Assessment Standards Applied:** NIST SP 800-53 Rev 5 | NIST CSF 2.0 | ISO/IEC 27001:2022 | SOC 2 TSC | CIS Controls v8

---

## Table of Contents

1. Purpose
2. Scope
3. Guiding Principles
4. Roles and Responsibilities
5. Identity Lifecycle Management
   - 5.1 Account Provisioning
   - 5.2 Account Attribute Requirements
   - 5.3 Account Deprovisioning
   - 5.4 Account Review and Recertification
6. Authentication Requirements
   - 6.1 Multi-Factor Authentication
   - 6.2 Conditional Access
   - 6.3 Password Policy
   - 6.4 Legacy Authentication
7. Authorization and Least Privilege
   - 7.1 Role-Based Access Control
   - 7.2 Privileged Access
   - 7.3 Privileged Identity Management
   - 7.4 Service Accounts and Non-Human Identities
8. Guest and External Access
9. Monitoring and Anomaly Response
   - 9.1 Sign-In Monitoring
   - 9.2 Brute Force and Lockout
   - 9.3 Privileged Activity Monitoring
10. Access Exceptions and Waivers
11. Enforcement and Consequences
12. Policy Review and Maintenance
13. Related Documents and References
14. Approval and Sign-Off

---

## 1. Purpose

This Access Control Policy establishes the minimum requirements for managing, protecting, and auditing access to all ABC Corp systems, identities, and data hosted within the cloud tenant. It defines who may access what, under what conditions, through what authentication mechanisms, and with what level of privilege.

This policy is grounded in evidence from a real gap assessment, which confirmed the following deficiencies in a live production environment:

- **MFA gap (real finding):** 83,600 of 203,906 sign-in events (41%) completed on single-factor authentication. Zero Conditional Access policies enforced. CA portal returned HTTP 401 at time of assessment, indicating no active CA policies existed.
- **Privileged access gap (real finding):** Five permanent Global Administrator accounts with no Privileged Identity Management configuration. Zero eligible assignments. Zero just-in-time activations. All five held standing, unrestricted tenant-wide access.
- **Identity governance gap (real finding):** 100% of 2,315 user accounts had blank Department fields, making role-based and attribute-based access reviews impossible.
- **Credential attack gap (real finding):** 30 accounts recorded more than 5 failed sign-in attempts. The top account accumulated 512 failures from an external domain with no lockout, no investigation trigger, and no response.

This policy provides the foundational requirements that close these gaps and prevent recurrence.

---

## 2. Scope

**Systems:** All systems, services, virtual machines, applications, and data stores within the ABC Corp cloud tenant, including Microsoft Entra ID, Microsoft 365, Azure resources, and any additional SaaS or cloud services connected to the tenant.

**Users:** All individuals with an account in the tenant, including administrators, staff, students, service accounts, guest accounts, and any third-party accounts with delegated access.

**Identities:** All human user accounts, guest accounts, service principals, managed identities, and application registrations within the tenant.

**Out of Scope:** Physical access to cloud provider datacenters. Identity controls within isolated lab environments that do not connect to the production tenant boundary.

---

## 3. Guiding Principles

All access control decisions must be consistent with the following principles:

**3.1 Least Privilege:** Every user, service account, and application must be granted only the minimum access required to perform its defined function. No access may be granted beyond what is explicitly justified and documented.

**3.2 Need to Know:** Access to sensitive data, configuration interfaces, and administrative functions must be restricted to individuals whose role requires that access.

**3.3 Zero Standing Privilege:** No account should hold permanent elevated permissions. Privileged access must be activated on demand, scoped to the minimum required role, time-limited, and revoked automatically upon session expiration.

**3.4 Separation of Duties:** Administrative functions must be distributed across roles to prevent any single account from controlling the full lifecycle of a sensitive operation without oversight.

**3.5 Auditability:** All access grants, modifications, and revocations must be logged. The inability to audit access history is itself a control failure.

**3.6 Defense in Depth:** Authentication and authorization controls must be layered. MFA, Conditional Access, least privilege, PIM, and monitoring must function together. No single control is sufficient.

---

## 4. Roles and Responsibilities

### 4.1 Security Lead / Cybersecurity Owner

- Approve and maintain this policy
- Authorize exceptions to policy requirements (Section 10)
- Review access review summary reports
- Escalation point for policy violations

### 4.2 Identity Administrator

- Own and operate all Entra ID identity lifecycle processes
- Implement and maintain Conditional Access policies
- Execute account provisioning, modification, and deprovisioning
- Conduct and document periodic access reviews
- Monitor and respond to sign-in anomalies
- Maintain PIM configuration and activation records

### 4.3 GRC Analyst

- Maintain policy documentation and version control
- Track compliance against this policy through the POA&M
- Produce access review summary reports for the Security Lead
- Update this policy following incidents or assessment findings

### 4.4 Cloud Administrator

- Manage service principal and managed identity access assignments
- Ensure Azure RBAC assignments comply with least privilege requirements
- Remove unauthorized resource-level access assignments

### 4.5 Managers and Account Requestors

- Submit formal access requests with documented business justification
- Attest to the accuracy of their direct reports' access during access reviews
- Notify the Identity Administrator within 1 business day when a user's role changes or employment ends

### 4.6 All Users

- Use only the accounts and permissions assigned to them
- Report suspected account compromise or unauthorized access immediately
- Not share credentials, tokens, or authentication methods with any other individual
- Complete security awareness training within 30 days of account creation and annually thereafter

---

## 5. Identity Lifecycle Management

### 5.1 Account Provisioning

**5.1.1** All user accounts must be provisioned through a documented request and approval process. Accounts must not be created outside this process for any reason.

**5.1.2** Every account must be associated with a named, identifiable individual or a documented service function. Generic, shared, or unnamed accounts are not permitted except for documented break-glass emergency access accounts.

**5.1.3** Access grants must be role-based wherever possible. Accounts must be assigned to the appropriate role or group at provisioning rather than receiving direct permission assignments.

**5.1.4** New accounts must not be activated until all mandatory attributes are populated (see Section 5.2) and the provisioning request has been approved.

**5.1.5** Privileged role assignments must not be made at account creation. Users requiring privileged access must first be provisioned as standard users and then separately approved for elevated access through the PIM eligibility process.

### 5.2 Account Attribute Requirements

All user accounts must have the following attributes populated at provisioning and kept current throughout the account lifecycle:

| Attribute | Requirement | Purpose |
|---|---|---|
| Display Name | Full legal name or approved alias | Identity confirmation |
| User Principal Name | Follows defined naming convention | Unique identifier |
| Department | Organization unit or program area | Access reviews, RBAC grouping |
| Job Title | Role or student classification | Access justification baseline |
| Manager | Named manager or account sponsor | Access review attestation |
| Account Enabled | True for active, False for suspended/terminated | Lifecycle state |

**Driving Evidence (real finding):** At time of the gap assessment, 100% of 2,315 user accounts had blank Department fields. This made role-based access reviews, anomaly investigation, and RBAC-based group assignment impossible. Department, Job Title, and Manager are mandatory from the effective date of this policy.

**5.2.1** The Identity Administrator must perform a monthly report of accounts with incomplete attributes. Any account missing mandatory attributes active for more than 30 days before this policy's effective date must be remediated within 60 days.

**5.2.2** Accounts created after the effective date that have not had mandatory attributes populated within 5 business days of creation must be suspended pending attribute completion.

### 5.3 Account Deprovisioning

**5.3.1** When a user's role, employment, or enrollment ends, the Identity Administrator must be notified within 1 business day by the user's manager or program administrator.

**5.3.2** Upon notification, the Identity Administrator must disable the account within 4 hours for privileged users and within 1 business day for standard users.

**5.3.3** Disabled accounts must be retained for 30 days before deletion. After 30 days, accounts must be permanently deleted unless a documented legal or investigation hold is in place.

**5.3.4** All active sessions must be revoked at the time of account disable. All group memberships, role assignments, and application access must be removed at permanent deletion.

**5.3.5** Access tokens, OAuth consents, and application permissions associated with the account must be reviewed and revoked as part of the offboarding process.

### 5.4 Account Review and Recertification

**5.4.1** Access reviews must be conducted on the following cadence:

| Account Type | Review Cadence | Reviewer |
|---|---|---|
| Global Administrator | Quarterly | Security Lead |
| All privileged roles | Quarterly | Identity Administrator |
| Standard user accounts | Annual | Manager or account sponsor |
| Guest accounts | Quarterly | Named sponsor |
| Service principals | Quarterly | Cloud Administrator |

**5.4.2** During each review, the reviewer must attest that: the account is still required, the account owner's access is appropriate for their current role, no excessive permissions exist, and account attributes are accurate and current.

**5.4.3** Any account that cannot be attested within 10 business days of the review period opening must be suspended pending attestation.

**5.4.4** Access review results must be documented and retained for a minimum of 1 year. Summary reports must be submitted to the Security Lead within 5 business days of review completion.

---

## 6. Authentication Requirements

### 6.1 Multi-Factor Authentication

**6.1.1** Multi-factor authentication is required for all user accounts without exception. No account may access any tenant resource using single-factor authentication.

**Driving Evidence (real finding):** At time of the gap assessment, 83,600 of 203,906 sign-in events (41%) completed on single-factor authentication. SigninLogs confirmed CA notApplied for 203,906 sign-ins and CA Enforced for 0. This policy eliminates single-factor authentication entirely.

**6.1.2** MFA must be enforced through Conditional Access policy, not through per-user MFA legacy settings.

**6.1.3** Acceptable second factors, in order of preference:
1. Microsoft Authenticator (push notification or number matching)
2. FIDO2 hardware security key
3. Authenticator app TOTP (time-based one-time password)
4. SMS or voice call (permitted only where no other method is technically feasible; must be documented exception)

**6.1.4** SMS and voice-based MFA must not be used for privileged accounts under any circumstances. Privileged accounts must use Microsoft Authenticator or FIDO2.

**6.1.5** All user accounts must have a registered MFA method before account activation.

**6.1.6** MFA method changes must be logged and reviewed as a security-sensitive operation. All MFA method registrations must be correlated to a provisioning or change request.

### 6.2 Conditional Access

**6.2.1** A minimum of three Conditional Access policies must be active at all times:

| Policy | Scope | Action |
|---|---|---|
| Require MFA for all users | All users, all cloud apps | Require MFA |
| Block legacy authentication | All users, all legacy auth clients | Block |
| Require MFA for privileged roles | All privileged role holders | Require MFA (always) |

**6.2.2** Conditional Access policies must be tested in Report-only mode for a minimum of 5 business days before being set to Enabled.

**6.2.3** Conditional Access policies may not be disabled, modified to Report-only, or deleted without written authorization from the Security Lead. All CA policy changes must be logged and reviewed within 24 hours.

**6.2.4** The Identity Administrator must review Conditional Access policy effectiveness monthly by querying SigninLogs for CA notApplied counts. Any month where CA notApplied exceeds 5% of total sign-ins must be investigated and documented.

### 6.3 Password Policy

**6.3.1** All user accounts must comply with the following minimum password requirements:

| Requirement | Standard User | Privileged User |
|---|---|---|
| Minimum length | 12 characters | 16 characters |
| Complexity | Uppercase, lowercase, number, special character | Uppercase, lowercase, number, special character |
| Password history | No reuse of last 10 passwords | No reuse of last 24 passwords |
| Expiration | 365 days | 90 days |
| Lockout threshold | 10 failed attempts | 5 failed attempts |

**6.3.2** Passwords must never be shared, written down in plaintext, stored in unencrypted files, or reused across systems.

**6.3.3** Temporary passwords issued during account creation or password reset must require immediate change at first sign-in.

### 6.4 Legacy Authentication

**6.4.1** Legacy authentication protocols (SMTP AUTH, IMAP, POP3, Basic Auth, ActiveSync with Basic) must be blocked via Conditional Access for all users without exception.

**6.4.2** Any application requiring legacy authentication must be formally documented, approved by the Security Lead, and treated as a priority modernization item tracked in the POA&M.

**6.4.3** The proportion of sign-ins using legacy protocols must be reported monthly. The target is zero.

---

## 7. Authorization and Least Privilege

### 7.1 Role-Based Access Control

**7.1.1** All access must be assigned through defined roles rather than direct permission grants wherever technically feasible.

**7.1.2** The Identity Administrator must maintain a role catalog documenting each defined role, its permissions, its intended user population, and the business justification for each permission included.

**7.1.3** Users must not hold more than one role that creates a separation-of-duties conflict. Role conflicts must be identified during provisioning and escalated to the Security Lead for resolution before account activation.

**7.1.4** Role assignments must be reviewed as part of the access recertification process in Section 5.4. Any role assignment that cannot be attested during a review period must be removed.

### 7.2 Privileged Access

**7.2.1** Privileged access is defined as any assignment to: Global Administrator, Privileged Role Administrator, User Administrator, Exchange Administrator, Security Administrator, Compliance Administrator, Application Administrator, Cloud Application Administrator, or any custom role with write permissions to identity or security configurations.

**7.2.2** The maximum number of active Global Administrator accounts is two (2). All accounts assigned the Global Administrator role beyond this limit must be downgraded within 30 days of this policy's effective date.

**Driving Evidence (real finding):** At time of the gap assessment, 5 permanent Global Administrator accounts existed. Two is the absolute maximum. The remaining three must be converted to eligible-only via PIM or downgraded to a lower role.

**7.2.3** Privileged role assignments must be justified, documented, and reviewed quarterly. The justification must state: the specific role assigned, the specific business function it supports, the approving manager's name, and the review date.

**7.2.4** Privileged users must use a dedicated privileged account separate from their standard user account for all administrative operations. The privileged account must not be used for email, web browsing, or non-administrative tasks.

**7.2.5** All privileged accounts must be registered with MFA using Microsoft Authenticator or FIDO2 as specified in Section 6.1.4.

### 7.3 Privileged Identity Management

**7.3.1** Privileged Identity Management (PIM) must be implemented for all Entra ID directory role assignments. PIM requires Entra ID P2 licensing. Procurement of the required licensing must be treated as a priority remediation item.

**Driving Evidence (real finding):** At time of the gap assessment, PIM was not configured. Zero eligible assignments existed. All 5 Global Administrator assignments were permanent and active with no time limit, no activation requirement, and no approval workflow.

**7.3.2** All privileged role assignments must be converted from Active (permanent) to Eligible (just-in-time) through PIM. Permanent active assignments are not permitted except for break-glass emergency accounts.

**7.3.3** PIM activation settings must be configured as follows:

| Setting | Requirement |
|---|---|
| Maximum activation duration | 4 hours for standard privileged roles; 1 hour for Global Administrator |
| Require MFA on activation | Yes -- always required |
| Require justification on activation | Yes -- justification text required |
| Require approval | Yes for Global Administrator; recommended for all other privileged roles |
| Activation notification | Yes -- email notification to Security Lead on all GA activations |

**7.3.4** PIM activation logs must be reviewed weekly. Any activation outside business hours, from an unusual location, or without a corresponding change management record must be investigated within 24 hours.

**7.3.5** Break-glass emergency accounts are exempt from PIM eligible-only requirements but must meet: no more than two break-glass accounts may exist, credentials stored offline in a physically secured location, all usage triggers automatic alert to the Security Lead, and all usage must be reviewed within 24 hours and documented.

### 7.4 Service Accounts and Non-Human Identities

**7.4.1** All service principals, managed identities, and application registrations must be documented in a service account inventory including: service name, owning team, intended function, permission scope, credential type, and last review date.

**7.4.2** Service principals must follow least privilege. No service principal may hold a directory role unless the specific role is required for its function.

**7.4.3** Service principal credentials must be rotated on the following schedule:

| Credential Type | Maximum Validity |
|---|---|
| Client Secret | 12 months |
| Certificate | 24 months |

**7.4.4** Service principal credentials nearing expiration (within 30 days) must be rotated before expiry. Expired credentials must be removed immediately.

**7.4.5** Unused service principals (no sign-in activity in the preceding 90 days) must be reviewed and either justified with documented active purpose or disabled and scheduled for deletion.

---

## 8. Guest and External Access

**8.1** Guest accounts must be provisioned through a formal invitation process including: the external user's name and organization, the specific access being granted, a named internal sponsor, and the intended duration of access.

**8.2** Guest accounts must not be assigned privileged directory roles. Assignment of any privileged role to a guest account requires Security Lead approval and must be documented as a formal exception per Section 10.

**8.3** Guest account access must be reviewed on a quarterly basis by the named sponsor. Sponsors who cannot attest within 10 business days must have the guest account disabled.

**8.4** Guest accounts must be disabled no later than 30 days after the expiration of the access period stated at provisioning. Permanent guest access is not permitted without annual re-approval.

**8.5** Total guest account count and access scope must be reported to the Security Lead quarterly.

**Driving Evidence (real finding):** At time of the gap assessment, 2 guest accounts existed with no privileged roles. No formal inventory, review process, or sponsor assignment existed for either account.

---

## 9. Monitoring and Anomaly Response

### 9.1 Sign-In Monitoring

**9.1.1** The Identity Administrator must review SigninLogs on a minimum weekly basis covering: failed sign-in rates by account, MFA completion rates, CA policy application rates, sign-ins from unusual locations, and new device registrations.

**9.1.2** The following KQL query must be used weekly to monitor MFA enforcement effectiveness:

```kql
SigninLogs
| summarize TotalSignIns = count(),
            MFACount     = countif(AuthenticationRequirement == "multiFactorAuthentication"),
            SFACount     = countif(AuthenticationRequirement == "singleFactorAuthentication"),
            CANotApplied = countif(ConditionalAccessStatus   == "notApplied"),
            CAEnforced   = countif(ConditionalAccessStatus   == "success")
    by bin(TimeGenerated, 1d)
| extend MFARate = round(100.0 * MFACount / TotalSignIns, 1)
| order by TimeGenerated desc
```

**Assessment baseline (real data):** MFA rate was 59% at time of assessment. CA notApplied was 203,906 (100%). CA Enforced was 0. This query confirms when enforcement reaches the required 95%+ target.

**9.1.3** Any week in which the MFA completion rate falls below 95% must be investigated and a remediation action opened within 5 business days.

### 9.2 Brute Force and Lockout

**9.2.1** The following thresholds define mandatory investigation triggers:

| Trigger | Threshold | Response SLA |
|---|---|---|
| Single account failed sign-ins | More than 5 failures in 24 hours | Investigate within 24 hours |
| Single account high-volume failures | More than 20 failures in 24 hours | Suspend account within 4 hours |
| Cross-account failures from single IP | More than 20 failures across accounts from one IP in 1 hour | Block IP via CA within 2 hours |
| Failures followed by success | Any account with 5+ failures then a success | Treat as potential compromise per IR Playbook 8.1 |

**Driving Evidence (real finding):** At time of the gap assessment, 30 accounts had more than 5 failed sign-in attempts. The top account recorded 512 failures from an external domain with no lockout and no investigation. This table codifies the response that should have been activated.

**9.2.2** The following KQL query must be run weekly to identify accounts meeting investigation thresholds:

```kql
SigninLogs
| where ResultType != "0"
| summarize FailureCount = count(),
            DistinctIPs  = dcount(IPAddress),
            LastSeen     = max(TimeGenerated)
    by UserPrincipalName
| where FailureCount > 5
| order by FailureCount desc
```

**Assessment baseline (real data):** This query returned 30 accounts at time of assessment. Top account: 512 failures. Post-remediation target: zero accounts above threshold without documented disposition.

### 9.3 Privileged Activity Monitoring

**9.3.1** All changes to directory role assignments must be reviewed within 24 hours of occurrence:

```kql
AuditLogs
| where TimeGenerated > ago(7d)
| where Category == "RoleManagement"
| project TimeGenerated, OperationName, InitiatedBy, TargetResources, Result
| order by TimeGenerated desc
```

**9.3.2** Any role assignment change that cannot be correlated to an approved provisioning request or PIM activation record must be treated as a potential unauthorized change and investigated within 4 hours.

**9.3.3** PIM activation logs must be reviewed weekly. Activations outside business hours or from unusual locations must be escalated to the Security Lead within 24 hours.

---

## 10. Access Exceptions and Waivers

**10.1** Any access configuration that cannot comply with this policy must be documented as a formal exception including: the specific requirement being excepted, the technical or operational reason compliance is not currently feasible, the compensating control in place, the risk acceptance statement, the approver (Security Lead), and the exception expiration date (maximum 90 days; renewable with re-approval).

**10.2** No exception may be granted for the following requirements under any circumstances:
- MFA for Global Administrator accounts (Section 6.1.4)
- Maximum Global Administrator account count of two (Section 7.2.2)
- Logging and monitoring of privileged role assignments (Section 9.3.1)

**10.3** All active exceptions must be tracked in the risk register and reviewed at each quarterly privileged access review.

---

## 11. Enforcement and Consequences

**11.1** Violations of this policy are subject to disciplinary action up to and including suspension of access, removal from the program, and referral to appropriate institutional or legal authorities.

**11.2** The Identity Administrator is responsible for identifying and documenting violations. The Security Lead determines the appropriate response.

**11.3** The following violations are classified as severe and may result in immediate access revocation without prior notice:
- Sharing of credentials or authentication tokens
- Bypass of MFA through any unauthorized method
- Unauthorized modification of Conditional Access policies
- Creation of accounts outside the provisioning process
- Assignment of privileged roles outside the PIM process

**11.4** The Security Lead must be notified within 24 hours of the discovery of any severe violation.

---

## 12. Policy Review and Maintenance

**12.1** This policy must be reviewed annually by the Identity Administrator and GRC Analyst assessing: whether all requirements remain technically appropriate, whether new attack patterns require updated requirements, and whether compliance metrics from the preceding year indicate systemic gaps.

**12.2** This policy must also be reviewed following: any identity or access-related incident classified as High or Critical, any significant change to the identity platform or connected systems, or a regulatory or audit finding related to access control.

**12.3** All revisions must be version-controlled. Version history must be maintained for a minimum of 3 years.

**12.4** This policy is effective as of March 5, 2026. Next scheduled review: March 5, 2027.

---

## 13. Related Documents and References

| Document | Status | Relationship |
|---|---|---|
| Information Security Policy | Active (March 5, 2026) | Parent policy -- this document operationalizes Sections 4.1, 4.2 |
| Risk Register | Active (March 5, 2026) | RISK-001, RISK-002, RISK-007, RISK-008 map directly to this policy |
| Incident Response Plan | Active (March 5, 2026) | IR Playbooks 8.1, 8.2, 8.4 implement Section 9 of this policy |
| Plan of Action and Milestones | Active | POA-001, POA-002, POA-007, POA-008 remediated by this policy |
| Privileged Access Management Standard | Pending | Will expand Section 7.3 with detailed PIM procedures |
| System Security Plan | Pending | Will document implemented controls from this policy |

**Frameworks Referenced:**
- NIST Special Publication 800-53 Revision 5 (AC-1 through AC-25, IA-1 through IA-12)
- NIST Cybersecurity Framework 2.0 (PR.AA -- Identity Management, Authentication and Access Control)
- ISO/IEC 27001:2022 (Annex A 5.15, 5.16, 5.17, 5.18, 8.2, 8.3, 8.5)
- SOC 2 Trust Services Criteria (CC6.1, CC6.2, CC6.3, CC6.7)
- CIS Controls v8 (Control 5: Account Management, Control 6: Access Control Management)

---

## 14. Approval and Sign-Off

| Role | Name | Signature | Date |
|---|---|---|---|
| Security Lead / Cybersecurity Owner | [To be completed] | [Signature required] | March 5, 2026 |
| Identity Administrator | [To be completed] | [Signature required] | March 5, 2026 |
| GRC Analyst | Alex Truong | [Signature required] | March 5, 2026 |

**Policy Effective Date:** March 5, 2026
**Next Review Date:** March 5, 2027
**Version:** 1.0
**Classification:** CONFIDENTIAL

---

*End of Document*

*Organization: ABC Corp | Microsoft Entra ID Tenant. This Access Control Policy was produced from a real gap assessment conducted against a live Azure cloud environment. All requirements trace to specific deficiencies confirmed through live telemetry. All organization names and identifiers have been sanitized. Published for portfolio and educational purposes only.*
