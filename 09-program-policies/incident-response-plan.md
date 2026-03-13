# Incident Response Plan

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity and Access Governance Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> **Disclaimer:** This is a sanitized version of an Incident Response Plan produced following a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This Incident Response Plan was produced as a direct deliverable from a real, evidence-based gap assessment of a live Microsoft Azure shared tenant. All playbooks, KQL queries, escalation thresholds, and alert type references are grounded in actual findings from that environment, including real telemetry counts from Microsoft Sentinel, Tenable vulnerability scan data, and Microsoft Entra ID exports gathered at assessment time. This is not a generic template -- every procedural decision traces to a specific finding.

**Driving Finding:** IR-8 gap -- no formal Incident Response Plan existed. NIST SP 800-53 Incident Response (IR) control family rated Gap Rating 1 (Not Implemented). 24,905 PowerShell encoded command alerts and 50 active alert types in the SIEM produced no documented triage response. SecurityIncident records showed active lifecycle states with no documented procedures. This plan directly remediates POA-009.

**Assessment Standards Applied:** NIST SP 800-53 Rev 5 | NIST SP 800-61 Rev 2 | NIST CSF 2.0 | ISO/IEC 27001:2022 | SOC 2 TSC

---

## Table of Contents

1. Purpose and Objectives
2. Scope
3. Roles and Responsibilities
4. Incident Classification and Severity
5. Incident Response Lifecycle
   - 5.1 Preparation
   - 5.2 Detection and Analysis
   - 5.3 Containment
   - 5.4 Eradication
   - 5.5 Recovery
   - 5.6 Post-Incident Review
6. Communication and Notification
7. Evidence Handling and Chain of Custody
8. Specific Playbooks
   - 8.1 Compromised User Account
   - 8.2 Privileged Account Compromise or Abuse
   - 8.3 Malware or Ransomware Detection
   - 8.4 Credential Stuffing or Brute Force Attack
   - 8.5 Suspicious PowerShell Execution
   - 8.6 Unauthorized Data Access or Exfiltration
9. KQL Detection Queries
10. Escalation Matrix
11. Incident Tracking and Reporting
12. Plan Testing and Maintenance
13. Related Documents and References
14. Approval and Sign-Off

---

## 1. Purpose and Objectives

This Incident Response Plan (IRP) establishes ABC Corp's formal process for preparing for, detecting, analyzing, containing, eradicating, and recovering from information security incidents. It defines roles, responsibilities, communication protocols, and specific playbooks for the incident types most relevant to the cloud environment.

The objectives of this plan are to:

- Minimize the impact of security incidents on users, data, and operations
- Ensure rapid, consistent, and documented response to confirmed incidents
- Provide specific procedural guidance for the alert types actively generating in the SIEM
- Establish a measurable review cycle so that each incident improves the program
- Satisfy NIST SP 800-53 IR control family requirements, ISO/IEC 27001:2022 Annex A 5.24 through 5.28, NIST CSF 2.0 Respond and Recover functions, and SOC 2 TSC CC7.3, CC7.4, and CC7.5

**Driving Evidence (real assessment finding):** The gap assessment identified 24,905 PowerShell encoded command alerts as the highest-volume alert type in the SIEM SecurityAlert table with no documented response. SecurityIncident records showed lifecycle states of New, Active, Closed, and Deleted with no evidence of structured triage or documented outcomes. This plan establishes the missing procedural layer.

---

## 2. Scope

**Systems Covered:** All systems, virtual machines, cloud services, and user accounts within the ABC Corp cloud tenant, including Microsoft Entra ID, Microsoft 365, the SIEM, Log Analytics Workspace, vulnerability scanning, and any additional connected services.

**Incident Types Covered:** All information security incidents affecting the confidentiality, integrity, or availability of ABC Corp systems and data, including but not limited to: account compromise, privileged access abuse, malware, ransomware, unauthorized access, data exfiltration, and denial of service.

**Users Covered:** All administrators, staff, students, and third parties with access to ABC Corp systems.

**Out of Scope:** Incidents originating entirely within cloud provider physical infrastructure. Physical datacenter incidents are the responsibility of the cloud provider under its shared responsibility model.

