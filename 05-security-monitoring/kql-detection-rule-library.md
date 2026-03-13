# KQL Detection Rule Library

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | Security Operations and Detection Engineering Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> **Disclaimer:** This is a sanitized version of a KQL Detection Rule Library produced from a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** Every rule in this library was written for a real, live Microsoft Sentinel workspace connected to an Azure shared tenant. The tables referenced (SignInLogs, AuditLogs, ProtectionStatus, SecurityAlert, SecurityEvent, AADServicePrincipalSignInLogs, AzureActivity, Heartbeat) were confirmed present during the live gap assessment. This is not a theoretical detection library. It was built to close a confirmed zero-rule detection state in a production environment.

**Real Findings That Drove This Library:**
- No analytics rules active in Microsoft Sentinel at time of assessment -- the detection layer was completely absent (real finding)
- High and Critical alerts present in SecurityAlert table with no evidence of triage (real finding)
- Unactioned incidents in SecurityIncident table -- never assigned, investigated, or closed (real finding)
- ProtectionStatus table returned 0 rows for all 126 assets -- endpoint telemetry not flowing to Sentinel (real finding)
- 83,600 of 203,906 sign-in events completed on single-factor authentication with no detection rule (real finding)
- Query CP-001 would have returned exactly 83,600 results if run during the assessment window (real finding)
- Query CP-005 would have returned multiple overdue High and Critical alerts -- count was confirmed greater than zero (real finding)

**Standards Applied:** NIST SP 800-53 Rev 5 (AU, SI, IR, CA) | NIST CSF 2.0 (DE.AE, DE.CM) | MITRE ATT&CK Enterprise | CIS Controls v8 Controls 8, 17 | ISO/IEC 27001:2022 Annex A 8.15/8.16

---

## Table of Contents

1. Library Overview and Usage Guide
2. Rule Metadata Standard
3. Category 1: Identity and Authentication (10 rules)
4. Category 2: Endpoint and Infrastructure (5 rules)
5. Category 3: Privilege and Access Escalation (4 rules)
6. Category 4: Application and Service Principal (4 rules)
7. Category 5: Cloud Resource and Configuration (4 rules)
8. Category 6: Monitoring Integrity and Infrastructure (2 rules)
9. Compliance Posture Queries (5 queries, non-alerting)
10. Rule Inventory and Coverage Matrix

---

## 1. Library Overview and Usage Guide

This library contains 29 detection rules and 5 compliance posture queries for Microsoft Sentinel. Each rule includes complete KQL ready for direct Sentinel import, rule metadata, detection objective grounded in real assessment findings, Sentinel configuration recommendations, and analyst response guidance.

**How to implement:** Each rule in Categories 1 through 6 is implemented as a Scheduled Query Rule in Sentinel (Analytics > Create > Scheduled query rule). Copy the KQL into the Rule Query field. Use the Sentinel configuration block for frequency, lookback, and incident creation settings.

**Naming convention:** `[Category] - [Specific Activity] - [Severity]`

**Compliance posture queries** (Category 9) are run manually on a scheduled basis for audit and compliance verification. They are not Sentinel analytics rules.

---

## 2. Rule Metadata Standard

| Field | Description |
|---|---|
| Rule ID | CATEGORY-NNN |
| Rule Name | Full name following naming convention |
| Severity | Critical, High, Medium, or Low |
| MITRE Tactic | Primary MITRE ATT&CK tactic |
| MITRE Technique | Technique ID and name |
| Data Sources | Tables queried |
| Query Frequency | How often Sentinel runs the query |
| Lookback Period | How far back the query looks each run |
| Incident Creation | Yes or No |
| Entity Mapping | Entities for Sentinel correlation |
| Status | Active or Pending Data Source |

---

## 3. Category 1: Identity and Authentication

### Rule ID-001: Break-Glass Account Sign-In

| Field | Value |
|---|---|
| Rule ID | ID-001 |
| Rule Name | Identity - Break-Glass Account Sign-In - Critical |
| Severity | Critical |
| MITRE Tactic | Privilege Escalation, Initial Access |
| MITRE Technique | T1078.004 - Valid Accounts: Cloud Accounts |
| Data Sources | SigninLogs |
| Query Frequency | Every 5 minutes |
| Lookback Period | 5 minutes |
| Incident Creation | Yes |
| Entity Mapping | Account: UserPrincipalName; IP: IPAddress |
| Status | Active |

**Detection Objective:** Any sign-in by a break-glass account (prefixed bg- per PAM Standard) is inherently anomalous. These accounts are for declared emergencies only. Every sign-in, successful or failed, must be immediately investigated.

