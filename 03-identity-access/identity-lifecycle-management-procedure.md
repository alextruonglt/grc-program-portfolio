# Identity Lifecycle Management Procedure

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity and Access Governance Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Document Type:** Operational Procedure (subordinate to the Access Control Policy and PAM Standard)

> **Disclaimer:** This is a sanitized version of an Identity Lifecycle Management Procedure produced following a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This procedure was produced as a direct deliverable from a real, evidence-based gap assessment of a live Microsoft Azure shared tenant. Every process step and data requirement traces to a specific identity governance deficiency confirmed through live Microsoft Entra ID exports and AuditLogs telemetry gathered at assessment time. This is not a generic template. Each step closes a documented gap in a production environment.

**Driving Findings (real data):**
- 100% of 2,315 user accounts had blank Department, Job Title, and Manager fields (real finding -- confirmed through Entra ID export)
- No formal provisioning, deprovisioning, or access review process existed (real finding)
- 30 accounts recorded more than 5 failed sign-in attempts with no investigation (real finding)
- Top account recorded 512 failed sign-in attempts from an external domain with no lockout (real finding)
- 2 guest accounts existed with no documented sponsor, no expiration date, and no review record (real finding)

**Assessment Standards Applied:** NIST SP 800-53 Rev 5 | NIST SP 800-63A | NIST CSF 2.0 | ISO/IEC 27001:2022 | SOC 2 TSC | CIS Controls v8

---

## Table of Contents

1. Purpose and Scope
2. Roles and Responsibilities
3. Identity Types and Classification
4. Joiner Procedure (Account Provisioning)
5. Mover Procedure (Role and Attribute Changes)
6. Leaver Procedure (Account Deprovisioning)
7. Account Attribute Standards
8. MFA Registration Procedure
9. Access Review Procedure
10. Anomaly and Incident Triggers
11. Record Retention
12. Procedure Exceptions
13. Related Documents and References
14. Approval and Sign-Off

---

## 1. Purpose and Scope

### 1.1 Purpose

This Identity Lifecycle Management Procedure defines the step-by-step operational processes for creating, modifying, reviewing, and removing identities within the ABC Corp cloud tenant. It governs the full identity lifecycle from initial account request through final deletion, covering all identity types: standard users, privileged users, guest accounts, and service principals.

This procedure was created in direct response to identity governance deficiencies confirmed through live telemetry:

- Every one of 2,315 user accounts in the tenant had a blank Department field. The same was true for Job Title and Manager. This rendered attribute-based access reviews, anomaly investigation by user role, and RBAC group-based access assignment impossible. (real finding)
- No formal provisioning process existed. Accounts appeared in the directory with no evidence of a documented request, approval, or onboarding step. (real finding)
- No deprovisioning records existed. There was no way to confirm that inactive accounts had been reviewed or intentionally retained. (real finding)
- Two guest accounts existed with no documented sponsor, no access review record, and no expiration date. (real finding)
- Thirty accounts with more than 5 failed sign-in attempts went uninvestigated. The top account had 512 failures from an external domain with no lockout and no response. (real finding)

### 1.2 Scope

