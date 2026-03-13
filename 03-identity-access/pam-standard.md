# Privileged Access Management (PAM) Standard

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity and Access Governance Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Document Type:** Technical Standard (subordinate to the Access Control Policy)

> **Disclaimer:** This is a sanitized version of a Privileged Access Management Standard produced following a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This PAM Standard was produced as a direct deliverable from a real, evidence-based gap assessment of a live Microsoft Azure shared tenant. Every requirement in this document traces to a specific privileged access deficiency confirmed through live telemetry from Microsoft Entra ID, AuditLogs, and SigninLogs gathered at assessment time. This is not a generic template -- each requirement closes a documented gap in a production environment.

**Driving Findings (real data):** Five permanent Global Administrator accounts with zero PIM configuration confirmed through Entra ID portal export. PIM portal showed zero eligible assignments, zero active PIM assignments, and zero activation history. No approval workflow existed for any privileged role. No notification was configured for any privileged role activation. Directly remediates POA-002.

**Assessment Standards Applied:** NIST SP 800-53 Rev 5 | NIST SP 800-63B | NIST CSF 2.0 | ISO/IEC 27001:2022 | SOC 2 TSC | CIS Controls v8

---

## Table of Contents

1. Purpose and Scope
2. Privileged Role Taxonomy
3. Privileged Account Standards
4. Privileged Identity Management (PIM) Configuration
5. Just-In-Time Access Procedures
6. Break-Glass Emergency Access
7. Privileged Account Authentication
8. Privileged Access Workstations
9. Service Principal and Workload Identity Standards
10. Privileged Access Monitoring
11. Privileged Access Review and Recertification
12. Separation of Duties Matrix
13. Remediation Roadmap
14. Related Documents and References
15. Approval and Sign-Off

---

## 1. Purpose and Scope

### 1.1 Purpose

This Privileged Access Management (PAM) Standard defines the technical requirements, procedures, and controls governing all privileged access within the ABC Corp cloud tenant. It operationalizes the privileged access requirements established in the Access Control Policy (Sections 7.2 and 7.3) by providing specific configuration standards, activation procedures, monitoring requirements, and remediation guidance.

A privileged access failure is not a theoretical risk. The gap assessment confirmed the following state in a live production tenant:

- Five accounts held permanent Global Administrator role assignments with no time limit, no activation requirement, and no approval workflow (real finding)
- Privileged Identity Management was not configured -- PIM portal showed zero eligible assignments, zero active PIM assignments, and zero activation history (real finding)
- No approval workflow existed for any privileged role (real finding)
- No notification was configured for privileged role activations (real finding)
- AuditLogs showed role assignment operations with no correlation to change management records (real finding)

This standard provides the specific technical implementation requirements that eliminate each of these deficiencies.

### 1.2 Scope

**In Scope:**
- All Microsoft Entra ID directory role assignments (built-in and custom)
- All Azure subscription and resource group RBAC role assignments at Owner, Contributor, and User Access Administrator level
- All service principals and managed identities with directory roles or Azure Owner/Contributor rights
- All break-glass emergency access accounts
- All accounts with delegated administration rights over any ABC Corp system

**Out of Scope:**
- Standard user accounts without any role assignment above the default user permission level
- Read-only role assignments (e.g., Security Reader, Reports Reader)
- Isolated lab environments that do not connect to the production tenant boundary

---

## 2. Privileged Role Taxonomy

All privileged roles are classified into four tiers. Tier determines the activation policy, approval requirements, maximum duration, and monitoring intensity applied.

| Tier | Classification | Roles Included | Rationale |
|---|---|---|---|
| Tier 0 | Critical | Global Administrator, Privileged Role Administrator | Full tenant control. Compromise equals complete tenant takeover. Maximum restrictions apply. |
| Tier 1 | High | User Administrator, Exchange Administrator, Security Administrator, Compliance Administrator | Broad user and security impact. Near-tenant-level blast radius. |
| Tier 2 | Elevated | Application Administrator, Cloud Application Administrator, Intune Administrator, Teams Administrator | Application and service-layer impact. Significant but contained blast radius. |
| Tier 3 | Standard Privileged | Azure Subscription Owner, Azure Contributor, Custom roles with write access to identity or security config | Resource-level impact. JIT required but shorter approval chain. |