---

## 3. Roles and Responsibilities

### 3.1 Incident Commander

**Appointed by:** Security Lead on a per-incident basis for Critical and High severity incidents.

**Responsibilities:**
- Overall coordination of the incident response
- Final decisions on containment and recovery actions
- Stakeholder communication and escalation
- Declaration of incident closure

### 3.2 Security Lead / Cybersecurity Owner

**Responsibilities:**
- Authorization of containment actions that affect user access or system availability
- Notification of leadership and external parties as required
- Approval of post-incident review findings
- Maintenance and approval of this plan

### 3.3 SOC Analyst

**Responsibilities:**
- First responder for all alert triage
- Initial severity classification
- Initial containment actions within defined scope
- Evidence collection and log preservation
- Escalation to Incident Commander for Critical and High severity incidents
- Incident record maintenance in the SIEM

### 3.4 Identity Administrator

**Responsibilities:**
- Execution of account-level containment actions (disable account, revoke sessions, reset credentials, remove roles)
- Review of sign-in logs and access patterns for affected accounts
- Entra ID-specific investigation queries
- Account restoration during recovery

### 3.5 Cloud Administrator

**Responsibilities:**
- Isolation of affected virtual machines or network segments
- Snapshot creation for forensic preservation
- Firewall rule and network security group modifications for containment
- System restoration from backup during recovery

### 3.6 GRC Analyst

**Responsibilities:**
- Post-incident review facilitation
- Risk register and POA&M updates following incidents
- Documentation of lessons learned
- Plan maintenance and update coordination

### 3.7 All Users

**Responsibilities:**
- Report suspected incidents to the SOC Analyst or Security Lead immediately
- Preserve evidence (do not delete emails, logs, or messages related to a suspected incident)
- Cooperate with the response team investigation
- Do not attempt independent remediation without authorization from the response team

---

## 4. Incident Classification and Severity

### 4.1 Severity Definitions

| Severity | Score | Definition | Response SLA |
|---|---|---|---|
| Critical (SEV-1) | 5 | Active exploitation, confirmed breach, ransomware, or compromise of a privileged account with active tenant access | Immediate -- response begins within 15 minutes of detection |
| High (SEV-2) | 4 | Credible threat indicator, confirmed unauthorized access without confirmed data impact, suspicious privileged activity | Urgent -- response begins within 1 hour of detection |
| Medium (SEV-3) | 3 | Suspicious activity requiring investigation, anomalous sign-in patterns, high-volume alerts without confirmed compromise | Standard -- investigation begins within 4 hours of detection |
| Low (SEV-4) | 2 | Informational events, policy violations without active threat, single failed sign-in spikes | Routine -- review within 24 hours |
| Informational (SEV-5) | 1 | Telemetry, audit trail events, and detection tuning events | Weekly review cadence |

### 4.2 Incident Categories

| Category | Examples | Default Severity |
|---|---|---|
| Identity and Access | Account compromise, unauthorized role assignment, MFA bypass | High to Critical |
| Privileged Access | Admin account abuse, PIM manipulation, service principal misuse | Critical |
| Malware and Ransomware | Endpoint infection, encoded command execution, file encryption | Critical |
| Credential Attack | Brute force, credential stuffing, password spray | High |
| Data Exfiltration | Unauthorized data download, DLP trigger, anomalous transfer | High to Critical |
| Denial of Service | Resource exhaustion, lockout cascade, service disruption | Medium to High |
| Insider Threat | Deliberate unauthorized access, data theft by authorized user | High to Critical |
| Configuration Abuse | Unauthorized policy change, security control modification | Medium to High |

### 4.3 Severity Escalation Triggers

An incident must be immediately re-classified to Critical if any of the following are confirmed:

- A Global Administrator account or service principal with Owner rights is involved
- Data exfiltration is confirmed or suspected
- Ransomware indicators are detected (file encryption, ransom note, mass file modification)
- The incident affects more than 50 users simultaneously
- An attacker has maintained persistent access for more than 24 hours
- External notification obligations are triggered

