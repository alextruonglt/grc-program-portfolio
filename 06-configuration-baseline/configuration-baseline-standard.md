# Configuration Baseline Standard

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | Security Configuration and Compliance Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> **Disclaimer:** This is a sanitized version of a Configuration Baseline Standard produced following a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This Configuration Baseline Standard was built from evidence gathered during a real, live gap assessment of a Microsoft Azure shared tenant. Every configuration requirement in this document traces to a specific misconfiguration or absent control confirmed through live Entra ID telemetry, Sentinel Log Analytics Workspace queries, and Azure resource review. This is not a generic hardening checklist. It is a targeted baseline built from what was actually broken.

**Driving Findings (real data from a live environment):**
- Conditional Access portal returned HTTP 401 at time of assessment. Zero Conditional Access policies were enforced. 83,600 of 203,906 sign-in events completed on single-factor authentication. (real finding)
- ProtectionStatus table in Microsoft Sentinel returned zero rows for all 126 assets. Endpoint protection posture was completely unverifiable. (real finding)
- 100% of 2,315 user accounts had blank Department, Job Title, and Manager fields. (real finding)
- No screen lock, patch management, or system hardening standard existed for any asset class. (real finding)
- No Azure Policy assignments enforced any compliance requirement at the subscription level. (real finding)
- No analytics rules were documented or active in Sentinel at time of assessment. (real finding)

**Assessment Standards Applied:** NIST SP 800-53 Rev 5 | NIST SP 800-63B | NIST CSF 2.0 | ISO/IEC 27001:2022 | CIS Controls v8 | CIS Microsoft Azure Foundations Benchmark | Microsoft Security Baselines

---

## Table of Contents

1. Purpose and Scope
2. Roles and Responsibilities
3. Baseline Governance
4. Microsoft Entra ID Configuration Baseline
   - 4.1 Tenant-Level Security Defaults and Conditional Access
   - 4.2 User Account Configuration Requirements
   - 4.3 Privileged Account Configuration Requirements
   - 4.4 Guest Account Configuration Requirements
   - 4.5 Multi-Factor Authentication Requirements
   - 4.6 Password and Authentication Policy
   - 4.7 Entra ID Diagnostic Settings
5. Azure Subscription and Resource Configuration Baseline
   - 5.1 Subscription-Level Security Controls
   - 5.2 Azure Policy Assignments
   - 5.3 Microsoft Defender for Cloud Configuration
   - 5.4 Network Security Group Requirements
6. Endpoint Configuration Baseline
   - 6.1 Operating System Hardening
   - 6.2 Endpoint Protection Requirements
   - 6.3 Administrative Account Configuration
   - 6.4 Remote Access Configuration
7. Microsoft Sentinel Configuration Baseline
   - 7.1 Workspace Configuration
   - 7.2 Data Connector Requirements
   - 7.3 Analytics Rule Requirements
   - 7.4 Log Retention Requirements
8. Application and Service Principal Configuration Baseline
9. Baseline Verification and Compliance Monitoring
   - 9.1 Verification Cadence
   - 9.2 KQL Compliance Queries
   - 9.3 Compliance Scoring
10. Compliance Mapping
11. Related Documents and References
12. Approval and Sign-Off

---

## 1. Purpose and Scope

### 1.1 Purpose

This Configuration Baseline Standard defines the minimum required security configuration for all systems, services, and accounts within the ABC Corp Azure cloud environment. It establishes specific, measurable, and auditable configuration requirements that must be maintained as the operational security floor. Any configuration that falls below this baseline is a compliance finding requiring remediation within the timeframes defined in Section 9.1.

This standard was produced in direct response to configuration deficiencies confirmed through live telemetry in the March 2026 gap assessment:

- **Conditional Access gap (real finding):** The Conditional Access portal returned HTTP 401. Zero CA policies were enforced. 83,600 of 203,906 sign-in events completed on single-factor authentication. Any account in the tenant could be accessed with a password alone.
- **Endpoint protection gap (real finding):** The ProtectionStatus table in Sentinel returned zero rows for all 126 assets. Endpoint protection was completely unverifiable.
- **Attribute completeness gap (real finding):** 100% of 2,315 user accounts had blank Department, Job Title, and Manager fields.
- **Hardening baseline gap (real finding):** No screen lock, patch, or system hardening standard existed for any asset class.
- **Policy enforcement gap (real finding):** No Azure Policy assignments enforced any compliance requirement at the subscription level.
- **Detection gap (real finding):** No analytics rules were documented or active in Sentinel.

These are confirmed configuration failures in a live production environment. This standard defines what "configured correctly" looks like.

### 1.2 Scope

**In Scope:**
- All Microsoft Entra ID tenant configurations (Conditional Access, authentication policies, diagnostic settings, guest settings)
- All Azure subscription resources including virtual machines, network security groups, and storage accounts
- All endpoints used by administrators and instructors to access production systems
- All Microsoft Sentinel workspace configurations, data connectors, and analytics rules
- All app registrations and service principals in the tenant