**Driving Evidence (real finding):** At time of the gap assessment, all five permanent GA accounts were Tier 0 by this taxonomy with zero Tier 0 controls applied. Maximum count is 2. Three accounts required immediate downgrade.

---

## 3. Privileged Account Standards

### 3.1 Dedicated Privileged Accounts

**3.1.1** All users performing Tier 0 or Tier 1 privileged operations must use a dedicated administrative account entirely separate from their standard daily-use account. The accounts must not share any credential, MFA method, or device session.

**3.1.2** Dedicated privileged accounts must be used exclusively for administrative tasks. Prohibited activities on a privileged account:
- Reading or sending email
- Web browsing (except administrative portals)
- Accessing productivity applications for personal use
- Running non-administrative scripts or code

**3.1.3** Tier 2 and Tier 3 privileged operations may be performed from the standard user account provided PIM just-in-time activation is used and standard daily-use activities are not performed during the elevated session.

### 3.2 Account Naming Convention

| Account Type | UPN Format |
|---|---|
| Standard user account | firstname.lastname@tenant.domain |
| Tier 0 and Tier 1 admin account | adm-firstname.lastname@tenant.domain |
| Break-glass account | bg-account01@tenant.domain |
| Service principal | svc-servicename (display name) |

### 3.3 Account Attribute Requirements

In addition to the mandatory attributes in the Access Control Policy, all privileged accounts must have:

| Attribute | Requirement |
|---|---|
| Department | "IT Administration" or specific team name |
| Job Title | Role description reflecting administrative function |
| Manager | Named security lead or administrator supervisor |
| extensionAttribute1 | Tier classification (Tier0, Tier1, Tier2, Tier3) |
| extensionAttribute2 | Linked standard user account UPN |

### 3.4 Maximum Account Counts by Role Tier

| Tier | Role | Maximum Active Accounts |
|---|---|---|
| Tier 0 | Global Administrator | 2 (includes break-glass accounts) |
| Tier 0 | Privileged Role Administrator | 2 |
| Tier 1 | User Administrator | 3 |
| Tier 1 | Security Administrator | 3 |
| Tier 1 | Exchange Administrator | 3 |
| Tier 2 | Any individual Tier 2 role | 5 |
| Tier 3 | Azure Subscription Owner | 3 per subscription |

**Driving Evidence (real finding):** At time of the gap assessment, 5 permanent Global Administrator accounts existed against a maximum of 2. Three accounts required immediate downgrade or conversion to PIM eligible-only status.

---

## 4. Privileged Identity Management (PIM) Configuration

### 4.1 Licensing Requirements

PIM requires Microsoft Entra ID P2 licensing. Licensing must be assigned to every user who will hold a PIM eligible assignment and to every approver in a PIM approval workflow.

**4.1.1 Compensating Controls (Pre-PIM):** Until PIM is licensed and configured:
- Global Administrator account count must be reduced to 2 immediately
- All remaining GA accounts must have MFA enforced via Conditional Access with no exceptions
- GA account sign-in must be reviewed daily using the KQL queries in Section 10
- Any GA role assignment change must require written Security Lead approval before execution

### 4.2 Assignment Type Standards

| Assignment Type | Definition | Permitted For |
|---|---|---|
| PIM Eligible | User holds the right to activate but is not currently active in the role | All Tier 0, Tier 1, Tier 2 roles |
| PIM Active (time-limited) | Active role assignment with a defined expiration set by an approver | Tier 3 roles where frequent short activations create friction |
| Permanent Active | Always-active role assignment with no expiration | Break-glass accounts only |
| Permanent Eligible | Permanent right to activate on demand | Standard configuration for all non-break-glass privileged users |

### 4.3 Activation Policy by Role Tier

| Tier | Max Activation Duration | MFA Required | Justification Required | Approval Required | Approver |
|---|---|---|---|---|---|
| Tier 0 | 1 hour | Yes | Yes | Yes | Security Lead |
| Tier 1 | 2 hours | Yes | Yes | Yes | Identity Administrator or Security Lead |
| Tier 2 | 4 hours | Yes | Yes | Recommended | Identity Administrator |
| Tier 3 | 8 hours | Yes | Yes | No | Self-activate with justification |

Activation duration extensions are not permitted. Users must deactivate and re-activate with a new justification if additional time is required.

### 4.4 Approval Workflows