---

## 5. Incident Response Lifecycle

### 5.1 Preparation

Preparation activities must be maintained continuously, not initiated at the time of an incident.

**Detective Controls (maintained in SIEM):**
- Unified Audit Log enabled for all Entra ID and Microsoft 365 events
- SecurityAlert, SecurityIncident, AuditLogs, and SigninLogs tables actively collecting in Log Analytics Workspace
- Alert rules configured for account lockout, suspicious PowerShell, failed sign-in thresholds, and role assignment changes
- ProtectionStatus table monitored for endpoint protection gaps

**Response Readiness:**
- This plan reviewed and approved by Security Lead
- Incident contact list maintained and accessible offline (see Section 10)
- SOC Analyst has pre-authorized access to disable accounts, revoke sessions, and snapshot VMs
- Identity Administrator has pre-authorized access to Entra ID emergency controls
- Break-glass account credentials secured offline and tested quarterly

### 5.2 Detection and Analysis

**Primary Detection Sources:**

| Source | Table / Signal | Alert Threshold |
|---|---|---|
| SIEM | SecurityAlert | Any High or Critical severity rule trigger |
| SIEM | SecurityIncident | New incident creation |
| Log Analytics Workspace | SigninLogs | Failed sign-ins exceeding 5 in 24 hours per account |
| Log Analytics Workspace | AuditLogs | Privileged role assignment, MFA change, account creation outside provisioning window |
| Vulnerability Scanner | Scan results | New Critical finding on any asset |
| User Report | Helpdesk or direct contact | Any user-reported anomaly |

**Triage Process:**

Upon receiving an alert or report, the SOC Analyst must complete the following within the applicable response SLA:

1. Confirm the alert is a genuine event and not a false positive or known-good activity
2. Identify the affected account(s), asset(s), and scope of potential impact
3. Assign initial severity classification using Section 4.1
4. Create or update the SecurityIncident record with: incident ID, detection time, initial classification, affected entities, and analyst name
5. Escalate to Incident Commander immediately for Critical and High severity incidents
6. Notify Security Lead within 1 hour for all High and Critical incidents
7. Document all analysis actions with timestamps in the incident record

**Key Analysis Questions:**

- What account or asset is involved?
- What is the earliest evidence of the activity (first seen timestamp)?
- Is the activity ongoing or historical?
- Are there indicators of lateral movement or privilege escalation?
- Is any data potentially at risk?
- Is the activity consistent with a known authorized operation?

### 5.3 Containment

Containment actions must be authorized by the Incident Commander (Critical/High) or SOC Analyst (Medium/Low) before execution. All actions must be documented with timestamp and actor in the incident record.

**Account-Level Containment (Identity Administrator):**

| Action | Trigger | Location |
|---|---|---|
| Disable user account | Confirmed or suspected compromise | Entra ID admin portal |
| Revoke all active sessions | Account compromise, token theft | Entra ID -- Revoke sessions |
| Reset password | Confirmed compromise | Entra ID -- Reset password |
| Remove privileged roles | Privilege abuse | Entra ID -- Role assignments |
| Block sign-in via CA | Targeted block without full disable | Conditional Access policy |
| Invalidate app registrations | Service principal compromise | Entra ID -- App registrations |

**Endpoint-Level Containment (Cloud Administrator):**

| Action | Trigger | Method |
|---|---|---|
| Network isolation | Malware confirmed, active C2 suspected | NSG rule -- deny all inbound/outbound |
| VM snapshot | Forensic preservation before action | Cloud portal snapshot |
| VM shutdown | Active ransomware, uncontrollable spread | Cloud portal -- Stop VM |
| Restore from backup | Confirmed ransomware or unrecoverable state | Cloud Backup -- Restore point |

### 5.4 Eradication

Eradication must be completed before any affected account or system is restored to active use.

**Account Eradication Steps:**
1. Verify all active sessions have been revoked and remain revoked
2. Confirm no unauthorized OAuth applications remain consented under the affected account
3. Verify no forwarding rules or inbox rules were created by the attacker
4. Confirm attacker-created accounts or role assignments have been removed
5. Validate MFA methods on the account have not been modified by the attacker
6. Document all confirmed attacker-controlled artifacts removed