**Out of Scope:**
- Fully isolated lab VMs with no production connectivity
- Microsoft Azure platform-managed infrastructure not configurable by the tenant administrator
- Third-party SaaS application configurations not accessible via the Azure tenant

---

## 2. Roles and Responsibilities

| Role | Configuration Baseline Responsibilities |
|---|---|
| Security Lead | Approve this standard and any deviations. Review quarterly compliance reports. Authorize exceptions. Final authority on baseline configuration decisions. |
| Identity Administrator | Implement and maintain all Entra ID configuration requirements. Verify Conditional Access policy compliance. Manage MFA and authentication policy settings. |
| Cloud Administrator | Implement and maintain Azure subscription, Defender for Cloud, Sentinel, and endpoint configuration requirements. Maintain Azure Policy assignments. Manage NSG configurations. |
| Vulnerability Management Lead | Verify endpoint protection configuration compliance. Integrate Tenable findings with configuration gap identification. Manage patch compliance. |
| GRC Analyst | Maintain this standard. Track deviations in the risk register and POA&M. Run quarterly compliance verification using the KQL queries in Section 9.2. Produce quarterly compliance score reports. |

---

## 3. Baseline Governance

### 3.1 Standard Hierarchy

This Configuration Baseline Standard is subordinate to the Information Security Policy and Vulnerability Management Policy. It operationalizes configuration requirements by providing specific, measurable values.

All configuration decisions not covered by this standard must default to the principle of least permissive configuration. When in doubt, the more restrictive configuration is required unless a documented exception is approved.

### 3.2 Baseline Versioning and Change Control

Any change to a baseline requirement must be reviewed by the Security Lead, documented with a rationale, and reflected in an updated version of this document. The baseline version number must be referenced in all compliance reports and exception requests. When Microsoft releases new security features affecting baseline requirements, the GRC Analyst must evaluate whether this standard requires updating within 60 days of the feature release.

### 3.3 Deviation and Exception Process

Any configuration that cannot meet a baseline requirement must be documented as a formal deviation with: the specific requirement that cannot be met, the technical reason, the compensating control in place, the approving authority, and the deviation expiration date (maximum 90 days). Deviations must be tracked in the POA&M.

No deviation may be granted for the Conditional Access MFA enforcement requirements in Section 4.1 or the privileged account authentication requirements in Section 4.3. These are non-negotiable.

---

## 4. Microsoft Entra ID Configuration Baseline

### 4.1 Tenant-Level Security Defaults and Conditional Access

**Driving Evidence (real finding):** At time of the March 2026 assessment, the Conditional Access portal returned HTTP 401. Zero CA policies were enforced. 83,600 of 203,906 sign-in events completed on single-factor authentication. This is the highest-priority configuration gap in the environment.

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| CA-001 | Security Defaults | Disabled (replaced by Conditional Access policies) | Entra ID > Properties > Manage Security Defaults |
| CA-002 | MFA for all users | Enabled via CA policy; no exclusions permitted | CA policy: "Require MFA for all users" -- confirm grant = MFA required |
| CA-003 | MFA for privileged roles | Separate CA policy targeting all privileged directory roles; block if MFA unavailable | CA policy: "Require MFA for privileged roles" |
| CA-004 | Block legacy authentication | CA policy blocking all legacy auth clients (Exchange ActiveSync, IMAP, POP3, SMTP Auth, basic auth) | CA policy: condition = legacy auth clients; grant = block |
| CA-005 | Sign-in risk policy | Block at high risk; require MFA at medium risk | Entra ID Identity Protection > Sign-in risk policy |
| CA-006 | User risk policy | Require password change at high user risk; require MFA at medium risk | Entra ID Identity Protection > User risk policy |
| CA-007 | Session sign-in frequency (privileged accounts) | 1 hour; no persistent browser session | CA policy: privileged roles; session = 1-hour frequency; disable persistent session |
| CA-008 | Trusted locations | Defined and limited to confirmed organizational IP ranges only | CA > Named Locations |
| CA-009 | Block high-risk countries | CA policy blocking countries with no legitimate administrative presence | CA > Named Locations (geographic) |

**Minimum Conditional Access Policy Set Required:**
1. Require MFA for all users (all cloud apps; all users; grant = MFA required)
2. Require MFA for privileged directory roles (all cloud apps; directory role members; grant = MFA; block if MFA unavailable)
3. Block legacy authentication (all cloud apps; all users; client apps = legacy; grant = block)
4. Sign-in risk-based MFA and block (all cloud apps; all users; medium = MFA; high = block)
5. Privileged account session controls (all cloud apps; privileged roles; session = 1-hour, no persistent session)

### 4.2 User Account Configuration Requirements