**Tier 0 approval workflow:**
1. User submits PIM activation request with business justification
2. PIM sends email notification to Security Lead
3. Security Lead must approve or deny within 30 minutes
4. If no approver responds within 30 minutes, the request is automatically denied
5. Approved activation begins immediately with email confirmation to the requesting user

**Tier 1 approval workflow:**
1. User submits PIM activation request with business justification
2. PIM sends notification to Identity Administrator
3. Identity Administrator must approve or deny within 1 hour
4. If no approver responds within 1 hour, the request is automatically denied

### 4.5 Notification Requirements

| Event | Notification Recipient | Review SLA |
|---|---|---|
| Tier 0 activation (any) | Security Lead | Within 1 hour |
| Tier 0 activation outside business hours | Security Lead and Identity Admin | Immediate investigation |
| Tier 1 activation outside business hours | Identity Administrator | Within 2 hours |
| Tier 0 eligibility added or removed | Security Lead | Within 24 hours |
| PIM policy modification | Security Lead | Within 1 hour |

---

## 5. Just-In-Time Access Procedures

### 5.1 Activation Request Process

Users with PIM eligible assignments must follow this process for every activation:

1. Navigate to the Microsoft Entra ID PIM portal
2. Select "My roles" and identify the eligible role to activate
3. Click "Activate" on the target role
4. Complete the MFA challenge (Microsoft Authenticator or FIDO2 required for Tier 0 and Tier 1)
5. Enter a specific justification in the justification field (see Section 5.2)
6. Set the activation duration to the minimum required
7. Submit the activation request
8. For Tier 0 and Tier 1 roles, wait for approver confirmation before proceeding
9. Perform only the specific administrative task the activation was requested for
10. Deactivate the role immediately upon task completion

### 5.2 Activation Justification Requirements

Vague justifications are not acceptable:

| Not Acceptable | Acceptable |
|---|---|
| "Admin tasks" | "Resetting MFA for user -- helpdesk ticket 4521" |
| "Maintenance" | "Applying CA policy update approved in Change Record CR-2026-031" |
| "Routine work" | "Reviewing PIM activation audit log for March weekly review" |
| "Need access" | "Adding new user group to Teams admin center -- provisioning request PR-2026-108" |

### 5.3 Activation Duration Guidance

| Task Type | Expected Duration |
|---|---|
| Single user account operation | 15 to 30 minutes |
| Bulk user operations | 30 to 60 minutes |
| Conditional Access policy modification | 30 to 60 minutes |
| Incident response requiring sustained privileged access | Full tier maximum; re-activate if needed |

### 5.4 Emergency Activation

If the PIM approval workflow cannot be completed during an active security incident:

1. The requesting user contacts the Security Lead via out-of-band channel (phone)
2. The Security Lead verbally authorizes the activation
3. The requesting user activates via PIM with justification noting the incident ticket number and verbal authorization
4. The Security Lead approves the PIM request immediately
5. All emergency activations must be documented in the incident record

---

## 6. Break-Glass Emergency Access

### 6.1 Account Configuration

| Requirement | Specification |
|---|---|
| Number of accounts | Exactly 2 |
| Role assignment | Global Administrator -- Permanent Active (exempt from PIM JIT) |
| Account type | Cloud-only (not synchronized from on-premises AD) |
| MFA method | FIDO2 hardware security key only |
| Password | Long random passphrase, minimum 20 characters, randomly generated |

### 6.2 Credential Management

**6.2.1** Break-glass account credentials must be stored in two separate physical locations protected by physical access controls. Digital storage of break-glass credentials is not permitted.

**6.2.2** The two break-glass accounts must be stored in different physical locations so that a single physical incident cannot disable both.

**6.2.3** Verification schedule:

| Test Type | Frequency |
|---|---|
| Credential access verification | Quarterly |
| Sign-in test (non-production or isolated session) | Semi-annually |
| Full activation test in production (Security Lead present) | Annually |

**6.2.4** If either break-glass account is used in production outside of a scheduled test, credentials must be rotated within 24 hours.

### 6.3 Monitoring and Alerting

An alert rule must generate an immediate notification to the Security Lead whenever either break-glass account signs in to the production tenant. The alert must fire within 5 minutes.

```kql
SigninLogs
| where UserPrincipalName in (
    "bg-account01@tenant.domain",
    "bg-account02@tenant.domain"
  )
| where ResultType == "0"
| project TimeGenerated, UserPrincipalName, IPAddress,
          Location, AppDisplayName
```