```kql
SigninLogs
| where TimeGenerated > ago(5m)
| where UserPrincipalName startswith "bg-"
    or UserPrincipalName has "break-glass"
    or UserPrincipalName has "breakglass"
| project
    TimeGenerated,
    UserPrincipalName,
    IPAddress,
    Location,
    ResultType,
    ResultDescription,
    AppDisplayName,
    ClientAppUsed,
    AuthenticationRequirement,
    ConditionalAccessStatus,
    UserAgent
| order by TimeGenerated desc
```

**Analyst Response:**
1. Confirm whether the sign-in was pre-authorized by the Security Lead
2. If not pre-authorized: treat as active compromise. Escalate to Security Lead immediately (Severity 1 incident)
3. Review AuditLogs for all actions performed during the session
4. If unauthorized: disable the account, rotate the FIDO2 key, begin incident response

---

### Rule ID-002: Single-Factor Authentication Success

| Field | Value |
|---|---|
| Rule ID | ID-002 |
| Rule Name | Identity - Single-Factor Authentication Success - Critical |
| Severity | Critical |
| MITRE Tactic | Initial Access |
| MITRE Technique | T1078.004 - Valid Accounts: Cloud Accounts |
| Data Sources | SigninLogs |
| Query Frequency | Every 15 minutes |
| Lookback Period | 15 minutes |
| Incident Creation | Yes |
| Entity Mapping | Account: UserPrincipalName; IP: IPAddress |
| Status | Active |

**Real Environment Finding:** This query was executed on the live environment during the March 2026 gap assessment. It returned 83,600 SFA successes from 203,906 total sign-in events (40.9% SFA rate). Target after Conditional Access deployment: zero results. (real finding)

```kql
SigninLogs
| where TimeGenerated > ago(15m)
| where AuthenticationRequirement == "singleFactorAuthentication"
| where ResultType == "0"
| where UserPrincipalName !startswith "bg-"
| extend
    RiskLevel = RiskLevelDuringSignIn,
    CAResult  = ConditionalAccessStatus
| project
    TimeGenerated,
    UserPrincipalName,
    IPAddress,
    Location,
    AppDisplayName,
    ClientAppUsed,
    AuthenticationRequirement,
    CAResult,
    RiskLevel,
    UserAgent,
    CorrelationId
| order by TimeGenerated desc
```

---

### Rule ID-003: Failed Sign-In Threshold Breach

| Field | Value |
|---|---|
| Rule ID | ID-003 |
| Rule Name | Identity - Failed Sign-In Threshold Breach - High |
| Severity | High |
| MITRE Tactic | Credential Access |
| MITRE Technique | T1110.003 - Brute Force: Password Spraying |
| Data Sources | SigninLogs |
| Query Frequency | Every 15 minutes |
| Lookback Period | 1 hour |
| Incident Creation | Yes |
| Entity Mapping | Account: UserPrincipalName; IP: IPAddress |
| Status | Active |

**Detection Objective:** Detects both single-account brute force (5 or more failures) and password spray patterns (1 failure each against 10 or more accounts from the same IP).

```kql
let FailureThreshold_SingleAccount = 5;
let SprayThreshold_AccountCount = 10;
let Lookback = 1h;
let SingleAccountAttacks =
    SigninLogs
    | where TimeGenerated > ago(Lookback)
    | where ResultType != "0"
    | where ResultType !in ("50074", "50076", "50079", "70044")
    | summarize
        FailureCount   = count(),
        FirstFailure   = min(TimeGenerated),
        LastFailure    = max(TimeGenerated),
        DistinctIPs    = dcount(IPAddress),
        IPList         = make_set(IPAddress, 10),
        AppList        = make_set(AppDisplayName, 5)
        by UserPrincipalName
    | where FailureCount >= FailureThreshold_SingleAccount
    | extend AttackPattern = "SingleAccountBruteForce";
let SprayAttacks =
    SigninLogs
    | where TimeGenerated > ago(Lookback)
    | where ResultType != "0"
    | where ResultType !in ("50074", "50076", "50079", "70044")
    | summarize
        AccountCount   = dcount(UserPrincipalName),
        FailureCount   = count(),
        AccountList    = make_set(UserPrincipalName, 20),
        AppList        = make_set(AppDisplayName, 5)
        by IPAddress
    | where AccountCount >= SprayThreshold_AccountCount
    | extend AttackPattern = "PasswordSpray";
SingleAccountAttacks
| project TimeGenerated = LastFailure, UserPrincipalName,
          FailureCount, DistinctIPs, IPList, AppList, AttackPattern
| union (SprayAttacks
    | project TimeGenerated = now(), UserPrincipalName = "Multiple (spray)",
              FailureCount, DistinctIPs = AccountCount,
              IPList = pack_array(IPAddress), AppList, AttackPattern)
```