**Driving Evidence (real finding):** At time of assessment, 100% of 2,315 user accounts had blank Department, Job Title, and Manager fields. UA-001, UA-002, and UA-003 are direct remediations of this confirmed gap.

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| UA-001 | Department attribute | Populated for all accounts; never blank | Entra ID export; Department must not be null |
| UA-002 | Job Title attribute | Populated for all accounts; never blank | Entra ID export; JobTitle must not be null |
| UA-003 | Manager attribute | Populated for all accounts; never blank (except top of reporting chain) | Entra ID export; Manager must not be null |
| UA-004 | Usage Location | Set for all accounts; required for license assignment | Entra ID export; UsageLocation must not be null |
| UA-005 | Account naming convention | Follows standard naming per ILM Procedure; adm- prefix accounts must not exist in standard user pool | Manual spot-check |
| UA-006 | Guest accounts must have a named sponsor | ExtensionAttribute documenting the internal sponsor UPN | Entra ID export; filter UserType = Guest; review extensionAttribute |
| UA-007 | Guest account expiration | Maximum 90 days initial; renewable only after sponsor attestation | Entra ID Users; filter UserType = Guest; review ExternalUserStateChangeDateTime |
| UA-008 | Inactive accounts | No account with zero sign-in in 30-plus days without documented justification | SigninLogs KQL query (see Section 9.2) |

### 4.3 Privileged Account Configuration Requirements

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| PA-001 | Maximum Global Administrator count | 2 (including break-glass accounts) | Entra ID > Roles > Global Administrator > Assignments |
| PA-002 | Privileged accounts must be dedicated | No adm- account used for email, browsing, or productivity apps | AuditLogs; monitor adm- sign-ins to non-admin applications |
| PA-003 | PIM eligible assignments for all Tier 0 and Tier 1 roles | Zero permanent active role assignments for any Tier 0 or Tier 1 role except break-glass accounts | PIM > Eligible assignments; confirm no permanent active non-break-glass |
| PA-004 | PIM activation policy -- Tier 0 | Max activation 1 hour; MFA required; justification required; approval required | PIM > Roles > Global Administrator > Settings |
| PA-005 | PIM activation policy -- Tier 1 | Max activation 2 hours; MFA required; justification required; approval required | PIM > Roles > [Tier 1 roles] > Settings |
| PA-006 | Break-glass account count | Exactly 2; cloud-only; FIDO2 only | Entra ID Users; filter by bg- prefix |
| PA-007 | Break-glass account MFA method | FIDO2 hardware security key only; no phone-based MFA | Entra ID > Users > [bg account] > Authentication Methods |
| PA-008 | Privileged account naming convention | adm-firstname.lastname@tenant.domain | Entra ID Users; filter by role assignment; confirm adm- prefix |
| PA-009 | extensionAttribute1 for privileged accounts | Tier0, Tier1, Tier2, or Tier3 | Entra ID export; filter privileged role holders; check extensionAttribute1 |
| PA-010 | extensionAttribute2 for privileged accounts | Linked standard user UPN | Entra ID export; filter privileged role holders; check extensionAttribute2 |

### 4.4 Guest Account Configuration Requirements

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| GA-001 | Guest invite permissions | Restricted to administrators only; end users cannot invite guests | Entra ID > External Identities > External Collaboration Settings > Guest invite restrictions = Admins only |
| GA-002 | Guest user access level | Limited access; guests cannot enumerate directory objects | External Collaboration Settings > Guest user access = Most restricted |
| GA-003 | B2B collaboration domains | Allow-list or block-list configured; not open to all domains | External Identities > Cross-tenant access |
| GA-004 | Guest access review | Quarterly access review required per ILM Procedure | Entra ID Access Reviews |

### 4.5 Multi-Factor Authentication Requirements

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| MF-001 | MFA before account enable | Account Enabled = False until MFA registration confirmed | Provisioning process documentation; Authentication Methods blade |
| MF-002 | MFA methods -- standard users | Microsoft Authenticator (push), FIDO2, or TOTP; SMS as last resort only | Entra ID > Users > Authentication Methods |
| MF-003 | MFA methods -- privileged accounts | Microsoft Authenticator (number matching) or FIDO2 only; SMS and voice prohibited | Entra ID > [adm- accounts] > Authentication Methods |
| MF-004 | MFA methods -- break-glass accounts | FIDO2 hardware security key only | Entra ID > [bg- accounts] > Authentication Methods |
| MF-005 | Phishing-resistant MFA | Required for all Tier 0 and Tier 1 accounts (FIDO2 or Windows Hello for Business) | CA policy requiring authentication strength |
| MF-006 | MFA registration campaign | Enabled for any user without a registered MFA method | Entra ID > Protection > Authentication Methods > Registration campaign |
| MF-007 | Authenticator number matching | Enabled for all users using the Authenticator app | Authentication Methods > Microsoft Authenticator > Number matching |