**Assessment baseline (real finding):** No break-glass monitoring alert existed at time of assessment. This query must be configured as a scheduled alert rule in Microsoft Sentinel.

### 6.4 Usage and Post-Use Review

Break-glass accounts must only be used when the normal PIM activation process is unavailable, the Security Lead has been notified before use, and the task cannot be deferred.

Every production use must result in a post-use review completed within 24 hours documenting: the date and time of access, the specific task performed, why normal privileged access was unavailable, all actions taken during the session (from AuditLogs), and credential rotation confirmation. Post-use review documents must be retained for a minimum of 2 years.

---

## 7. Privileged Account Authentication

### 7.1 MFA Requirements

| Requirement | Standard User | Tier 2 and Tier 3 Privileged | Tier 0 and Tier 1 Privileged |
|---|---|---|---|
| MFA required | Yes | Yes | Yes -- no exceptions |
| Acceptable MFA methods | Authenticator app, FIDO2, TOTP, SMS (fallback) | Authenticator app, FIDO2, TOTP | Microsoft Authenticator (number matching) or FIDO2 only |
| SMS permitted | Yes (fallback only) | No | No |
| MFA method change requires | Self-service | Identity Administrator approval | Security Lead approval |
| CA trusted location bypass | Permitted | Not permitted | Not permitted |

**Driving Evidence (real finding):** At time of the gap assessment, zero Conditional Access policies were enforced. The CA portal returned HTTP 401, indicating no policies existed. 83,600 of 203,906 sign-in events completed on single-factor authentication.

### 7.2 Sign-In Risk Controls and Session Controls

**7.2.1** Conditional Access must be configured to block sign-in for Tier 0 and Tier 1 accounts when Identity Protection detects a high sign-in risk score.

**7.2.2** The following anomaly indicators must trigger escalation to the Security Lead within 1 hour for any Tier 0 or Tier 1 account:
- Sign-in from a country or region where the organization has no administrative presence
- Sign-in from a device not previously seen for that account
- Sign-in outside normal working hours (06:00 to 22:00 local)
- Impossible travel indicators

**7.2.3** Sign-in frequency for privileged accounts must be set to 1 hour in Conditional Access. Persistent browser sessions must be disabled. Users must sign out of all administrative portals immediately upon completion of administrative tasks.

---

## 8. Privileged Access Workstations

### 8.1 Applicability

**Tier 0 operations:** Dedicated Privileged Access Workstation (PAW) is required.
**Tier 1 operations:** PAW strongly recommended. Standard hardened endpoint permitted until dedicated PAW hardware is available.
**Tier 2 and Tier 3 operations:** Standard hardened endpoint is sufficient.

### 8.2 Endpoint Hardening Requirements

| Control | Requirement |
|---|---|
| Endpoint protection | Microsoft Defender for Endpoint enrolled and active |
| OS version | Current supported release with all security patches applied |
| Local administrator | No local administrator accounts other than built-in (disabled or renamed) |
| Browser | Enterprise configuration; no personal browser profiles |
| Screen lock | Activates after 5 minutes of inactivity |
| Encryption | BitLocker or equivalent full-disk encryption enabled |
| Audit logging | Forwarded to Log Analytics Workspace |

**Driving Evidence (real finding):** ProtectionStatus table in Log Analytics Workspace returned no data at time of assessment, indicating endpoint protection posture was unknown across all 126 assets. PAW requirement for Tier 0 becomes effective upon endpoint protection visibility being confirmed (POA-004).

---

## 9. Service Principal and Workload Identity Standards

### 9.1 Provisioning and Permission Scoping

**9.1.1** All service principals and managed identities must be provisioned through a formal request process. No service principal may be created without a documented business purpose.

**9.1.2** Managed identities must be used in preference to service principals with client secrets wherever Azure services support managed identity authentication.

**9.1.3** The following role assignments are prohibited for service principals unless explicitly approved by the Security Lead:
- Global Administrator, Privileged Role Administrator, User Administrator, Security Administrator
- Owner at the subscription level

### 9.2 Credential Management

| Credential Type | Maximum Validity | Rotation Trigger |
|---|---|---|
| Client Secret (production) | 12 months | 30 days before expiry |
| Client Secret (development and test) | 3 months | 30 days before expiry |
| Certificate (production) | 24 months | 60 days before expiry |
| Managed Identity | No credential to rotate | N/A |