**Endpoint Eradication Steps:**
1. Conduct malware scan on isolated endpoint using updated definitions
2. Identify and remove all persistence mechanisms (scheduled tasks, registry run keys, startup items)
3. Remove or quarantine identified malicious files
4. If full eradication cannot be confirmed, reimage the endpoint from known-good baseline
5. Validate endpoint protection agent is installed and reporting

**Eradication Completion Criteria:**
- No active sessions or tokens associated with the compromised account
- No malware detected on scanned systems
- All unauthorized changes documented and reversed
- Eradication sign-off by Incident Commander

### 5.5 Recovery

Recovery may not begin until eradication is confirmed.

**Account Recovery Steps:**
1. Re-enable account only after eradication is confirmed
2. Force password reset at next sign-in
3. Re-register MFA methods through a verified out-of-band process
4. Restore legitimate role assignments based on pre-incident documentation
5. Monitor account sign-in activity for 72 hours post-restoration

**Endpoint Recovery Steps:**
1. Restore from clean backup or reimage from baseline if eradication was incomplete
2. Apply all outstanding patches before returning to production
3. Verify endpoint protection agent is active and reporting
4. Monitor for 72 hours post-restoration

**Recovery Completion Criteria:**
- Affected accounts and systems operational and monitored
- No further indicators of compromise in 72-hour post-restoration window
- Incident Commander declares recovery complete
- SecurityIncident record updated to Closed status

### 5.6 Post-Incident Review

A post-incident review must be conducted for all Critical and High severity incidents within 5 business days of closure.

**Post-Incident Review Agenda:**
1. Timeline reconstruction -- first detected, first contained, eradicated, recovered
2. Root cause analysis -- how did the incident occur? What control failed or was absent?
3. Detection evaluation -- was detection timely? Did existing alerts fire appropriately?
4. Response evaluation -- were response times within SLA?
5. Lessons learned -- what would have improved the response?
6. Action items -- what changes to controls, processes, or documentation are required?

**Required Outputs:**
- Post-Incident Review document (filed in incident record)
- Risk register update (if new risk identified or existing risk requires re-scoring)
- POA&M update (if a control gap contributed to the incident)
- Updated detection rule or playbook (if a detection or response gap was identified)

---

## 6. Communication and Notification

### 6.1 Internal Notification Timeline

| Recipient | Trigger | Timeline |
|---|---|---|
| Incident Commander | Critical or High severity confirmed | Within 15 minutes (Critical) or 1 hour (High) |
| Security Lead | Critical or High severity confirmed | Within 1 hour |
| Affected user(s) | Account action taken (disable, password reset) | Within 2 hours of action |
| Leadership | Critical severity or potential data breach | Within 4 hours |

### 6.2 Communication Principles

- All external communications about a security incident must be reviewed and approved by the Security Lead before transmission
- Do not use potentially compromised channels for incident communications
- Do not speculate about root cause or impact in initial notifications -- state what is known and that investigation is ongoing
- Maintain a communication log with timestamp, recipient, content, and sender for each notification sent

### 6.3 External Notification

If the incident involves potential compromise of user data, the Security Lead must assess notification obligations within 24 hours of the incident being classified as High or Critical. Legal counsel should be consulted for incidents involving confirmed data exfiltration.

---

## 7. Evidence Handling and Chain of Custody

### 7.1 Evidence Preservation Principles

- Evidence must be preserved before containment actions are taken wherever operationally feasible
- Log data must not be modified, deleted, or moved during an active investigation
- VM snapshots must be created before an affected endpoint is isolated or modified
- All documentation must be timestamped

### 7.2 Log Sources and Retention

| Log Source | Table | Minimum Retention |
|---|---|---|
| Identity sign-in events | SigninLogs | 90 days online |
| Entra ID audit events | AuditLogs | 90 days online |
| Security alerts | SecurityAlert | 90 days online |
| Security incidents | SecurityIncident | 1 year |
| Endpoint protection | ProtectionStatus | 90 days online |
| Vulnerability data | Scanner exports | 1 year |