### 4.6 Password and Authentication Policy

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| PW-001 | Password expiration | Disabled; rely on breach detection via Identity Protection | Entra ID > Users > Password expiration policy |
| PW-002 | Password protection | Microsoft Entra Password Protection enabled; banned password list enforced | Entra ID > Protection > Authentication Methods > Password Protection |
| PW-003 | Smart lockout threshold | Maximum 5 failed attempts before lockout | Password Protection > Lockout threshold |
| PW-004 | Smart lockout duration | Minimum 60 seconds; increasing with repeated failures | Password Protection > Lockout duration |
| PW-005 | Self-Service Password Reset | Enabled for standard users; requires 2 authentication methods | Entra ID > Protection > Password Reset |
| PW-006 | SSPR authentication methods | 2 methods required; acceptable: Authenticator app, phone call, alternate email | Entra ID > Password Reset > Authentication Methods |
| PW-007 | Temporary Access Pass | Enabled for MFA registration and account recovery | Entra ID > Protection > Authentication Methods > Temporary Access Pass |

### 4.7 Entra ID Diagnostic Settings

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| DS-001 | Audit log streaming | AuditLogs category enabled and streaming to Log Analytics Workspace | Entra ID > Diagnostic Settings |
| DS-002 | Sign-in log streaming | SignInLogs and NonInteractiveUserSignInLogs enabled and streaming to LAW | Entra ID > Diagnostic Settings |
| DS-003 | Service principal sign-in streaming | AADServicePrincipalSignInLogs category enabled | Entra ID > Diagnostic Settings |
| DS-004 | Risky user streaming | RiskyUsers category enabled | Entra ID > Diagnostic Settings |
| DS-005 | Log retention | Minimum 90 days hot retention in LAW; long-term export to storage for 1-year archive | LAW > Usage and estimated costs > Data retention |

---

## 5. Azure Subscription and Resource Configuration Baseline

### 5.1 Subscription-Level Security Controls

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| AZ-001 | Microsoft Defender for Cloud | Enabled for all applicable resource types (Servers, Storage, Key Vault, App Service) | Defender for Cloud > Environment settings > Defender plans |
| AZ-002 | Security score baseline | Establish current score; target 20-point improvement within 90 days | Defender for Cloud > Security posture > Secure score |
| AZ-003 | Subscription Owner count | Maximum 3 named individuals; no service principals as subscription owners | Azure portal > Subscriptions > IAM > Owner role assignments |
| AZ-004 | Azure Activity Log | Minimum 90 days retention; diagnostic setting streaming to LAW | Subscriptions > Activity log > Diagnostic settings |
| AZ-005 | Resource locks | Production resource groups must have Delete locks applied | Azure portal > Resource Groups > Locks |
| AZ-006 | Azure RBAC scope | Role assignments at resource group or resource scope; avoid broad subscription-level assignments | Subscriptions > Access control (IAM) > Role assignments |

### 5.2 Azure Policy Assignments

**Driving Evidence (real finding):** No Azure Policy assignments were active at the subscription level at time of the March 2026 assessment. The following policies must be assigned:

| Policy | Effect | Rationale |
|---|---|---|
| Require a tag on resources | Deny | Enforces resource ownership tagging required for asset inventory accuracy |
| Audit VMs that do not use managed disks | Audit | Identifies unmanaged disk usage |
| Allowed locations | Deny | Restricts resource provisioning to approved Azure regions |
| MFA should be enabled on accounts with Owner permissions | Audit | Validates MFA enforcement at resource level |
| MFA should be enabled on accounts with Write permissions | Audit | Validates MFA enforcement for write-access accounts |
| Microsoft Defender for Cloud should be enabled | Audit | Confirms Defender for Cloud is active |
| Endpoint protection solution should be installed on VM scale sets | Audit | Confirms endpoint protection deployment |
| Log Analytics agent should be installed on virtual machines | Audit | Confirms agent-based log collection |

### 5.3 Microsoft Defender for Cloud Configuration

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| DC-001 | Defender for Servers | Plan 2 enabled for all Azure VMs | Defender for Cloud > Environment settings > Servers |
| DC-002 | Defender for Key Vault | Enabled | Defender for Cloud > Environment settings > Key Vault |
| DC-003 | Defender for Storage | Enabled | Defender for Cloud > Environment settings > Storage |
| DC-004 | Auto-provisioning for MDE agent | Enabled; Microsoft Defender for Endpoint extension auto-deployed to all supported VMs | Defender for Cloud > Environment settings > Auto-provisioning |
| DC-005 | Email notifications | Security Lead email configured for High and Critical alerts | Defender for Cloud > Environment settings > Email notifications |
| DC-006 | Security score review | Reviewed monthly; recommendations reviewed and prioritized | Defender for Cloud > Security posture |
| DC-007 | Regulatory compliance dashboard | Enabled for NIST SP 800-53 or equivalent | Defender for Cloud > Regulatory compliance |

### 5.4 Network Security Group Requirements

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| NS-001 | RDP (port 3389) internet exposure | Blocked at NSG for all assets except jump servers; jump servers restricted to specific source IPs | NSG inbound rules; check for 0.0.0.0/0 source on port 3389 |
| NS-002 | SSH (port 22) internet exposure | Blocked at NSG for all assets except designated management endpoints | NSG inbound rules; check for 0.0.0.0/0 source on port 22 |
| NS-003 | Management port exposure | No unrestricted management ports (3389, 22, 5985, 5986) exposed to internet | Defender for Cloud > Just-in-time VM access recommendations |
| NS-004 | Just-in-time VM access | Enabled for all Tier 1 and Tier 2 virtual machines | Defender for Cloud > Workload protections > Just-in-time VM access |
| NS-005 | NSG flow logs | Enabled and streaming to Log Analytics Workspace | NSG > Diagnostic settings > Flow logs |
| NS-006 | Default deny rule | All NSGs must have a default deny-all inbound rule lower in priority than all allow rules | NSG inbound rules; confirm deny rule exists |