---

### Rule ID-004: Impossible Travel Detection

| Field | Value |
|---|---|
| Rule ID | ID-004 |
| Rule Name | Identity - Impossible Travel Detection - High |
| Severity | High |
| MITRE Tactic | Initial Access |
| MITRE Technique | T1078 - Valid Accounts |
| Data Sources | SigninLogs |
| Query Frequency | Every 30 minutes |
| Lookback Period | 2 hours |
| Incident Creation | Yes |
| Status | Active |

```kql
let TravelWindowMinutes = 60;
SigninLogs
| where TimeGenerated > ago(2h)
| where ResultType == "0"
| where isnotempty(Location)
| project
    TimeGenerated,
    UserPrincipalName,
    IPAddress,
    Country = tostring(parse_json(LocationDetails).countryOrRegion),
    City    = tostring(parse_json(LocationDetails).city),
    AppDisplayName,
    AuthenticationRequirement
| summarize
    SignInTimes  = make_list(TimeGenerated, 10),
    Countries    = make_set(Country, 10),
    IPs          = make_set(IPAddress, 10),
    Apps         = make_set(AppDisplayName, 5),
    SignInCount  = count()
    by UserPrincipalName, bin(TimeGenerated, totimespan(TravelWindowMinutes * 1m))
| where array_length(Countries) > 1
| mv-expand SignInTimes
| project WindowStart = TimeGenerated, UserPrincipalName, Countries, IPs, Apps, SignInCount
```

---

### Rule ID-005: Legacy Authentication Sign-In Success

| Field | Value |
|---|---|
| Rule ID | ID-005 |
| Rule Name | Identity - Legacy Authentication Sign-In Success - High |
| Severity | High |
| MITRE Tactic | Initial Access, Defense Evasion |
| MITRE Technique | T1078.004; T1562.001 |
| Data Sources | SigninLogs |
| Query Frequency | Every 15 minutes |
| Lookback Period | 15 minutes |
| Incident Creation | Yes |
| Status | Active |

```kql
let LegacyProtocols = dynamic([
    "Exchange ActiveSync", "Exchange Online PowerShell", "Exchange Web Services",
    "IMAP4", "MAPI Over HTTP", "Offline Address Book",
    "Outlook Anywhere (RPC over HTTP)", "POP3", "Reporting Web Services",
    "Other clients", "SMTP", "Authenticated SMTP"
]);
SigninLogs
| where TimeGenerated > ago(15m)
| where ClientAppUsed in (LegacyProtocols)
| where ResultType == "0"
| project
    TimeGenerated, UserPrincipalName, IPAddress, Location,
    ClientAppUsed, AppDisplayName, AuthenticationRequirement,
    ConditionalAccessStatus, UserAgent
| order by TimeGenerated desc
```

---

### Rule ID-006: Privileged Role Assignment Outside PIM

| Field | Value |
|---|---|
| Rule ID | ID-006 |
| Rule Name | Identity - Privileged Role Assignment Outside PIM - Critical |
| Severity | Critical |
| MITRE Tactic | Privilege Escalation |
| MITRE Technique | T1078.004 |
| Data Sources | AuditLogs |
| Query Frequency | Every 5 minutes |
| Lookback Period | 5 minutes |
| Incident Creation | Yes |
| Status | Active |

```kql
let PrivilegedRoles = dynamic([
    "Global Administrator", "Privileged Role Administrator", "Security Administrator",
    "User Account Administrator", "Exchange Administrator", "SharePoint Administrator",
    "Conditional Access Administrator", "Authentication Administrator",
    "Hybrid Identity Administrator", "Application Administrator",
    "Cloud Application Administrator", "Privileged Authentication Administrator"
]);
AuditLogs
| where TimeGenerated > ago(5m)
| where OperationName in ("Add member to role", "Add eligible member to role")
| where Category == "RoleManagement"
| extend
    TargetUPN = tostring(TargetResources[0].userPrincipalName),
    RoleName  = tostring(TargetResources[0].displayName),
    InitBy    = tostring(InitiatedBy.user.userPrincipalName),
    IsPIM     = tobool(AdditionalDetails has "PIM")
| where RoleName in (PrivilegedRoles)
| where IsPIM == false or isempty(IsPIM)
| project TimeGenerated, TargetUPN, RoleName, InitBy, IsPIM, OperationName, CorrelationId
```

---

### Rule ID-007: Bulk User Account Operations