Client secrets and certificate private keys must never be stored in source code repositories, configuration files in version control, or any communication channel. Secrets must be stored in Azure Key Vault or equivalent.

### 9.3 Inactive Service Principal Detection

```kql
AADServicePrincipalSignInLogs
| where TimeGenerated > ago(90d)
| summarize LastSignIn = max(TimeGenerated)
    by ServicePrincipalName, ServicePrincipalId
| where LastSignIn < ago(90d)
| order by LastSignIn asc
```

Service principals with no sign-in activity in the preceding 90 days must be flagged during the quarterly review and either documented with a reason for inactivity or disabled immediately.

---

## 10. Privileged Access Monitoring

### 10.1 Audit Log Requirements

| Log Table | Data Captured | Retention |
|---|---|---|
| AuditLogs | All Entra ID directory operations including role assignments, policy changes | 90 days online |
| SigninLogs | All sign-in events including result, location, MFA method, CA status | 90 days online |
| AADServicePrincipalSignInLogs | Service principal authentication events | 90 days online |
| SecurityIncident | SIEM-generated incidents | 1 year |

### 10.2 PIM Activation Review (Weekly)

```kql
AuditLogs
| where TimeGenerated > ago(7d)
| where Category == "RoleManagement"
| where OperationName has_any (
    "Add eligible member to role",
    "Add member to role (PIM activation)",
    "Remove member from role (PIM activation completed)",
    "Remove eligible member from role"
  )
| project TimeGenerated, OperationName, InitiatedBy,
          TargetResources, AdditionalDetails, Result
| order by TimeGenerated desc
```

### 10.3 Role Assignment Changes (Daily Review)

```kql
AuditLogs
| where TimeGenerated > ago(24h)
| where Category == "RoleManagement"
| where OperationName has_any (
    "Add member to role",
    "Remove member from role",
    "Add eligible member to role",
    "Remove eligible member from role",
    "Add scoped member to role",
    "Add owner to role"
  )
| project TimeGenerated, OperationName, InitiatedBy,
          TargetResources, Result, CorrelationId
| order by TimeGenerated desc
```

**Assessment baseline (real finding):** At time of the gap assessment, role assignment operations existed in AuditLogs with no corresponding change management records. Any role assignment change that cannot be correlated to a documented change record must be treated as a potential unauthorized change and escalated within 4 hours.

### 10.4 MFA Compliance for Privileged Accounts

```kql
SigninLogs
| where UserPrincipalName startswith "adm-"
| where TimeGenerated > ago(7d)
| summarize TotalSignIns = count(),
            MFACompliant = countif(AuthenticationRequirement == "multiFactorAuthentication"),
            SFAEvents    = countif(AuthenticationRequirement == "singleFactorAuthentication")
    by UserPrincipalName
| extend MFARate = round(100.0 * MFACompliant / TotalSignIns, 1)
| where SFAEvents > 0
| order by SFAEvents desc
```

Any result returned by this query is a critical policy violation requiring immediate investigation.

---

## 11. Privileged Access Review and Recertification

### 11.1 Quarterly Privileged Access Review

| Review Item | Reviewer | Output |
|---|---|---|
| All Tier 0 and Tier 1 role assignments | Security Lead | Written attestation or revocation order |
| All Tier 2 and Tier 3 role assignments | Identity Administrator | Written attestation or revocation order |
| All PIM eligible assignments | Identity Administrator | Confirm each eligibility remains justified |
| All service principal assignments | Cloud Administrator | Written attestation or revocation order |
| Active exceptions to this standard | GRC Analyst | Renew, resolve, or escalate |

Any assignment not attested within 10 business days is automatically revoked without further notice.

### 11.2 Annual Privileged Access Audit

The annual audit must include:
- Verification that PIM is configured per Section 4 specifications
- Verification that break-glass accounts meet Section 6 requirements
- Verification that no permanent active Tier 0 or Tier 1 assignments exist outside of break-glass accounts
- Test activation of a PIM eligible assignment to confirm the approval workflow functions correctly
- Review of the preceding 12 months of PIM activation logs for anomalies not previously documented

---

## 12. Separation of Duties Matrix