---

## 6. Endpoint Configuration Baseline

### 6.1 Operating System Hardening

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| OS-001 | Operating system patch level | All security patches applied within VMP SLA (Critical: 7-15 days; High: 15-30 days) | Tenable scan results; Azure Update Manager compliance report |
| OS-002 | Supported operating systems | All VMs running a vendor-supported OS version with active security patch support | Tenable EOL OS detection |
| OS-003 | Screen lock timeout | Activates after 5 minutes of inactivity | Group Policy or Intune compliance policy |
| OS-004 | Full-disk encryption | BitLocker (Windows) or equivalent (Linux) enabled on all admin and instructor endpoints | Azure Disk Encryption status; Intune compliance report |
| OS-005 | Windows Firewall | Enabled on all profiles (Domain, Private, Public); default inbound: deny | Group Policy or Intune compliance policy |
| OS-006 | BIOS/UEFI password | Set on all administrator endpoints | Physical verification or Intune firmware configuration |
| OS-007 | Secure boot | Enabled on all supported endpoints | Intune compliance report |
| OS-008 | Audit logging | Windows Security Event Log enabled; forwarded to Log Analytics Workspace | Diagnostic settings; LAW table SecurityEvent |

### 6.2 Endpoint Protection Requirements

**Driving Evidence (real finding):** The ProtectionStatus table in Sentinel returned zero data for all 126 assets at time of the March 2026 assessment. The following requirements cannot currently be verified as implemented. Verification is blocked pending POA-004 remediation.

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| EP-001 | Endpoint protection agent | Microsoft Defender for Endpoint deployed and active on all in-scope assets | ProtectionStatus table query; MDE portal device inventory |
| EP-002 | Real-time protection | Enabled on all assets; tamper protection enabled | ProtectionStatus > ProtectionState = Protected |
| EP-003 | Threat definition currency | Updated within 24 hours of new definition release | ProtectionStatus > SignatureVersion vs. current version |
| EP-004 | Reporting to Sentinel | ProtectionStatus data received in LAW within 24 hours | ProtectionStatus table query (must return rows for all assets) |
| EP-005 | Behavior monitoring | Enabled (Microsoft Defender real-time behavior monitoring) | MDE portal > Device configuration |
| EP-006 | Cloud-delivered protection | Enabled (Microsoft Defender cloud-delivered protection) | MDE portal > Device configuration |
| EP-007 | Controlled folder access | Enabled for administrator and instructor endpoints | Intune or Group Policy; MDE Security Center |

### 6.3 Administrative Account Configuration

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| AC-001 | Local administrator accounts | No enabled local administrator accounts other than the built-in account; built-in must be renamed or disabled | Local Security Policy or Intune |
| AC-002 | Local Administrator Password Solution | LAPS enabled and rotating local admin passwords | Azure LAPS configuration; Intune enrollment |
| AC-003 | User Account Control | Enabled at highest setting (Prompt for credentials for all admin actions) | Group Policy or Intune |
| AC-004 | No shared administrative accounts | All administrative actions attributed to a named individual account | Account audit; no shared credentials permitted |

### 6.4 Remote Access Configuration

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| RA-001 | RDP access | Restricted to jump servers or VPN only; no direct internet RDP | NSG rules; Defender for Cloud |
| RA-002 | NLA for RDP | Network Level Authentication required for all RDP connections | Group Policy or Intune |
| RA-003 | VPN MFA | MFA required for all VPN connections used for remote management | VPN gateway configuration |
| RA-004 | Session timeout | Remote sessions must terminate after 15 minutes of inactivity | Group Policy: Computer Configuration > Administrative Templates > Windows Components > Remote Desktop Services |

---

## 7. Microsoft Sentinel Configuration Baseline

### 7.1 Workspace Configuration

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| SW-001 | Log Analytics Workspace | Single LAW connected to the Azure subscription | Azure portal > Log Analytics Workspaces |
| SW-002 | Workspace retention | Minimum 90 days hot retention; archive tier configured for 1-year total retention | LAW > Usage and estimated costs > Data retention |
| SW-003 | Sentinel enabled | Microsoft Sentinel enabled on the primary LAW | Sentinel > Overview |
| SW-004 | Daily data cap | Configured to prevent unexpected cost overruns | LAW > Usage and estimated costs > Daily cap |

### 7.2 Data Connector Requirements

**Driving Evidence (real finding):** The ProtectionStatus table returned zero rows at time of assessment, indicating the Microsoft Defender for Endpoint connector was not delivering data. This directly violates DC-004 and EP-004.