### 7.3 Chain of Custody Requirements

For incidents where legal action or regulatory notification may result, the SOC Analyst must document:
- What evidence was collected
- When it was collected (timestamp)
- Who collected it
- Where it is stored
- Who has accessed it since collection

---

## 8. Specific Playbooks

### Playbook 8.1: Compromised User Account

**Trigger:** Alert on successful sign-in following multiple failures, sign-in from new country or impossible travel, or user reports unauthorized access.

**Triage (SOC Analyst):**
1. Query SigninLogs for the affected account -- review location, device, client app, CA status, and MFA method for the last 30 days
2. Query AuditLogs for changes made by the account in the last 30 days
3. Confirm whether activity is consistent with the user's known behavior
4. Classify severity and escalate if High or Critical

**KQL -- Account sign-in investigation:**
```kql
SigninLogs
| where UserPrincipalName == "user@domain.com"
| project TimeGenerated, UserPrincipalName, IPAddress, Location, AppDisplayName,
          ConditionalAccessStatus, AuthenticationRequirement, ResultType, ResultDescription
| order by TimeGenerated desc
```

**Containment (Identity Administrator):**
1. Disable account in Entra ID
2. Revoke all active sessions
3. Remove any unauthorized role assignments identified in AuditLogs
4. Block any unauthorized OAuth app consents
5. Remove unauthorized inbox rules or forwarding rules

**Eradication:** Verify attacker persistence mechanisms removed per Section 5.4.

**Recovery:** Re-enable account per Section 5.5. Notify user and require out-of-band MFA re-registration.

---

### Playbook 8.2: Privileged Account Compromise or Abuse

**Trigger:** Unauthorized Global Administrator activity, suspicious role assignment, privileged activation outside business hours, or alert on admin account sign-in anomaly.

**Special Handling:** This playbook triggers automatic escalation to Critical. Security Lead must be notified immediately.

**Triage (SOC Analyst + Identity Administrator):**
1. Identify all role assignments made by or to the affected account in the last 7 days
2. Review all AuditLogs entries for the account -- especially service principal creation, policy changes, and directory setting modifications
3. Identify any new accounts created, new app registrations, or Conditional Access policy modifications
4. Determine if the attacker has created any backdoor accounts or persistent access paths

**KQL -- Privileged role assignment audit:**
```kql
AuditLogs
| where Category == "RoleManagement"
| where OperationName has_any ("Add member to role", "Remove member from role", "Add eligible member")
| project TimeGenerated, OperationName, InitiatedBy, TargetResources, Result
| order by TimeGenerated desc
```

**KQL -- Admin account sign-in review:**
```kql
SigninLogs
| where UserPrincipalName in ("admin1@domain.com", "admin2@domain.com")
| project TimeGenerated, UserPrincipalName, IPAddress, Location, AppDisplayName,
          AuthenticationRequirement, ConditionalAccessStatus, ResultType
| order by TimeGenerated desc
```

**Containment:**
1. Immediately disable the affected privileged account
2. Revoke all sessions
3. Remove all role assignments from the account
4. Review and revoke any attacker-created accounts
5. Review all Conditional Access policy changes and revert unauthorized modifications

**Eradication:** Full audit of all attacker actions during the access window. Reverse all unauthorized changes. Verify no backdoor accounts or service principals remain.

**Recovery:** Do not restore privileged access until full audit is complete and Security Lead approves.

---

### Playbook 8.3: Malware or Ransomware Detection

**Trigger:** Endpoint protection alert, critical vulnerability finding indicating active exploitation, SecurityAlert for malicious file or process, or user reports encrypted files or ransom note.

**Triage (SOC Analyst):**
1. Identify affected asset(s) by hostname or IP from the alert
2. Determine scope -- is there evidence of lateral movement to other assets?
3. Classify as Critical immediately if file encryption or ransom note is confirmed

**Containment (Cloud Administrator -- immediate on confirmation):**
1. Create VM snapshot before any action
2. Isolate affected VM via NSG rule (deny all inbound/outbound except management port)
3. Do not shut down immediately -- running memory may contain forensic artifacts
4. Identify any network shares or mounted drives accessible from the affected VM