| Operation A | Operation B | Reason | Control |
|---|---|---|---|
| Create a user account | Assign a privileged role to that account | Prevents unauthorized privilege grant | Separate provisioning and role assignment steps; role assignment requires separate approval |
| Approve a PIM activation request | Activate the same role for yourself | Conflict of interest | PIM technically prevents self-approval |
| Modify a Conditional Access policy | Approve the change for production deployment | Unauthorized security control weakening | Change management requires second reviewer |
| Create an app registration | Grant admin consent to that app's permissions | Unauthorized permission grant | Admin consent requires Security Lead approval |
| Add a PIM eligible assignment | Approve that user's first activation | Circular authorization | Activation approval must be performed by a different individual than who granted eligibility |
| Modify audit log settings | Review the audit log for that modification | Evidence tampering risk | Log settings changes must be reviewed by a party other than the changer |

---

## 13. Remediation Roadmap

| Finding | Current State | Requirement | Target Date | POA&M Ref |
|---|---|---|---|---|
| 5 permanent Global Administrators | 5 permanent GA accounts (real data) | Maximum 2; remaining 3 must be downgraded or made PIM eligible | March 20, 2026 | POA-002 |
| PIM not configured | Zero PIM eligible assignments (real data) | PIM must be configured for all Tier 0 and Tier 1 roles | April 5, 2026 | POA-002 |
| Entra ID P2 licensing not confirmed | Licensing status unknown (real data) | P2 licensing required for PIM | April 5, 2026 | POA-002 |
| No approval workflow | No PIM approval workflow (real data) | Tier 0 and Tier 1 require approval workflow per Section 4.4 | April 5, 2026 | POA-002 |
| No break-glass monitoring | No alert on break-glass use (real data) | Alert rule required within 5 minutes of break-glass sign-in | April 5, 2026 | POA-002 |
| 100% blank Department fields | 2,315 accounts with no Dept (real data) | Privileged accounts must have Tier classification in extensionAttribute1 | May 15, 2026 | POA-007 |
| No privileged account naming convention | No adm- prefix in use (real data) | Naming convention enforced for all new privileged accounts; existing accounts within 60 days | April 5, 2026 | POA-002 |
| Endpoint protection unknown | ProtectionStatus table empty (real data) | PAW requirement for Tier 0 becomes effective when POA-004 is resolved | April 5, 2026 | POA-004 |

---

## 14. Related Documents and References

| Document | Status | Relationship |
|---|---|---|
| Access Control Policy | Active (March 5, 2026) | Parent policy -- this standard operationalizes Sections 7.2 and 7.3 |
| Information Security Policy | Active (March 5, 2026) | Governing policy |
| Incident Response Plan | Active (March 5, 2026) | IR Playbooks 8.1 and 8.2 govern privileged account incident response |
| Risk Register | Active (March 5, 2026) | RISK-002 directly addressed by this standard |
| Plan of Action and Milestones | Active | POA-002 is the primary remediation item |
| System Security Plan | Pending | Will document implemented controls from this standard |

**Frameworks Referenced:**
- NIST Special Publication 800-53 Revision 5 (AC-2, AC-3, AC-5, AC-6, IA-2, IA-4, IA-5, IA-8, IA-12)
- NIST Special Publication 800-63B (Digital Identity Guidelines)
- NIST Cybersecurity Framework 2.0 (PR.AA-01 through PR.AA-06)
- ISO/IEC 27001:2022 (Annex A 5.15, 5.16, 5.17, 5.18, 8.2, 8.3, 8.5)
- SOC 2 Trust Services Criteria (CC6.1, CC6.2, CC6.3)
- CIS Controls v8 (Control 5: Account Management, Control 6: Access Control Management)

---

## 15. Approval and Sign-Off

| Role | Name | Signature | Date |
|---|---|---|---|
| Security Lead / Cybersecurity Owner | [To be completed] | [Signature required] | March 5, 2026 |
| Identity Administrator | [To be completed] | [Signature required] | March 5, 2026 |
| GRC Analyst | Alex Truong | [Signature required] | March 5, 2026 |

**Document Effective Date:** March 5, 2026
**Next Review Date:** March 5, 2027
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Parent Policy:** Access Control Policy v1.0

---

*End of Document*

*Organization: ABC Corp | Microsoft Entra ID Tenant. This PAM Standard was produced from a real gap assessment conducted against a live Azure cloud environment. All requirements trace to specific privileged access deficiencies confirmed through live telemetry. All organization names and identifiers have been sanitized. Published for portfolio and educational purposes only.*