| Connector | Required Status | Primary Tables Populated | Verification |
|---|---|---|---|
| Microsoft Entra ID | Connected; last data received within 24 hours | AuditLogs, SignInLogs, AADNonInteractiveUserSignInLogs | Data connectors blade; query each table |
| Microsoft Defender for Cloud | Connected; last data received within 24 hours | SecurityAlert, SecurityIncident | Data connectors blade |
| Microsoft Defender for Endpoint | Connected; last data received within 24 hours | DeviceEvents, ProtectionStatus | Data connectors blade |
| Azure Activity | Connected; last data received within 24 hours | AzureActivity | Data connectors blade |
| Microsoft 365 Defender | Connected if M365 E3/E5 licensed | Various M365 tables | Data connectors blade |

### 7.3 Analytics Rule Requirements

**Driving Evidence (real finding):** No analytics rules were documented or active in Sentinel at the time of assessment. All rules in this table represent new requirements.

| Rule Category | Minimum Active Rules | Priority |
|---|---|---|
| Privileged account sign-in anomalies | 1 | Critical |
| Break-glass account usage | 1 | Critical |
| Impossible travel detection | 1 | High |
| MFA bypass or downgrade detection | 1 | Critical |
| Bulk user account changes (more than 10 accounts in 1 hour) | 1 | High |
| Role assignment changes outside of PIM | 1 | Critical |
| Failed sign-in threshold (5 or more failures for same account) | 1 | High |
| Legacy authentication sign-in detection | 1 | High |
| ProtectionStatus non-reporting assets | 1 | High |
| Critical vulnerability alert (Tenable integration when available) | 1 | High |

### 7.4 Log Retention Requirements

| Log Type | Hot Retention | Archive Retention | Rationale |
|---|---|---|---|
| AuditLogs | 90 days | 1 year | Identity lifecycle and change audit trail |
| SignInLogs | 90 days | 1 year | Authentication anomaly investigation |
| SecurityAlert | 90 days | 1 year | Incident investigation |
| SecurityIncident | 90 days | 2 years | Compliance evidence |
| ProtectionStatus | 90 days | 1 year | Endpoint protection compliance evidence |
| AzureActivity | 90 days | 1 year | Subscription-level change audit trail |

---

## 8. Application and Service Principal Configuration Baseline

### 8.1 Application Registration Standards

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| AP-001 | Application ownership | Every app registration must have at least 1 named owner | Entra ID > App registrations > [each app] > Owners |
| AP-002 | Client secret expiration | Maximum 12 months for production; maximum 3 months for development/test | Entra ID > App registrations > Certificates and secrets |
| AP-003 | No expired secrets | Zero app registrations with expired client secrets | Entra ID export; filter by expiration date |
| AP-004 | Certificate preferred over secret | Certificates preferred over client secrets for production apps | Entra ID > App registrations > Certificates and secrets |
| AP-005 | Secret storage | All client secrets stored in Azure Key Vault immediately upon creation; no secrets in code or config files | Key Vault secrets inventory; code review |

### 8.2 Service Principal Permission Standards

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| SP-001 | No service principal as Global Administrator | Zero service principals with Global Administrator role | Entra ID > Roles > Global Administrator; filter Type = ServicePrincipal |
| SP-002 | No service principal as subscription Owner | Zero service principals with Azure subscription Owner role | Azure IAM > Role assignments; filter by service principal type |
| SP-003 | Least privilege Graph API permissions | Application permissions scoped to minimum required | Entra ID > Enterprise Apps > [app] > Permissions |
| SP-004 | Service principal inventory | Complete inventory maintained; all service principals have a named owner | Service account inventory document |
| SP-005 | Inactive service principal detection | Service principals with no sign-in in 90 days reviewed quarterly | KQL query against AADServicePrincipalSignInLogs |

### 8.3 OAuth Consent Configuration

| Req ID | Requirement | Required Value | Verification Method |
|---|---|---|---|
| OA-001 | User consent for apps | Disabled; admin consent required for all app permission grants | Entra ID > Enterprise apps > Consent and permissions > User consent settings |
| OA-002 | Admin consent workflow | Enabled; users can request consent which routes to admin approval | Entra ID > Enterprise apps > Admin consent workflow settings |
| OA-003 | Publisher verification | Prefer verified publishers; unverified publisher apps require Security Lead approval | Entra ID > App registrations > filter by publisher verification status |

---

## 9. Baseline Verification and Compliance Monitoring

### 9.1 Verification Cadence

| Baseline Area | Verification Cadence | Responsible Role |
|---|---|---|
| Conditional Access policies (Section 4.1) | Monthly | Identity Administrator |
| User account attribute compliance (Section 4.2) | Monthly | Identity Administrator |
| Privileged account configuration (Section 4.3) | Quarterly | Identity Administrator |
| Endpoint protection coverage (Section 6.2) | Weekly until POA-004 closed; then monthly | Vulnerability Management Lead |
| Sentinel data connector status (Section 7.2) | Weekly | Cloud Administrator |
| Azure Policy compliance (Section 5.2) | Monthly | Cloud Administrator |
| App registration and service principal compliance (Section 8) | Quarterly | Cloud Administrator |
| Full baseline verification | Annual | GRC Analyst |