**Covered Identities:**
- All standard user accounts in the tenant
- All privileged user accounts (Tier 0 through Tier 3 per the PAM Standard)
- All guest accounts (external identities with #EXT# suffix in UPN)
- All service principals and managed identities in the tenant

**Out of Scope:**
- Physical access control provisioning
- Isolated lab environment accounts that do not authenticate against the production tenant
- Microsoft-managed service accounts not visible in the Entra ID Users blade

---

## 2. Roles and Responsibilities

| Role | Lifecycle Responsibilities |
|---|---|
| Identity Administrator | Execute all provisioning, modification, and deprovisioning steps. Own the account attribute inventory. Conduct and document access reviews. Monitor and respond to anomaly triggers. |
| Security Lead | Approve privileged account provisioning. Approve exceptions to this procedure. Receive and review access review summary reports. Escalation point for lifecycle violations. |
| GRC Analyst | Maintain this procedure documentation. Track open lifecycle items in the POA&M. Report compliance metrics to the Security Lead. Update this procedure following incidents or assessment findings. |
| Cloud Administrator | Provision and deprovision service principal and managed identity access. Own the service account inventory. Attest to service principal access during quarterly reviews. |
| Managers and Program Administrators | Submit provisioning requests with business justification. Attest to their direct reports' access during access reviews. Notify the Identity Administrator within 1 business day when a user's role changes or their access should be removed. |
| All Users | Complete MFA registration before first sign-in. Report suspected account compromise immediately. Not share credentials or authentication tokens. Complete security awareness training within 30 days of account creation and annually thereafter. |

---

## 3. Identity Types and Classification

| Identity Type | Description | Examples | Review Cadence |
|---|---|---|---|
| Standard User | Human user with no privileged directory role assignment | General staff, students, non-admin users | Annual |
| Privileged User | Human user with one or more Tier 0 through Tier 3 role assignments per the PAM Standard | Global Administrators, Security Administrators, User Administrators | Quarterly |
| Guest User | External identity invited to the tenant. Identified by #EXT# in UPN | External contractors, partner users, vendor accounts | Quarterly |
| Service Account | Non-human identity representing an application or automated process | Monitoring agents, automation accounts, scanner credentials | Quarterly |
| Break-Glass Account | Emergency access account with permanent Global Administrator assignment | bg-account01, bg-account02 | Semi-annual credential verification |

---

## 4. Joiner Procedure (Account Provisioning)

### 4.1 Standard User Provisioning

**Trigger:** Enrollment confirmation, hiring confirmation, or formal access request submitted by a manager or program administrator.

**Step 1 -- Request and Approval**
- The requesting manager submits a provisioning request including: full legal name, intended UPN, Department, Job Title, Manager UPN, start date, and specific access required.
- The Identity Administrator reviews for completeness. Incomplete requests must be returned within 1 business day.
- The Identity Administrator approves or rejects within 2 business days of a complete request.

**Step 2 -- Account Creation**

Create the account in Entra ID with all mandatory attributes populated at creation:

| Attribute | Requirement |
|---|---|
| Display Name | Full legal name or approved alias |
| User Principal Name | Follows naming convention in PAM Standard Section 3.2 |
| Department | Organizational unit or program area (not blank) |
| Job Title | Role or classification (not blank) |
| Manager | Named manager UPN (not blank) |
| Usage Location | ISO country code (required for license assignment) |
| Account Enabled | Set to False until MFA registration is confirmed |

**Driving Evidence (real finding):** At time of the gap assessment, 100% of 2,315 accounts had blank Department, Job Title, and Manager fields. All three are mandatory at account creation from the effective date of this procedure. Accounts created with blank mandatory attributes will be suspended pending remediation.

**Step 3 -- License and Group Assignment**
- Assign only the licenses required for the user's role. Do not assign licenses beyond what the role requires.
- Assign the user to the appropriate security groups or M365 groups. Use group-based access wherever available. Do not make direct role assignments.
- Standard users must not receive any privileged directory role assignment at this step.

**Step 4 -- MFA Registration**
- Send the user a Temporary Access Pass (TAP) and MFA registration instructions via an out-of-band channel.
- The user must complete MFA registration per Section 8.1 before the account is enabled.
- Set Account Enabled to True only after MFA registration is confirmed.

**Step 5 -- Provisioning Record**
- Document in the identity management log: request date, requester name, approver name, account UPN, attributes assigned, groups assigned, MFA registration confirmation date, and account enable date.
- Retain for a minimum of 2 years.

**SLA:** Account creation within 2 business days of approved request. Account enabled within 1 business day of MFA registration confirmation.

### 4.2 Privileged User Provisioning

Privileged user provisioning follows Section 4.1 first. After the standard account is created and enabled, privileged access is provisioned as a separate additional step.

**Trigger:** Formal privileged access request approved by the Security Lead. Standard user account must already exist and be active.

**Step 1 -- Privileged Access Request**
- Submit a request including: standard account UPN, specific role tier and role name, business justification, and expected duration.
- Security Lead must approve all Tier 0 and Tier 1 requests. Identity Administrator may approve Tier 2 and Tier 3 requests.

**Step 2 -- Dedicated Privileged Account Creation**
- Create a separate dedicated privileged account following the naming convention (adm-firstname.lastname@tenant.domain).
- Populate all mandatory attributes including extensionAttribute1 (Tier classification) and extensionAttribute2 (linked standard user UPN).
- The privileged account must not be used to receive email or access productivity applications.

**Step 3 -- MFA Registration for Privileged Account**
- Microsoft Authenticator (number matching) or FIDO2 hardware security key only. SMS and voice MFA are not permitted for any privileged account.

**Step 4 -- PIM Eligible Assignment**
- Configure a PIM Eligible assignment for the approved role. Do not create a Permanent Active assignment except for break-glass accounts.
- Set the activation policy per PAM Standard Section 4.3.
- Test the PIM activation workflow with the user before closing the provisioning record.

**SLA:** Privileged provisioning completed within 3 business days of Security Lead approval.

### 4.3 Guest and External User Provisioning

**Trigger:** Internal sponsor submits a guest access request.

- The internal sponsor submits a request including: external user's full name and organization, specific access being granted and why, intended duration, and sponsor's agreement to attest during quarterly reviews.
- No guest account may be created without a named, accountable internal sponsor.
- Any guest access request that includes a privileged role requires Security Lead approval.
- Send the invitation using Microsoft Entra B2B invitation. Set an access expiration date. Maximum initial duration is 90 days. Renewal requires sponsor re-attestation.

**Driving Evidence (real finding):** At time of the gap assessment, 2 guest accounts existed with no documented sponsor, no access scope, and no review record for either account.

**SLA:** Guest invitation sent within 2 business days of approved request.

### 4.4 Service Account and Service Principal Provisioning

- Submit a request including: service name, intended function, specific permissions required and why, credential type (managed identity preferred), and expected lifespan.
- Security Lead must approve any service principal that requires a directory role assignment.
- Assign only the minimum permissions required for the documented function.
- For client secret credentials: generate the secret, store it immediately in Azure Key Vault, and record the expiration date in the service account inventory.

**SLA:** Service account provisioned within 3 business days of approved request.

---

## 5. Mover Procedure (Role and Attribute Changes)

### 5.1 Internal Role Change

**Trigger:** Manager submits a role change request.

1. Submit a request documenting: current role, new role, effective date, and reason for the change.
2. Identity Administrator identifies: access to be added, access to be removed, and access that carries over unchanged.
3. Execute all additions and removals on the effective date. Removal must occur simultaneously with or before addition. Access changes must not be split across multiple days.
4. Update all affected attributes (Department, Job Title, Manager) to reflect the new role.
5. If the change involves promotion to privileged access, execute Section 4.2 privileged provisioning.
6. If the change involves removal of privileged access, execute Section 6.1 deprovisioning steps for the privileged account.

**SLA:** Role change executed on the requested effective date or within 1 business day if the effective date has already passed.

### 5.2 Temporary Access Grants

- All temporary access grants must have a defined end date. No temporary grant may be open-ended.
- Maximum duration without Security Lead re-approval is 30 days.
- The Identity Administrator must set a calendar reminder to revoke the access on the end date.
- Temporary privileged access must use PIM time-limited Active assignments per PAM Standard Section 4.2.

### 5.3 Attribute Updates

- All mandatory attributes must be updated within 2 business days of notification.
- Manager changes must be reflected in the Manager attribute, as the new manager will be the access review attestor at the next review cycle.
- The Identity Administrator must perform a monthly attribute completeness report per the Access Control Policy.

---

## 6. Leaver Procedure (Account Deprovisioning)

### 6.1 Planned Departure

**Step 1 -- Pre-Departure Preparation (5 or more business days before departure)**
- Identify all access held by the departing user: group memberships, role assignments, PIM eligible assignments, application permissions.
- Confirm with the manager whether any data or delegated tasks must be transferred before departure.

**Step 2 -- Account Disable (on departure date, before end of business)**
- Set Account Enabled to False.
- Revoke all active sessions (Entra ID: Users > select user > Revoke sessions).
- Revoke all refresh tokens.
- For privileged accounts: remove all role assignments and PIM eligible assignments on the departure date.

**Timing:** Privileged account disable and role removal within 4 hours of departure date/time. Standard account disable by end of business on the departure date.

**Step 3 -- Post-Departure Monitoring (30 days)**
- Monitor SigninLogs for any sign-in attempt against the disabled account.
- Any successful sign-in against a disabled account is a critical security incident.

**Step 4 -- Account Deletion (30 days after disable)**
- Permanently delete the account 30 days after disable unless a legal hold mandates extended retention.
- Remove all group memberships, role assignments, application permissions, and OAuth consent grants at deletion.

**SLA:** Disable by end of business on departure date. Deletion at 30 days unless hold applies.

### 6.2 Unplanned or Immediate Termination

**Trigger:** Manager or Security Lead requests immediate account removal.

**Immediate actions (within 1 hour of notification):**
1. Set Account Enabled to False
2. Revoke all active sessions
3. Revoke all refresh tokens
4. Remove all privileged role assignments and PIM eligible assignments
5. Block sign-in via Conditional Access if account disable alone is not sufficient
6. Remove from any shared distribution lists or mailbox delegations
7. Notify the Security Lead

**Post-disable (within 24 hours):**
- Review AuditLogs for any actions taken by the account in the 7 days preceding disable
- Review for data exports, permission changes, or unauthorized account creations

### 6.3 Guest Account Expiration

- 14 days before expiration, send a renewal notice to the internal sponsor.
- Sponsor must confirm continued need and re-attest within 10 business days.
- If no response: disable on the expiration date, revoke all sessions, queue for deletion at 30 days.
- If the sponsor is no longer at the organization: disable immediately.

**Driving Evidence (real finding):** At time of the gap assessment, 2 guest accounts existed with no expiration date, no sponsor, and no review record. Both lacked the documentation required to make a renewal or revocation decision.

### 6.4 Inactive Account Remediation

**Trigger:** Weekly monitoring query identifies accounts with no sign-in activity in the preceding 30 days (standard users) or 14 days (privileged users).

```kql
SigninLogs
| where TimeGenerated > ago(30d)
| summarize LastSignIn = max(TimeGenerated) by UserPrincipalName
| where LastSignIn < ago(30d)
    or isnull(LastSignIn)
| order by LastSignIn asc
```

**Response:** Identity Administrator contacts the account's manager or sponsor. Manager must respond within 5 business days. No response means account is disabled and placed in the 30-day retention period.

---

## 7. Account Attribute Standards

### 7.1 Mandatory Attributes

| Attribute | Blank Permitted |
|---|---|
| Display Name | No |
| User Principal Name | No |
| Department | No |
| Job Title | No |
| Manager | No (except the top of the reporting chain) |
| Usage Location | No |
| Account Enabled | No |

**Driving Evidence (real finding):** At time of the gap assessment, 100% of 2,315 accounts had blank Department, Job Title, and Manager fields. This is the single most impactful remediation item for identity governance maturity in the environment assessed.

### 7.2 Attribute Remediation Procedure

1. Export the full user list from Entra ID Users blade (All Users > Export to CSV) to identify accounts with blank mandatory attributes.
2. Send a bulk attribute update request to all managers, requesting confirmation of Department, Job Title, and Manager for each of their direct reports. Set a 30-day response deadline.
3. Update attributes using the Entra ID bulk user update feature or PowerShell (Update-MgUser). Verify by re-exporting and confirming no blank mandatory attributes remain.
4. Any account whose mandatory attributes cannot be confirmed within 60 days of the procedure effective date must be suspended pending attribute completion.

**Completion Target:** Zero accounts with blank mandatory attributes within 60 days of the effective date.

---

## 8. MFA Registration Procedure

### 8.1 Initial Registration

1. Identity Administrator generates a Temporary Access Pass (TAP). Valid for 24 hours, single use.
2. Identity Administrator delivers the TAP and registration instructions via an out-of-band channel (manager relay or institutional email separate from the new account).
3. User signs in with the TAP and is prompted to register an MFA method.
4. Standard users: Microsoft Authenticator (push notification) is the recommended method. TOTP is acceptable. SMS is permitted as a last resort only and must be documented.
5. Privileged users: Microsoft Authenticator (number matching) or FIDO2 hardware security key only. SMS and voice are not permitted.
6. Identity Administrator confirms the registration is present under the user's Authentication Methods blade.
7. Identity Administrator sets Account Enabled to True and documents the MFA registration confirmation date in the provisioning record.

**If MFA registration is not completed within 5 business days of TAP issuance:** Cancel the TAP, notify the requesting manager, and do not enable the account.

### 8.2 MFA Method Change

**Authorized change:**
1. User submits an MFA method change request to the Identity Administrator.
2. Identity Administrator verifies the request is legitimate by contacting the user through a channel separate from the account being modified.
3. Identity Administrator updates the authentication method and documents the change with: date, old method, new method, and the verification step taken.

**Unauthorized change detected:**
- Any AuditLogs entry for "User registered security info" or "User changed default security info" not correlated to a documented change request must be treated as potential account compromise.
- Escalate immediately to the Security Lead and follow IR Playbook 8.1.

```kql
AuditLogs
| where TimeGenerated > ago(7d)
| where OperationName in (
    "User registered security info",
    "User changed default security info",
    "User deleted security info",
    "Admin registered security info for a user"
  )
| project TimeGenerated, OperationName, InitiatedBy,
          TargetResources, Result
| order by TimeGenerated desc
```

### 8.3 MFA Reset

**Identity verification required before reset. Acceptable methods:**
- In-person identity verification with government-issued ID
- Video call with government-issued ID presented on screen
- Manager attestation via signed email from the manager's institutional address

Phone verification alone is not acceptable.

**Reset steps:**
1. Verify identity and document the method used and date.
2. Delete all existing authentication methods in the Entra ID Authentication Methods blade.
3. Generate a new TAP (single use, 24-hour validity) and deliver via out-of-band channel.
4. User re-registers MFA per Section 8.1. Document the re-registration confirmation.

**Privileged account MFA reset:** Requires Security Lead awareness and approval before execution.

---

## 9. Access Review Procedure

### 9.1 Quarterly Privileged Access Review

**Scope:** All Tier 0 through Tier 3 role assignments, all PIM eligible assignments, all service principal assignments with directory roles or subscription Owner/Contributor rights.

**Cadence:** Quarterly. Completed within the first 15 business days of each quarter.

**Process:**
1. GRC Analyst generates an access inventory report at least 5 business days before the review deadline.
2. Reviewers receive their review packages. Security Lead reviews Tier 0 and Tier 1. Identity Administrator reviews Tier 2 and Tier 3, PIM eligible assignments, and guest accounts. Cloud Administrator reviews service principals.
3. Reviewers have 10 business days to attest that: the account is still required, the account owner's current role still requires this access, no excessive permissions exist, and all attributes are accurate.
4. Any assignment not attested within 10 business days is automatically revoked.
5. GRC Analyst compiles a review summary report submitted to the Security Lead within 5 business days of the attestation deadline.

### 9.2 Annual Standard User Review

**Cadence:** Annual, conducted during Q1 of each calendar year.

1. Export full user list. Identify accounts with no sign-in in the preceding 90 days, blank mandatory attributes, and accounts with no manager (orphaned accounts).
2. Send each manager a list of their direct reports' accounts for attestation. Attestation deadline: 10 business days.
3. Accounts with no manager attribute that cannot be matched to an active user must be suspended within 5 business days and queued for deletion at 30 days.

### 9.3 Guest Account Review

**Cadence:** Quarterly (aligned with the privileged access review).

- Identity Administrator contacts each guest account's named sponsor for re-attestation.
- Sponsors who attest within 10 business days: account extended.
- Sponsors who do not respond within 10 business days: guest account disabled immediately.
- If the internal sponsor is no longer at the organization: account disabled immediately.

### 9.4 Review Failure and Automatic Revocation

| Condition | Action |
|---|---|
| Reviewer does not attest within 10 business days | Access revoked by Identity Administrator |
| Manager is no longer at the organization and no substitute is identified | User access suspended |
| Guest account sponsor is no longer at the organization | Guest account disabled immediately |
| Privileged account MFA found non-compliant during review | Account suspended until MFA is corrected |
| Attribute fields found blank during review | Attributes must be corrected within 5 business days or account is suspended |

---

## 10. Anomaly and Incident Triggers

### 10.1 Failed Sign-In Thresholds

| Threshold | Trigger Action | Response Owner | SLA |
|---|---|---|---|
| 5 or more failed sign-ins in 24 hours (single account) | Review account for signs of compromise. Check if failures were followed by a success. | Identity Administrator | Within 24 hours |
| 20 or more failed sign-ins in 24 hours (single account) | Suspend the account pending investigation. Notify Security Lead. | Identity Administrator | Within 4 hours |
| 20 or more failures from a single IP across multiple accounts within 1 hour | Block source IP via Conditional Access. Notify Security Lead. | Identity Administrator | Within 2 hours |
| Any account with 5+ failures then a successful sign-in | Treat as potential account compromise. Follow IR Playbook 8.1 immediately. | Security Lead | Immediate |

**Driving Evidence (real finding):** At time of the gap assessment, 30 accounts exceeded the 5-failure threshold with no response. The highest-volume account had 512 failures from an external domain with no lockout triggered and no investigation. This table codifies the response that should have been activated.

### 10.2 Inactive Account Detection

| Account Type | Inactivity Period | Action |
|---|---|---|
| Privileged account | No sign-in in 14 days | Contact account owner within 2 business days. Disable if no response. |
| Standard user | No sign-in in 30 days | Contact manager within 5 business days. Disable if no response. |
| Guest account | No sign-in in 30 days | Contact sponsor within 5 business days. Disable if no response. |
| Service principal | No sign-in in 90 days | Contact owning team within 5 business days. Disable if no response. |

### 10.3 KQL Detection Queries

**New accounts created in the last 7 days:**

```kql
AuditLogs
| where TimeGenerated > ago(7d)
| where OperationName == "Add user"
| project TimeGenerated,
          NewUserUPN  = tostring(TargetResources[0].userPrincipalName),
          InitiatedBy = tostring(InitiatedBy.user.userPrincipalName),
          Result
| order by TimeGenerated desc
```

**Accounts disabled or deleted in the last 7 days:**

```kql
AuditLogs
| where TimeGenerated > ago(7d)
| where OperationName in ("Disable account", "Delete user", "Update user")
| project TimeGenerated, OperationName,
          TargetUPN   = tostring(TargetResources[0].userPrincipalName),
          InitiatedBy = tostring(InitiatedBy.user.userPrincipalName),
          Result
| order by TimeGenerated desc
```

**Failed sign-in summary (last 24 hours):**

```kql
SigninLogs
| where TimeGenerated > ago(24h)
| where ResultType != "0"
| summarize FailureCount = count(),
            DistinctIPs  = dcount(IPAddress),
            LastSeen     = max(TimeGenerated)
    by UserPrincipalName, ResultDescription
| where FailureCount > 5
| order by FailureCount desc
```

**Assessment baseline (real data):** This query returned 30 accounts at time of assessment. Top account: 512 failures. Post-remediation target: zero accounts above threshold without documented disposition.

**MFA registration events (last 7 days):**

```kql
AuditLogs
| where TimeGenerated > ago(7d)
| where OperationName in (
    "User registered security info",
    "User changed default security info",
    "User deleted security info",
    "Admin registered security info for a user"
  )
| project TimeGenerated, OperationName, InitiatedBy,
          TargetResources, Result
| order by TimeGenerated desc
```

---

## 11. Record Retention

| Record Type | Retention Period |
|---|---|
| Account provisioning records | 2 years from account deletion |
| Account deprovisioning records | 2 years from account deletion |
| Access review records and attestations | 2 years from review date |
| MFA registration and reset records | 2 years from account deletion |
| Privileged access change records | 3 years from change date |
| Anomaly investigation records | 3 years from investigation close |
| Break-glass post-use review records | 2 years from use date |

---

## 12. Procedure Exceptions

**12.1** Any deviation must be documented as a formal exception including: the specific step being excepted, the reason for the deviation, the compensating control in place, the approver, and the exception expiration date (maximum 30 days).

**12.2** No exception may be granted for:
- Account disable on departure date for privileged users (Section 6.1 Step 2)
- MFA verification before account enable (Section 4.1 Step 4)
- Identity verification before MFA reset (Section 8.3)
- Automatic revocation of unattested access (Section 9.4)

**12.3** All active exceptions must be reported to the Security Lead within 24 hours and tracked in the POA&M until resolved.

---

## 13. Related Documents and References

| Document | Status | Relationship |
|---|---|---|
| Access Control Policy | Active (March 5, 2026) | Parent policy -- this procedure operationalizes Sections 5.1 through 5.4 |
| PAM Standard | Active (March 5, 2026) | Governs privileged account provisioning steps referenced throughout |
| Information Security Policy | Active (March 5, 2026) | Governing policy |
| Incident Response Plan | Active (March 5, 2026) | IR Playbooks 8.1 and 8.4 activated by anomaly triggers in Section 10 |
| Risk Register | Active (March 5, 2026) | RISK-001, RISK-007, RISK-008 directly addressed by this procedure |
| Plan of Action and Milestones | Active | POA-007 is the primary remediation item |
| System Security Plan | Pending | Will document implemented controls from this procedure |

**Frameworks Referenced:**
- NIST Special Publication 800-53 Revision 5 (AC-2, AC-2(1), AC-2(3), AC-2(4), IA-4, IA-5, IA-8)
- NIST Special Publication 800-63A (Enrollment and Identity Proofing)
- NIST Cybersecurity Framework 2.0 (PR.AA-01, PR.AA-02, PR.AA-03, PR.AA-05, PR.AA-06)
- ISO/IEC 27001:2022 (Annex A 5.15, 5.16, 5.18, 6.1, 6.5)
- SOC 2 Trust Services Criteria (CC6.1, CC6.2, CC6.3)
- CIS Controls v8 (Control 5: Account Management, Control 6: Access Control Management)

---

## 14. Approval and Sign-Off

| Role | Name | Signature | Date |
|---|---|---|---|
| Security Lead / Cybersecurity Owner | [To be completed] | [Signature required] | March 5, 2026 |
| Identity Administrator | [To be completed] | [Signature required] | March 5, 2026 |
| GRC Analyst | Alex Truong | [Signature required] | March 5, 2026 |

**Document Effective Date:** March 5, 2026
**Next Review Date:** March 5, 2027
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Parent Documents:** Access Control Policy v1.0 | PAM Standard v1.0

---

*End of Document*

*Organization: ABC Corp | Microsoft Entra ID Tenant. This Identity Lifecycle Management Procedure was produced from a real gap assessment conducted against a live Azure cloud environment. All process steps trace to specific identity governance deficiencies confirmed through live telemetry. All organization names and identifiers have been sanitized. Published for portfolio and educational purposes only.*