```kql
let BulkThreshold = 10;
AuditLogs
| where TimeGenerated > ago(1h)
| where OperationName in (
    "Add user", "Delete user", "Update user", "Disable account",
    "Enable account", "Reset user password", "Revoke all refresh tokens for user"
  )
| extend
    InitBy    = tostring(InitiatedBy.user.userPrincipalName),
    TargetUPN = tostring(TargetResources[0].userPrincipalName)
| summarize
    OperationCount = count(),
    Operations     = make_set(OperationName, 10),
    AffectedUsers  = make_set(TargetUPN, 50),
    FirstSeen      = min(TimeGenerated),
    LastSeen       = max(TimeGenerated)
    by InitBy
| where OperationCount >= BulkThreshold
| project LastSeen, InitBy, OperationCount, Operations, AffectedUsers, FirstSeen
| order by OperationCount desc
```

---

### Rule ID-008: MFA Method Removed or Registration Deleted

```kql
AuditLogs
| where TimeGenerated > ago(15m)
| where OperationName in (
    "Delete authentication method",
    "User deleted",
    "User updated - StrongAuthenticationDetail updated",
    "Update user - StrongAuthenticationMethod",
    "User's authentication method deleted",
    "Authentication methods updated"
  )
| where OperationName !has "Add"
| extend
    TargetUPN     = tostring(TargetResources[0].userPrincipalName),
    InitBy        = tostring(InitiatedBy.user.userPrincipalName),
    ModifiedProps = tostring(TargetResources[0].modifiedProperties)
| project TimeGenerated, TargetUPN, InitBy, OperationName, ModifiedProps, CorrelationId
| extend SelfModified = (TargetUPN == InitBy)
| order by TimeGenerated desc
```

---

### Rule ID-009: High-Risk User Detection

```kql
AADRiskyUsers
| where TimeGenerated > ago(15m)
| where RiskLevel == "high"
    or RiskState in ("atRisk", "confirmedCompromised")
| project
    TimeGenerated,
    UserPrincipalName = UserDisplayName,
    RiskLevel,
    RiskState,
    RiskDetail,
    RiskLastUpdatedDateTime
| order by TimeGenerated desc
```

---

### Rule ID-010: Global Administrator Sign-In Anomaly

```kql
SigninLogs
| where TimeGenerated > ago(15m)
| where ResultType == "0"
| where UserPrincipalName startswith "adm-"
    or UserPrincipalName startswith "bg-"
| project
    TimeGenerated, UserPrincipalName, IPAddress, Location,
    AppDisplayName, ClientAppUsed, AuthenticationRequirement,
    ConditionalAccessStatus, RiskLevelDuringSignIn, UserAgent, CorrelationId
| order by TimeGenerated desc
```

---

## 4. Category 2: Endpoint and Infrastructure

### Rule EP-001: Endpoint Protection Non-Reporting Asset

**Status:** Pending Data Source -- ProtectionStatus table returned 0 rows for all 126 assets at time of assessment. This rule will not fire until endpoint telemetry is restored to Sentinel. (real finding)

```kql
let RecentlyReporting =
    ProtectionStatus
    | where TimeGenerated > ago(24h)
    | summarize LastReport = max(TimeGenerated) by Computer
    | where LastReport > ago(24h);
let KnownAssets =
    ProtectionStatus
    | summarize LastEverSeen = max(TimeGenerated) by Computer;
KnownAssets
| join kind=leftanti (RecentlyReporting) on Computer
| project Computer, LastEverSeen,
          HoursNotReporting = datetime_diff("hour", now(), LastEverSeen)
| order by HoursNotReporting desc
```

---

### Rule EP-002: ProtectionStatus Not Protected

**Status:** Pending Data Source (same POA as EP-001)

```kql
ProtectionStatus
| where TimeGenerated > ago(4h)
| where ProtectionState != "Protected"
    or ThreatStatusDetails !in ("No threats", "Threats remediated")
| summarize
    LastReport    = max(TimeGenerated),
    ProtState     = any(ProtectionState),
    ThreatDetails = any(ThreatStatusDetails),
    SignatureAge  = any(SignatureUpdateTime)
    by Computer
| project Computer, LastReport, ProtState, ThreatDetails, SignatureAge
| order by LastReport desc
```

---

### Rule EP-003: RDP Brute Force Threshold Breach

```kql
SecurityEvent
| where TimeGenerated > ago(1h)
| where EventID == 4625     // Failed logon
| where LogonType == 10     // RemoteInteractive (RDP)
| summarize
    FailureCount  = count(),
    SourceIPs     = make_set(IpAddress, 10),
    Accounts      = make_set(Account, 10),
    FirstSeen     = min(TimeGenerated),
    LastSeen      = max(TimeGenerated)
    by Computer
| where FailureCount >= 10
| project LastSeen, Computer, FailureCount, SourceIPs, Accounts, FirstSeen
| order by FailureCount desc
```