**Eradication:**
1. Conduct full malware scan using updated definitions on isolated VM
2. Identify and remove all malicious artifacts, persistence mechanisms, and attacker tools
3. If full eradication cannot be confirmed, reimage from known-good baseline

**Recovery:**
1. Restore from pre-infection backup if data was encrypted
2. Apply all outstanding patches before returning to production
3. Verify endpoint protection agent active and reporting before production return

---

### Playbook 8.4: Credential Stuffing or Brute Force Attack

**Trigger:** Alert on account exceeding 5 failed sign-ins in 24 hours, or 20+ failures from a single IP across multiple accounts.

**Driving Evidence (real assessment finding):** The gap assessment identified 30 accounts with more than 5 failed sign-in attempts. The highest-volume account recorded 512 failures from an external domain with no lockout triggered.

**Triage (SOC Analyst):**
1. Query SigninLogs for failed sign-in volume by account and source IP for the last 24 hours
2. Determine whether failures target a single account (brute force) or spread across many accounts from a single IP (credential stuffing)
3. Determine whether any failures were followed by a successful sign-in (potential successful compromise)

**KQL -- Failed sign-in analysis:**
```kql
SigninLogs
| where ResultType != "0"
| summarize FailureCount = count(), DistinctIPs = dcount(IPAddress)
    by UserPrincipalName, bin(TimeGenerated, 1h)
| where FailureCount > 5
| order by FailureCount desc
```

**KQL -- Source IP attack pattern:**
```kql
SigninLogs
| where ResultType != "0"
| summarize FailureCount = count(), DistinctUsers = dcount(UserPrincipalName)
    by IPAddress, bin(TimeGenerated, 1h)
| where FailureCount > 20
| order by FailureCount desc
```

**Containment:**
1. For accounts with successful sign-in following failures -- treat as compromised account per Playbook 8.1
2. For ongoing high-volume failures -- implement Conditional Access block for source IP or named location
3. For accounts exceeding 20 failures without success -- temporarily suspend account pending investigation

**Recovery:** Re-enable suspended accounts after confirming no successful compromise. Implement account lockout policy.

---

### Playbook 8.5: Suspicious PowerShell Execution

**Trigger:** SecurityAlert for PowerShell encoded command execution, suspicious script execution alert, or vulnerability scanner alert indicating PowerShell-based exploitation.

**Driving Evidence (real assessment finding):** The gap assessment identified 24,905 PowerShell encoded command alerts as the highest-volume alert type in the SIEM SecurityAlert table. This volume had not been triaged. Root cause was unknown and may represent attacker tooling, authorized activity, or misconfigured systems.

**Triage (SOC Analyst):**
1. Identify the source asset and account associated with the alert
2. Decode the Base64-encoded command if possible and review the decoded content
3. Determine if the activity is consistent with a known authorized operation
4. If decoded content contains network callbacks, file download instructions, credential dumping commands, or persistence mechanisms -- escalate to High or Critical immediately

**KQL -- PowerShell alert investigation:**
```kql
SecurityAlert
| where AlertName has "Powershell"
| project TimeGenerated, AlertName, AlertSeverity, CompromisedEntity, Description, Entities
| order by TimeGenerated desc
```

**KQL -- Correlated sign-in for the source account:**
```kql
let AlertTime = datetime(YYYY-MM-DD HH:MM:SS);
SigninLogs
| where TimeGenerated between ((AlertTime - 2h) .. (AlertTime + 2h))
| where UserPrincipalName == "user@domain.com"
| project TimeGenerated, IPAddress, Location, AuthenticationRequirement, ResultType
```

**Containment:**
- If confirmed malicious: isolate affected endpoint per Playbook 8.3 containment steps
- If confirmed authorized activity: document as false positive, update alert suppression rule with documented justification
- If unknown: treat as Medium severity, continue investigation before containment

**Long-Term Resolution:** The alert backlog must be triaged as part of the remediation plan. Each alert must be classified as: confirmed authorized activity (suppress with rule), confirmed malicious (full incident), or unknown (investigate and classify).