### 9.2 KQL Compliance Queries

**Conditional Access -- SFA sign-ins that should have triggered MFA:**

```kql
SigninLogs
| where TimeGenerated > ago(7d)
| where AuthenticationRequirement == "singleFactorAuthentication"
| where ResultType == "0"
| summarize SFACount = count() by UserPrincipalName, AppDisplayName
| order by SFACount desc
```

**Driving Evidence (real finding):** This query was executed against the live tenant during the March 2026 assessment. It returned 83,600 successful single-factor authentication events out of 203,906 total sign-ins (41% SFA rate). The compliance target is zero rows.

**User accounts with blank mandatory attributes (via AuditLogs for recent creates):**

```kql
AuditLogs
| where OperationName == "Add user"
| where TimeGenerated > ago(90d)
| project TimeGenerated,
          UPN         = tostring(TargetResources[0].userPrincipalName),
          InitiatedBy = tostring(InitiatedBy.user.userPrincipalName)
| order by TimeGenerated desc
```

Pair with Entra ID full user export filtered for blank Department, Job Title, and Manager fields. Compliance target: zero blank mandatory attributes across all accounts.

**Global Administrator count check:**

```kql
AuditLogs
| where OperationName has_any (
    "Add member to role",
    "Add eligible member to role"
  )
| where TargetResources has "Global Administrator"
| where TimeGenerated > ago(90d)
| project TimeGenerated, OperationName, InitiatedBy, TargetResources
| order by TimeGenerated desc
```

Verify current GA count in Entra ID > Roles > Global Administrator. Compliance target: maximum 2 (PA-001).

**ProtectionStatus compliance check:**

```kql
ProtectionStatus
| where TimeGenerated > ago(24h)
| summarize LastReport = max(TimeGenerated),
            State      = any(ProtectionState)
    by Computer
| where State != "Protected"
    or isnull(State)
| order by LastReport asc
```

**Driving Evidence (real finding):** This query returned zero rows at time of assessment because the ProtectionStatus table was entirely empty, not because all assets were compliant. The compliance target post-remediation is zero rows where State is not Protected.

**Legacy authentication sign-in detection:**

```kql
SigninLogs
| where TimeGenerated > ago(7d)
| where ClientAppUsed in (
    "Exchange ActiveSync",
    "IMAP4",
    "POP3",
    "SMTP",
    "Authenticated SMTP",
    "AutoDiscover",
    "Outlook Anywhere (RPC over HTTP)",
    "Other clients"
  )
| summarize LegacyCount = count() by UserPrincipalName, ClientAppUsed
| order by LegacyCount desc
```

Compliance target: zero rows (CA-004 legacy auth block active). Any rows indicate a CA policy gap.

**Service principals with no sign-in in 90 days:**

```kql
AADServicePrincipalSignInLogs
| where TimeGenerated > ago(90d)
| summarize LastSignIn = max(TimeGenerated) by ServicePrincipalName
| where LastSignIn < ago(90d)
| order by LastSignIn asc
```

Service principals returned must be reviewed quarterly per SP-005.

**Sentinel data connector health check:**

```kql
Heartbeat
| where TimeGenerated > ago(24h)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where LastHeartbeat < ago(2h)
| order by LastHeartbeat asc
```

Assets not sending a heartbeat within 2 hours require investigation.

### 9.3 Compliance Scoring

The GRC Analyst must produce a quarterly compliance score report using the following method:

- Each baseline requirement in Sections 4 through 8 counts as one control item
- Each control is marked as Compliant, Non-Compliant, or Deviation (formally documented)
- Compliance score = (Compliant controls / Total controls) x 100

| Score | Status | Required Action |
|---|---|---|
| 90 to 100 | Compliant | No action required beyond maintaining the score |
| 75 to 89 | Needs Improvement | Remediation plan required for all non-compliant items within 30 days |
| 60 to 74 | At Risk | Security Lead notification required; remediation plan within 15 days |
| Below 60 | Critical Non-Compliance | Immediate Security Lead notification; emergency remediation plan within 5 business days |

**Baseline score at time of this standard (March 2026, real data):** Estimated 20 to 30 percent compliant based on assessment findings. The primary gaps driving this score were the CA policy gap, the attribute completeness gap, the endpoint protection telemetry gap, the Sentinel connector gap, and the absence of PIM configuration.

---

## 10. Compliance Mapping