---

### Rule EP-004: New Local Administrator Account Created

```kql
SecurityEvent
| where TimeGenerated > ago(15m)
| where EventID in (4720, 4728, 4732)
| extend
    GroupName  = tostring(extract("Group Name:\\s+(\\S+)", 1, EventData)),
    NewAccount = tostring(extract("Subject Account Name:\\s+(\\S+)", 1, EventData)),
    ActionBy   = tostring(extract("Subject User Name:\\s+(\\S+)", 1, EventData))
| where GroupName has "Administrators" or EventID == 4720
| project TimeGenerated, Computer, EventID, Account, NewAccount, ActionBy, GroupName
| order by TimeGenerated desc
```

---

### Rule EP-005: Azure VM Created or Deleted Outside Maintenance Window

```kql
AzureActivity
| where TimeGenerated > ago(30m)
| where OperationNameValue in (
    "MICROSOFT.COMPUTE/VIRTUALMACHINES/WRITE",
    "MICROSOFT.COMPUTE/VIRTUALMACHINES/DELETE"
  )
| where ActivityStatusValue == "Success"
| extend
    VMName = tostring(parse_json(Properties).resource),
    VMOp   = iff(OperationNameValue has "DELETE", "DELETE", "CREATE/UPDATE")
| project TimeGenerated, Caller, VMName, VMOp, ResourceGroup, SubscriptionId, CorrelationId
| order by TimeGenerated desc
```

---

## 5. Category 3: Privilege and Access Escalation

### Rule PA-001: Subscription Owner Role Assignment

```kql
AzureActivity
| where TimeGenerated > ago(5m)
| where OperationNameValue == "MICROSOFT.AUTHORIZATION/ROLEASSIGNMENTS/WRITE"
| where ActivityStatusValue == "Success"
| extend
    RoleDefinition = tostring(parse_json(Properties).requestbody),
    PrincipalId    = tostring(parse_json(Properties).principalId)
| where RoleDefinition has "Owner"
    or RoleDefinition has "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
| project TimeGenerated, Caller, ResourceGroup, ResourceId,
          SubscriptionId, RoleDefinition, PrincipalId, CorrelationId
```

---

### Rule PA-002: New Global Administrator Assignment

```kql
AuditLogs
| where TimeGenerated > ago(5m)
| where OperationName in ("Add member to role", "Add eligible member to role")
| extend
    TargetUPN = tostring(TargetResources[0].userPrincipalName),
    RoleName  = tostring(TargetResources[0].displayName),
    InitBy    = tostring(InitiatedBy.user.userPrincipalName)
| where RoleName == "Global Administrator"
| project TimeGenerated, TargetUPN, RoleName, InitBy, OperationName, CorrelationId
```

---

### Rule PA-003: PIM Activation Outside Business Hours

```kql
AuditLogs
| where TimeGenerated > ago(15m)
| where OperationName == "Add member to role (PIM activation)"
    or OperationName has "PIM" and OperationName has "activate"
| extend
    HourUTC   = hourofday(TimeGenerated),
    DayOfWeek = dayofweek(TimeGenerated),
    TargetUPN = tostring(TargetResources[0].userPrincipalName),
    RoleName  = tostring(TargetResources[0].displayName),
    InitBy    = tostring(InitiatedBy.user.userPrincipalName)
| where HourUTC < 8 or HourUTC >= 18
    or DayOfWeek == 0 or DayOfWeek == 6
| project TimeGenerated, TargetUPN, RoleName, InitBy, HourUTC, DayOfWeek
| order by TimeGenerated desc
```

---

### Rule PA-004: Service Principal Assigned Privileged Role

```kql
AuditLogs
| where TimeGenerated > ago(5m)
| where OperationName in ("Add member to role", "Add app role assignment to service principal")
| extend
    TargetType = tostring(TargetResources[0].type),
    TargetName = tostring(TargetResources[0].displayName),
    RoleName   = tostring(TargetResources[1].displayName),
    InitBy     = tostring(InitiatedBy.user.userPrincipalName)
| where TargetType == "ServicePrincipal"
| where RoleName in (
    "Global Administrator", "Privileged Role Administrator",
    "Security Administrator", "Application Administrator"
  )
| project TimeGenerated, TargetName, RoleName, InitBy, OperationName
```

---

## 6. Category 4: Application and Service Principal