---

### Playbook 8.6: Unauthorized Data Access or Exfiltration

**Trigger:** DLP alert, large data download by a non-administrator account, unusual email forwarding rule, SecurityAlert for mass file access, or user report of missing data.

**Triage (SOC Analyst):**
1. Identify the account and asset involved
2. Determine what data was accessed and its classification
3. Determine whether data left the environment (exfiltration) or was only accessed internally
4. Classify as Critical if data exfiltration is confirmed or suspected

**KQL -- File access investigation:**
```kql
AuditLogs
| where OperationName has_any ("FileAccessed", "FileSyncDownloadedFull", "SendMessage")
| where InitiatedBy has "user@domain.com"
| project TimeGenerated, OperationName, TargetResources, Result
| order by TimeGenerated desc
```

**Containment:**
1. Disable the account immediately
2. Revoke all active sessions and tokens
3. Block external sharing if cloud storage services are involved
4. Preserve all logs before any further action

**Notification:** If data exfiltration is confirmed, Security Lead must assess regulatory notification obligations within 24 hours per Section 6.3.

---

## 9. KQL Detection Queries

### 9.1 MFA Coverage Check
```kql
SigninLogs
| summarize TotalSignIns = count(),
            MFACount = countif(AuthenticationRequirement == "multiFactorAuthentication"),
            SFACount = countif(AuthenticationRequirement == "singleFactorAuthentication")
    by bin(TimeGenerated, 1d)
| extend MFARate = round(100.0 * MFACount / TotalSignIns, 1)
| order by TimeGenerated desc
```

**Assessment baseline (real data):** MFA rate was 59% at time of assessment. 83,600 of 203,906 sign-ins completed on single-factor authentication.

### 9.2 Failed Sign-In Summary (Last 24 Hours)
```kql
SigninLogs
| where TimeGenerated > ago(24h)
| where ResultType != "0"
| summarize FailureCount = count() by UserPrincipalName, ResultDescription
| order by FailureCount desc
| take 50
```

**Assessment baseline (real data):** 30 accounts with more than 5 failures. Top account: 512 failures from external domain.

### 9.3 Privileged Role Assignment Changes (Last 7 Days)
```kql
AuditLogs
| where TimeGenerated > ago(7d)
| where Category == "RoleManagement"
| project TimeGenerated, OperationName, InitiatedBy, TargetResources, Result
| order by TimeGenerated desc
```

### 9.4 High Severity Security Alerts (Last 7 Days)
```kql
SecurityAlert
| where TimeGenerated > ago(7d)
| where AlertSeverity in ("High", "Critical")
| project TimeGenerated, AlertName, AlertSeverity, CompromisedEntity, Description
| order by TimeGenerated desc
```

**Assessment baseline (real data):** Top alert was PowerShell encoded command execution with 24,905 occurrences.

### 9.5 SecurityIncident Status Summary
```kql
SecurityIncident
| summarize Count = count() by Status, Severity
| order by Severity asc
```

### 9.6 New Accounts Created (Last 7 Days)
```kql
AuditLogs
| where TimeGenerated > ago(7d)
| where OperationName == "Add user"
| project TimeGenerated, OperationName, InitiatedBy, TargetResources, Result
| order by TimeGenerated desc
```

---

## 10. Escalation Matrix

| Severity | First Responder | Escalation Target | Notification Timeline |
|---|---|---|---|
| Critical (SEV-1) | SOC Analyst | Incident Commander + Security Lead | Within 15 minutes |
| High (SEV-2) | SOC Analyst | Incident Commander | Within 1 hour |
| Medium (SEV-3) | SOC Analyst | Security Lead (if unresolved in 4 hours) | Within 4 hours |
| Low (SEV-4) | SOC Analyst | Document and resolve independently | Within 24 hours |
| Informational | SOC Analyst | Weekly review | Weekly |

### Contact Reference