| Baseline Section | NIST SP 800-53 Rev 5 | NIST CSF 2.0 | ISO/IEC 27001:2022 | CIS Controls v8 |
|---|---|---|---|---|
| Conditional Access (4.1) | AC-2, AC-17, IA-2, IA-3 | PR.AA-01, PR.AA-05 | A.8.5, A.8.20 | Control 6 |
| User Account Configuration (4.2) | AC-2, IA-4, IA-5 | PR.AA-01, PR.AA-02 | A.5.16, A.5.18 | Control 5 |
| Privileged Account Configuration (4.3) | AC-2(6), AC-6, IA-2(1) | PR.AA-05, PR.AA-06 | A.5.15, A.8.2 | Controls 5, 6 |
| MFA Requirements (4.5) | IA-2, IA-2(1), IA-2(2) | PR.AA-01 | A.8.5 | Control 6 |
| Password Policy (4.6) | IA-5, IA-5(1) | PR.AA-01 | A.8.5 | Control 5 |
| Diagnostic Settings (4.7) | AU-2, AU-3, AU-6, AU-12 | DE.CM-01 | A.8.15, A.8.17 | Control 8 |
| Azure Subscription Controls (5.1) | CM-7, SC-7 | PR.PS-01 | A.8.9 | Controls 4, 12 |
| Azure Policy (5.2) | CM-6, CM-7 | PR.PS-01, PR.PS-02 | A.8.9 | Control 4 |
| Defender for Cloud (5.3) | CA-7, RA-5, SI-3 | DE.CM-01, ID.RA-01 | A.8.8 | Control 7 |
| NSG Configuration (5.4) | SC-7, SC-8 | PR.IR-01 | A.8.20, A.8.22 | Controls 12, 13 |
| OS Hardening (6.1) | CM-6, CM-7, SI-2 | PR.PS-01 | A.8.9, A.8.19 | Control 4 |
| Endpoint Protection (6.2) | SI-3, SI-4 | DE.CM-01 | A.8.7 | Control 10 |
| Sentinel Configuration (7) | AU-2, AU-9, CA-7, SI-4 | DE.CM-01, DE.CM-02 | A.8.15, A.8.16 | Control 8 |
| App and SP Configuration (8) | AC-2, AC-6, IA-4 | PR.AA-01, PR.AA-05 | A.5.18, A.8.2 | Controls 5, 6 |

---

## 11. Related Documents and References

| Document | Status | Relationship |
|---|---|---|
| Information Security Policy | Active (March 5, 2026) | Parent policy |
| Access Control Policy | Active (March 5, 2026) | Sections 4.1 through 4.3 operationalize ACP requirements |
| PAM Standard | Active (March 5, 2026) | Sections 4.3 and 4.5 are aligned with and subordinate to the PAM Standard |
| Identity Lifecycle Management Procedure | Active (March 5, 2026) | Section 4.2 attribute requirements aligned with ILM Procedure Section 7.1 |
| Vulnerability Management Policy | Active (March 5, 2026) | Section 6.1 patch requirements aligned with VMP SLAs |
| Risk Register | Active (March 5, 2026) | Baseline deviations tracked in the risk register |
| Plan of Action and Milestones | Active | POA-003, POA-004, and POA-006 directly addressed by this standard |
| System Security Plan | Pending | Will document implemented controls from this standard |

**Frameworks Referenced:**
- NIST Special Publication 800-53 Revision 5 (AC-2, AC-6, AC-17, AU-2, AU-3, CA-7, CM-6, CM-7, IA-2, IA-4, IA-5, RA-5, SC-7, SI-2, SI-3, SI-4)
- NIST Special Publication 800-63B (Digital Identity Guidelines)
- NIST Cybersecurity Framework 2.0 (PR.AA, PR.PS, DE.CM, ID.RA)
- ISO/IEC 27001:2022 (Annex A 5.15, 5.16, 5.18, 8.2, 8.5, 8.7, 8.8, 8.9, 8.15, 8.16, 8.17, 8.19, 8.20, 8.22)
- CIS Controls v8 (Controls 4, 5, 6, 7, 8, 10, 12, 13)
- CIS Microsoft Azure Foundations Benchmark
- Microsoft Security Baselines (Windows, Microsoft 365, Azure)
- CISA Known Exploited Vulnerabilities Catalog

---

## 12. Approval and Sign-Off

| Role | Name | Signature | Date |
|---|---|---|---|
| Security Lead / Cybersecurity Owner | [To be completed] | [Signature required] | March 5, 2026 |
| Identity Administrator | [To be completed] | [Signature required] | March 5, 2026 |
| Cloud Administrator | [To be completed] | [Signature required] | March 5, 2026 |
| GRC Analyst | Alex Truong | [Signature required] | March 5, 2026 |

**Standard Effective Date:** March 5, 2026
**Next Review Date:** March 5, 2027
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Parent Documents:** Information Security Policy v1.0 | Vulnerability Management Policy v1.0

---

*End of Document*

*Organization: ABC Corp | Microsoft Azure Cloud Environment. This Configuration Baseline Standard was produced from a real gap assessment conducted against a live Microsoft Azure shared tenant. All configuration requirements trace to specific misconfiguration findings confirmed through live Entra ID telemetry, Sentinel Log Analytics Workspace queries, and Azure resource review. All organization names and identifiers have been sanitized. Published for portfolio and educational purposes only.*