### Rule AP-001: High-Privilege Application Permission Grant

```kql
let DangerousPermissions = dynamic([
    "Directory.ReadWrite.All", "User.ReadWrite.All", "Mail.ReadWrite", "Mail.Send",
    "Files.ReadWrite.All", "Sites.FullControl.All", "RoleManagement.ReadWrite.Directory",
    "GroupMember.ReadWrite.All", "AppRoleAssignment.ReadWrite.All", "Application.ReadWrite.All"
]);
AuditLogs
| where TimeGenerated > ago(15m)
| where OperationName in (
    "Add app role assignment to service principal",
    "Add delegated permission grant",
    "Consent to application"
  )
| extend
    AppName = tostring(TargetResources[0].displayName),
    Scope   = tostring(parse_json(AdditionalDetails)[0].value),
    InitBy  = tostring(InitiatedBy.user.userPrincipalName)
| where Scope has_any (DangerousPermissions)
    or AdditionalDetails has_any (DangerousPermissions)
| project TimeGenerated, AppName, Scope, InitBy, OperationName, CorrelationId
```

---

### Rule AP-002: New Application Registration

```kql
AuditLogs
| where TimeGenerated > ago(30m)
| where OperationName == "Add application"
| extend
    AppName = tostring(TargetResources[0].displayName),
    AppId   = tostring(TargetResources[0].id),
    InitBy  = tostring(InitiatedBy.user.userPrincipalName)
| project TimeGenerated, AppName, AppId, InitBy, CorrelationId
```

---

### Rule AP-003: Service Principal New Credential Created

```kql
AuditLogs
| where TimeGenerated > ago(15m)
| where OperationName in (
    "Add service principal credentials",
    "Update application - Certificates and secrets management"
  )
| extend
    AppName  = tostring(TargetResources[0].displayName),
    InitBy   = tostring(InitiatedBy.user.userPrincipalName),
    CredType = tostring(parse_json(AdditionalDetails)[0].value)
| project TimeGenerated, AppName, InitBy, CredType, OperationName, CorrelationId
```

---

### Rule AP-004: OAuth Consent to Unverified Publisher

```kql
AuditLogs
| where TimeGenerated > ago(15m)
| where OperationName == "Consent to application"
| extend
    AppName   = tostring(TargetResources[0].displayName),
    InitBy    = tostring(InitiatedBy.user.userPrincipalName),
    ConsentBy = tostring(parse_json(AdditionalDetails)[0].value)
| where AdditionalDetails has "unverified"
    or not(AdditionalDetails has "verified")
| project TimeGenerated, AppName, InitBy, ConsentBy, OperationName, CorrelationId
```

---

## 7. Category 5: Cloud Resource and Configuration

### Rule CR-001: NSG Rule Modified in Production

```kql
AzureActivity
| where TimeGenerated > ago(15m)
| where OperationNameValue in (
    "MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/SECURITYRULES/WRITE",
    "MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/WRITE"
  )
| where ActivityStatusValue == "Success"
| extend
    NSGName    = tostring(parse_json(Properties).resource),
    RuleChange = tostring(parse_json(Properties).requestbody)
| project TimeGenerated, Caller, NSGName, ResourceGroup, RuleChange, CorrelationId
| order by TimeGenerated desc
```

---

### Rule CR-002: Azure Resource Deletion in Production Resource Group

```kql
AzureActivity
| where TimeGenerated > ago(15m)
| where OperationNameValue endswith "/DELETE"
| where ActivityStatusValue == "Success"
| where ResourceGroup has "prod"
    or ResourceGroup has "production"
| project TimeGenerated, Caller, ResourceGroup, ResourceId, OperationNameValue, CorrelationId
| order by TimeGenerated desc
```

---

### Rule CR-003: Storage Account Public Access Enabled

```kql
AzureActivity
| where TimeGenerated > ago(15m)
| where OperationNameValue in (
    "MICROSOFT.STORAGE/STORAGEACCOUNTS/WRITE",
    "MICROSOFT.STORAGE/STORAGEACCOUNTS/BLOBSERVICES/CONTAINERS/WRITE"
  )
| where ActivityStatusValue == "Success"
| extend RequestBody = tostring(parse_json(Properties).requestbody)
| where RequestBody has "allowBlobPublicAccess" and RequestBody has "true"
| project TimeGenerated, Caller, ResourceId, ResourceGroup, RequestBody, CorrelationId
```

---

### Rule CR-004: Defender for Cloud High or Critical Alert

**Real Environment Finding:** At time of the March 2026 gap assessment, the SecurityAlert table contained High and Critical alerts with no evidence of triage. This rule surfaces all new alerts into the Sentinel incident queue immediately. (real finding)