| Role | Primary Contact | Backup Contact | Out-of-Band Method |
|---|---|---|---|
| Security Lead | [Name -- to be completed] | [Name -- to be completed] | [Phone -- to be completed] |
| Incident Commander | [Appointed per incident] | [Name -- to be completed] | [Phone -- to be completed] |
| Identity Administrator | [Name -- to be completed] | [Name -- to be completed] | [Phone -- to be completed] |
| Cloud Administrator | [Name -- to be completed] | [Name -- to be completed] | [Phone -- to be completed] |
| SOC Analyst | [Name -- to be completed] | [Name -- to be completed] | [Phone -- to be completed] |

---

## 11. Incident Tracking and Reporting

### 11.1 Incident Record Requirements

Every incident must have a SIEM incident record containing:

- Unique incident ID
- Detection timestamp
- Initial severity classification and justification
- Affected entities (accounts, assets, data)
- Timeline of all response actions with timestamps and actors
- Containment, eradication, and recovery actions taken
- Post-incident review reference (for Critical and High)
- Final status (Closed) with closure timestamp and closure justification

### 11.2 Metrics and Reporting

| Metric | Target |
|---|---|
| Mean Time to Detect (MTTD) | Less than 24 hours for Critical/High |
| Mean Time to Contain (MTTC) | Less than 4 hours for Critical, 8 hours for High |
| Mean Time to Recover (MTTR) | Less than 24 hours for Critical, 72 hours for High |
| Post-Incident Reviews Completed on Time | 100% of Critical and High incidents |
| Open Incidents Greater Than 30 Days | Zero tolerance for Critical; fewer than 3 for High |

---

## 12. Plan Testing and Maintenance

### 12.1 Tabletop Exercises

A tabletop exercise must be conducted at minimum once per year. The exercise must:

- Simulate a realistic incident scenario based on actual alert types present in the environment
- Involve all members of the incident response team
- Walk through this plan step-by-step against the simulated scenario
- Identify gaps in procedures, roles, or tools
- Produce a written exercise report with action items

### 12.2 Plan Review and Update Triggers

This plan must be reviewed annually. It must also be reviewed and updated following:

- Any Critical or High severity incident (within 30 days of closure)
- A significant change to the technology environment
- A change in applicable regulatory requirements
- Identification of a material procedural gap through tabletop exercise

### 12.3 Version Control

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| 1.0 | March 5, 2026 | Alex Truong | Initial version -- produced as direct output of live environment gap assessment |

---

## 13. Related Documents and References

| Document | Status |
|---|---|
| Information Security Policy | Active (March 5, 2026) |
| Risk Register | Active (March 5, 2026) |
| Plan of Action and Milestones (POA&M) | Active -- POA-009 directly addresses this plan |
| System Security Plan | Pending |
| Privileged Access Management Standard | Pending |
| Vulnerability Management Policy | Pending |

**Frameworks Referenced:**
- NIST Special Publication 800-53 Revision 5 (IR-1 through IR-10)
- NIST Special Publication 800-61 Revision 2
- NIST Cybersecurity Framework 2.0 (RS -- Respond, RC -- Recover)
- ISO/IEC 27001:2022 (Annex A 5.24, 5.25, 5.26, 5.27, 5.28)
- SOC 2 Trust Services Criteria (CC7.3, CC7.4, CC7.5)

---

## 14. Approval and Sign-Off

| Role | Name | Signature | Date |
|---|---|---|---|
| Security Lead / Cybersecurity Owner | [To be completed] | [Signature required] | March 5, 2026 |
| GRC Analyst | Alex Truong | [Signature required] | March 5, 2026 |
| Incident Commander (designated) | [To be completed] | [Signature required] | March 5, 2026 |

**Plan Effective Date:** March 5, 2026
**Next Review Date:** March 5, 2027
**Version:** 1.0
**Classification:** CONFIDENTIAL

---

*End of Document*

*Organization: ABC Corp | Microsoft Entra ID Tenant. This Incident Response Plan was produced from a real gap assessment conducted against a live Azure cloud environment. All playbooks and KQL queries are grounded in actual alert types, telemetry sources, and infrastructure identified during that assessment. All organization names and identifiers have been sanitized. Published for portfolio and educational purposes only.*