```kql
SecurityAlert
| where TimeGenerated > ago(15m)
| where AlertSeverity in ("High", "Critical")
| where ProviderName == "Azure Security Center"
    or ProviderName == "Microsoft Defender for Cloud"
| project
    TimeGenerated, AlertName, AlertSeverity, CompromisedEntity,
    Description, RemediationSteps, ExtendedLinks, Entities, ProviderName, SystemAlertId
| order by AlertSeverity asc, TimeGenerated desc
```

---

## 8. Category 6: Monitoring Integrity

### Rule MI-001: Log Analytics Workspace Heartbeat Gap

```kql
let HeartbeatThreshold = 2h;
let LastHeartbeats =
    Heartbeat
    | summarize LastHeartbeat = max(TimeGenerated) by Computer
    | where LastHeartbeat < ago(HeartbeatThreshold);
LastHeartbeats
| project Computer, LastHeartbeat,
          HoursGap = datetime_diff("hour", now(), LastHeartbeat)
| order by HoursGap desc
```

---

### Rule MI-002: Sentinel Connector Data Ingestion Gap

```kql
let AuditLast  = toscalar(AuditLogs     | summarize max(TimeGenerated));
let SigninLast  = toscalar(SigninLogs    | summarize max(TimeGenerated));
let AlertLast   = toscalar(SecurityAlert | summarize max(TimeGenerated));
let NowTime     = now();
datatable(TableName:string, LastIngestion:datetime)[
    "AuditLogs",     AuditLast,
    "SignInLogs",    SigninLast,
    "SecurityAlert", AlertLast
]
| extend
    HoursSinceLastData = datetime_diff("hour", NowTime, LastIngestion),
    Status = iff(datetime_diff("hour", NowTime, LastIngestion) > 2,
                 "INGESTION GAP DETECTED", "OK")
| where Status == "INGESTION GAP DETECTED"
| project TableName, LastIngestion, HoursSinceLastData, Status
```

---

## 9. Compliance Posture Queries (Non-Alerting)

### CP-001: SFA Sign-In Volume Baseline

**Real Environment Finding:** Returned 83,600 SFA successes from 203,906 total sign-ins (40.9% SFA rate) during the live March 2026 assessment. Target after CA deployment: zero. (real finding)

```kql
SigninLogs
| where TimeGenerated > ago(7d)
| where AuthenticationRequirement == "singleFactorAuthentication"
| where ResultType == "0"
| summarize
    SFACount       = count(),
    UniqueAccounts = dcount(UserPrincipalName),
    UniqueApps     = dcount(AppDisplayName),
    SFAbyApp       = make_set(AppDisplayName, 20)
| extend
    TotalSignIns = toscalar(
        SigninLogs
        | where TimeGenerated > ago(7d)
        | where ResultType == "0"
        | count
    )
| extend SFAPercentage = round(100.0 * SFACount / TotalSignIns, 2)
| project SFACount, UniqueAccounts, UniqueApps, TotalSignIns, SFAPercentage, SFAbyApp
```

---

### CP-002: User Accounts with Blank Mandatory Attributes

**Real Environment Finding:** 2,315 accounts (100% of user accounts) had blank Department, Job Title, and Manager fields in the live environment. (real finding)

```kql
AuditLogs
| where TimeGenerated > ago(90d)
| where OperationName == "Add user"
| extend
    UPN        = tostring(TargetResources[0].userPrincipalName),
    Department = tostring(parse_json(AdditionalDetails)[?(@.key=="Department")].value),
    JobTitle   = tostring(parse_json(AdditionalDetails)[?(@.key=="JobTitle")].value)
| where isempty(Department) or isempty(JobTitle)
| project TimeGenerated, UPN, Department, JobTitle
| order by TimeGenerated desc
```

---

### CP-003: Active Guest Accounts Without Sponsor Attribute

```kql
SigninLogs
| where TimeGenerated > ago(90d)
| where UserType == "Guest"
| where ResultType == "0"
| summarize LastSignIn = max(TimeGenerated), SignInCount = count()
    by UserPrincipalName
| order by LastSignIn desc
// Cross-reference against Entra ID user export: UserType = Guest AND SponsorUPN = blank
```

---

### CP-004: Global Administrator Count Verification

```kql
AuditLogs
| where TimeGenerated > ago(90d)
| where OperationName in ("Add member to role", "Remove member from role")
| extend
    TargetUPN = tostring(TargetResources[0].userPrincipalName),
    RoleName  = tostring(TargetResources[0].displayName),
    InitBy    = tostring(InitiatedBy.user.userPrincipalName),
    Action    = OperationName
| where RoleName == "Global Administrator"
| project TimeGenerated, Action, TargetUPN, RoleName, InitBy
| order by TimeGenerated desc
// Target: maximum 2 active Global Administrator assignments
```

---

### CP-005: Open SecurityAlert Backlog

**Real Environment Finding:** This query would have returned multiple overdue High and Critical alerts if run during the March 2026 assessment. The count was confirmed greater than zero. These alerts had never been reviewed. (real finding)

```kql
let CriticalSLA = 1h;
let HighSLA     = 4h;
let MediumSLA   = 24h;
SecurityAlert
| where TimeGenerated > ago(7d)
| where AlertSeverity in ("Critical", "High", "Medium")
| extend
    SLAThreshold = case(
        AlertSeverity == "Critical", CriticalSLA,
        AlertSeverity == "High",     HighSLA,
        MediumSLA
    )
| extend
    HoursOld         = datetime_diff("hour", now(), TimeGenerated),
    OverSLAThreshold = (now() - TimeGenerated) > SLAThreshold
| where OverSLAThreshold == true
| project TimeGenerated, AlertName, AlertSeverity, CompromisedEntity, HoursOld, Description
| order by AlertSeverity asc, HoursOld desc
```

---

## 10. Rule Inventory and Coverage Matrix

| Rule ID | Name | Severity | MITRE Tactic | Status |
|---|---|---|---|---|
| ID-001 | Break-Glass Account Sign-In | Critical | Privilege Escalation | Active |
| ID-002 | Single-Factor Authentication Success | Critical | Initial Access | Active |
| ID-003 | Failed Sign-In Threshold Breach | High | Credential Access | Active |
| ID-004 | Impossible Travel Detection | High | Initial Access | Active |
| ID-005 | Legacy Authentication Sign-In Success | High | Defense Evasion | Active |
| ID-006 | Privileged Role Assignment Outside PIM | Critical | Privilege Escalation | Active |
| ID-007 | Bulk User Account Operations | High | Impact | Active |
| ID-008 | MFA Method Removed or Registration Deleted | High | Defense Evasion | Active |
| ID-009 | High-Risk User Detection | High | Credential Access | Active |
| ID-010 | Global Admin Sign-In Anomaly | Critical | Privilege Escalation | Active |
| EP-001 | Endpoint Protection Non-Reporting Asset | High | Defense Evasion | Pending (POA-004) |
| EP-002 | ProtectionStatus Not Protected | High | Defense Evasion | Pending (POA-004) |
| EP-003 | RDP Brute Force Threshold Breach | High | Credential Access | Active |
| EP-004 | New Local Administrator Account Created | High | Persistence | Active |
| EP-005 | Azure VM Created or Deleted Outside Window | Medium | Impact | Active |
| PA-001 | Subscription Owner Role Assignment | Critical | Privilege Escalation | Active |
| PA-002 | New Global Administrator Assignment | Critical | Privilege Escalation | Active |
| PA-003 | PIM Activation Outside Business Hours | High | Privilege Escalation | Active |
| PA-004 | Service Principal Assigned Privileged Role | Critical | Privilege Escalation | Active |
| AP-001 | High-Privilege Application Permission Grant | High | Collection | Active |
| AP-002 | New Application Registration | Medium | Persistence | Active |
| AP-003 | Service Principal New Credential Created | High | Credential Access | Active |
| AP-004 | OAuth Consent to Unverified Publisher | High | Collection | Active |
| CR-001 | NSG Rule Modified in Production | Medium | Defense Evasion | Active |
| CR-002 | Azure Resource Deletion in Production | Medium | Impact | Active |
| CR-003 | Storage Account Public Access Enabled | High | Exfiltration | Active |
| CR-004 | Defender for Cloud High or Critical Alert | High | Multiple | Active |
| MI-001 | LAW Heartbeat Gap Detected | High | Defense Evasion | Active |
| MI-002 | Sentinel Connector Ingestion Gap | High | Defense Evasion | Active |

**Total: 29 rules. 27 Active. 2 Pending (require endpoint telemetry restoration). All 18 mandatory detection categories from the Security Monitoring Policy are covered.**

---

*Organization: ABC Corp | Microsoft Azure Cloud Environment. This KQL Detection Rule Library was produced from a real gap assessment of a live Azure shared tenant. At time of assessment: zero analytics rules were active, unreviewed High and Critical alerts existed in the SecurityAlert table, and the ProtectionStatus table was empty for all 126 assets. All organization names and identifiers have been sanitized. Published for portfolio and educational purposes only.*
