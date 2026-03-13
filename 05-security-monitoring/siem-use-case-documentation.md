# SIEM Use Case Documentation

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | Security Operations and Detection Engineering Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL

> ⚠️ **Disclaimer:** This is a sanitized version of a SIEM Use Case Documentation produced following a gap assessment conducted on a live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Real Environment Context:** This SIEM Use Case Documentation was produced from a real, live gap assessment of a Microsoft Azure shared tenant. Every use case in this registry addresses a specific detection scenario that was either completely unmonitored or entirely blind at time of assessment. The highest-priority use cases trace to confirmed active conditions in the live environment: active credential attack traffic, 83,600 SFA sign-ins with no detection, and zero endpoint telemetry for 126 assets. This is not a generic SIEM template. It was written for the actual threat surface of a real Azure environment.

**Confirmed Findings from the Live Environment (real data):**
- Zero analytics rules active in Microsoft Sentinel at time of assessment. No detection layer existed. (real finding)
- Active credential attack traffic confirmed: 512 failed sign-in attempts from a single external source in SignInLogs. (real finding)
- 83,600 of 203,906 sign-in events completed on single-factor authentication. No detection rule existed for this. (real finding)
- High and Critical severity alerts in SecurityAlert table with no evidence of triage or response. (real finding)
- Unactioned incidents in SecurityIncident table with no owner assigned, investigation started, or closure recorded. (real finding)
- ProtectionStatus table returned zero rows for all 126 assets. Endpoint telemetry entirely absent from monitoring platform. (real finding)
- 2 unsponsored guest accounts with no expiration date. (real finding)
- No Conditional Access policies enforced. CA portal returned HTTP 401 for all policy queries. (real finding)
- This document directly implements the detection response capability required by the Security Monitoring Policy v1.0 (POA-002, POA-004, POA-006).

---


**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | Security Operations and Detection Engineering Analyst
**Date:** March 5, 2026
**Version:** 1.0
**Classification:** CONFIDENTIAL
**Document Type:** SIEM Use Case Registry (operationalizes Security Monitoring Policy v1.0 and KQL Detection Rule Library v1.0)
**Review Cadence:** Quarterly, or following any significant threat intelligence update, incident post-mortem, or detection failure

> **Assessment Context:** This SIEM Use Case Documentation was produced following a read-only gap assessment of a live Azure shared tenant serving approximately 1,000 or more students. At time of the March 2026 assessment, the Microsoft Sentinel workspace had zero active analytics rules, unreviewed High and Critical alerts in the SecurityAlert table, unactioned incidents in the SecurityIncident table, and an empty ProtectionStatus table for all 126 assets. Every use case in this registry addresses a specific detection scenario that was either actively unmonitored or entirely blind at time of assessment. This is not a generic SIEM use case template. Each entry was written for the actual threat surface, telemetry, and risk profile of this live environment.

---

## Table of Contents

1. Document Purpose and Structure
2. Use Case Standard Format
3. Use Case Registry
   - UC-001: Privileged Account Credential Attack Detection
   - UC-002: Single-Factor Authentication Policy Bypass
   - UC-003: Break-Glass Account Activation
   - UC-004: Impossible Travel and Session Anomaly
   - UC-005: Legacy Authentication Protocol Bypass
   - UC-006: Privileged Role Assignment Abuse
   - UC-007: Service Principal Credential Persistence
   - UC-008: OAuth Consent Phishing Attack
   - UC-009: Endpoint Protection Telemetry Loss
   - UC-010: Active Malware or Threat Detection
   - UC-011: Lateral Movement via RDP Brute Force
   - UC-012: Insider Threat via Bulk Account Manipulation
   - UC-013: Azure Subscription Privilege Escalation
   - UC-014: Network Perimeter Exposure via NSG Modification
   - UC-015: Cloud Resource Destruction Attack
   - UC-016: Data Exfiltration via Storage Account Misconfiguration
   - UC-017: Monitoring Evasion via Log Source Disruption
   - UC-018: MFA Bypass via Authentication Method Tampering
4. Use Case Coverage Matrix
5. Use Case Lifecycle and Maintenance
6. Related Documents

---

## 1. Document Purpose and Structure

### 1.1 Purpose

A SIEM use case is a documented detection scenario that defines: what threat or risk the detection addresses, what data sources and signals are required to detect it, how the detection logic works at a conceptual level, how an analyst should respond when the use case triggers, and how success is measured.

The KQL Detection Rule Library (v1.0) provides the technical queries. This document provides the operational context that makes those queries meaningful. Together they answer: what are we detecting, why does it matter in this environment, who responds, and how do we know the detection is working.

This registry was built from the March 2026 gap assessment findings. Every use case listed here was either completely unmonitored at time of assessment or was monitored by alert generation alone with no defined response workflow. The gap was both technical (no analytics rules) and operational (no defined analyst response for any scenario).

### 1.2 How This Document Fits Into the Security Program

| Document | Layer | Purpose |
|---|---|---|
| Security Monitoring Policy v1.0 | Governance | Defines what must be monitored, alert SLAs, roles, incident requirements |
| SIEM Use Case Documentation v1.0 (this document) | Operational | Defines each detection scenario, its threat context, response workflow, and success criteria |
| KQL Detection Rule Library v1.0 | Technical | Provides the KQL queries that implement the detection logic in Sentinel |

### 1.3 Use Case Prioritization

Use cases are prioritized on a three-tier model based on the threat severity and the specific risk profile of the ABC Corp Azure cloud tenant:

- **Priority 1 (Critical):** Scenarios where a successful attack would result in full tenant compromise, data breach, or loss of security visibility. These use cases must have detection coverage before any other priority work begins.
- **Priority 2 (High):** Scenarios where a successful attack would result in significant privilege escalation, persistent access, or major control failure. These use cases must have detection coverage within 30 days of policy effective date.
- **Priority 3 (Medium):** Scenarios where a successful attack would result in a policy violation, limited unauthorized access, or minor control failure. These use cases must have detection coverage within 90 days of policy effective date.

---

## 2. Use Case Standard Format

Each use case entry includes the following sections:

| Field | Description |
|---|---|
| Use Case ID | Unique identifier in format UC-NNN |
| Use Case Name | Descriptive name of the detection scenario |
| Priority | Critical, High, or Medium |
| Threat Category | MITRE ATT&CK tactic category |
| Threat Description | What the attacker is doing and why it is a threat to this environment |
| Environment Context | Why this specific threat matters in the ABC Corp Azure cloud tenant environment |
| Detection Approach | How the detection logic identifies this threat at a conceptual level |
| Data Sources Required | Which log tables must be available for the detection to function |
| Implementing Rules | KQL Detection Rule Library rule IDs that implement this use case |
| Alert Trigger Condition | What specific condition causes the detection to fire |
| False Positive Scenarios | Known benign activities that may trigger this use case |
| True Positive Indicators | Signals that confirm this is a real threat, not a false positive |
| Analyst Response Workflow | Step-by-step analyst actions when the use case triggers |
| Escalation Path | Who gets notified at each escalation level |
| Containment Actions | Immediate steps to limit damage if confirmed |
| Success Criteria | How we know this use case is working correctly |
| Current Status | Active, Pending Data Source, or In Development |
| Assessment Note | Any specific finding from the March 2026 assessment related to this use case |

---

## 3. Use Case Registry

---

### UC-001: Privileged Account Credential Attack Detection

| Field | Value |
|---|---|
| Use Case ID | UC-001 |
| Use Case Name | Privileged Account Credential Attack Detection |
| Priority | Priority 1 (Critical) |
| Threat Category | Credential Access |
| Implementing Rules | ID-003 (Failed Sign-In Threshold Breach) |
| Current Status | Active |

**Threat Description:**

An attacker targeting the ABC Corp cloud environment attempts to gain access by systematically guessing or spraying passwords against user accounts. Two primary attack patterns exist: single-account brute force (repeatedly attempting passwords against one account until lockout or success) and password spray (attempting one common password against many accounts to avoid triggering per-account lockout thresholds). Password spray is the preferred technique for targeting cloud environments because it evades most per-account lockout controls.

**Environment Context:**

The shared Azure tenant has approximately 1,000 or more student accounts with externally accessible sign-in endpoints (Entra ID). These accounts represent a substantial attack surface for credential attacks, particularly since many students may use predictable passwords. At time of the March 2026 assessment, the top failed sign-in source was [redacted external domain] with 512 failed attempts from a single account. This confirmed that credential attack activity was already occurring against the live environment with no detection response.

**Detection Approach:**

The detection monitors SignInLogs for two patterns. Pattern 1: five or more failed sign-in attempts against the same account within a one-hour window, indicating a targeted brute force attack. Pattern 2: failed sign-in attempts against ten or more distinct accounts originating from the same IP address within a one-hour window, indicating a password spray. Both patterns are detected in a single KQL query using parallel summarize branches that union their results.

**Data Sources Required:**
- SignInLogs (Entra ID connector - required and confirmed present at time of assessment)

**Alert Trigger Condition:**
- Single account: five or more failed sign-in attempts (ResultType not equal to "0") against the same UserPrincipalName within one hour, excluding MFA prompt result codes (50074, 50076, 50079, 70044)
- Spray pattern: one or more failed sign-in attempts each against ten or more distinct accounts from the same source IP within one hour

**False Positive Scenarios:**
- A user who has forgotten their password and is attempting multiple times before resetting
- An automated integration or script using stale credentials against a service account
- A developer testing authentication flows against multiple test accounts

**True Positive Indicators:**
- Source IP resolves to a known threat actor infrastructure, Tor exit node, or anonymizing proxy
- Targeted accounts include privileged accounts (adm- prefix per PAM Standard)
- A successful sign-in from the attacking IP follows the failure pattern
- Multiple distinct source IPs attacking the same accounts simultaneously (distributed spray)
- The failure pattern follows a time schedule consistent with automated tooling (failures at regular intervals)

**Analyst Response Workflow:**

Step 1: Review the alert in Sentinel. Identify the attack pattern (brute force or spray), the source IP(s), and the targeted accounts.

Step 2: Check whether any targeted account had a successful sign-in during or after the failure window. Query: SigninLogs filtered by UserPrincipalName from the alert and ResultType == "0" for the same time window.

Step 3: If a successful sign-in followed the failures, escalate to High-risk account compromise workflow immediately. This is now a Severity 2 incident minimum.

Step 4: If no success, determine whether the source IP is known malicious. Check the IP against Microsoft Threat Intelligence in Sentinel > Threat Intelligence.

Step 5: If the IP is confirmed malicious or the attack is ongoing, engage the Identity Administrator to create a Named Location block in Conditional Access for the offending IP range.

Step 6: For targeted privileged accounts (adm- prefix), notify the Security Lead regardless of whether an account was successfully compromised.

Step 7: Document the incident disposition (true positive with containment, or false positive with reason) and close the incident.

**Escalation Path:**
- Initial triage: Security Operations Analyst
- Confirmed true positive, no success: Security Operations Analyst closes with documentation
- Confirmed true positive, successful sign-in found: Escalate to Security Lead within 30 minutes
- Privileged account targeted: Notify Security Lead within 1 hour regardless of outcome

**Containment Actions (if account compromised):**
1. Revoke all active sessions for the compromised account: Entra ID > Users > [Account] > Revoke sessions
2. Reset the account password via admin: Entra ID > Users > [Account] > Reset password
3. Confirm MFA methods are intact (not removed): Entra ID > Users > [Account] > Authentication methods
4. Block sign-in on the account while investigation proceeds: Entra ID > Users > [Account] > Block sign-in
5. Block the source IP via Conditional Access Named Location if ongoing

**Success Criteria:**
- Detection fires within 15 minutes of the threshold being reached (one-hour lookback run every 15 minutes)
- Zero successful sign-ins from attacking IP after Named Location block is applied
- No escalation-worthy events missed during analyst shift (verified by CP-005 daily backlog query)
- False positive rate below 20 percent for this use case (tracked in monthly SOC report)

**Assessment Note:** The March 2026 assessment confirmed 512 failed sign-in attempts from a single source ([redacted external domain]) in the SignInLogs table. This attack was occurring in the live environment with no detection response. UC-001 directly addresses this confirmed active threat.

---

### UC-002: Single-Factor Authentication Policy Bypass

| Field | Value |
|---|---|
| Use Case ID | UC-002 |
| Use Case Name | Single-Factor Authentication Policy Bypass |
| Priority | Priority 1 (Critical) |
| Threat Category | Defense Evasion, Initial Access |
| Implementing Rules | ID-002 (SFA Success), ID-005 (Legacy Authentication Sign-In Success) |
| Current Status | Active |

**Threat Description:**

An attacker who has obtained valid credentials for an account in the tenant attempts to authenticate by exploiting a gap in Conditional Access policy enforcement, either by using a legacy authentication protocol that cannot enforce MFA (IMAP, POP3, SMTP AUTH, Exchange ActiveSync, basic auth) or by exploiting an account exclusion from a CA policy. If the attacker successfully signs in using only a password (SFA), they bypass the primary compensating control for credential theft and gain full account access.

**Environment Context:**

At time of the March 2026 assessment, 83,600 of 203,906 sign-in events (approximately 41 percent) completed on single-factor authentication. This means that for over 40 percent of sign-in events, MFA was either not required or was bypassed. Any account signing in on SFA is a credential-theft target with no MFA barrier. This is the single most impactful detection gap in the environment at time of assessment.

**Detection Approach:**

The detection monitors SignInLogs for successful sign-in events where AuthenticationRequirement equals "singleFactorAuthentication". After Conditional Access policies are fully deployed, this should produce zero results. Any SFA success is either a policy misconfiguration, an unintentional exclusion, or an attack. A companion detection specifically monitors for successful sign-ins using legacy authentication protocols (ClientAppUsed in the legacy protocol list) which represent a distinct bypass vector.

**Data Sources Required:**
- SignInLogs (Entra ID connector - required and confirmed present)

**Alert Trigger Condition:**
- Rule ID-002: any sign-in with ResultType equal to "0" and AuthenticationRequirement equal to "singleFactorAuthentication" within the last 15 minutes
- Rule ID-005: any sign-in with ResultType equal to "0" and ClientAppUsed in the legacy protocol list within the last 15 minutes

**False Positive Scenarios:**
- During the Conditional Access deployment phase (pre-policy), SFA sign-ins are expected and the rule should be used in monitor mode rather than alert mode until CA policies are fully deployed
- Service accounts using legacy protocols for legitimate automation that cannot be migrated to modern auth (these should be documented as formal exceptions with compensating controls)
- Break-glass accounts if not yet enrolled in FIDO2 (should be resolved immediately)

**True Positive Indicators:**
- SFA success on a privileged account (adm- prefix)
- SFA success from an IP address that previously generated failed sign-in alerts (attacker used stolen credentials)
- SFA success at an unusual time for the account
- SFA success via a legacy protocol on an account that was previously using modern auth only
- Multiple SFA successes from the same IP against different accounts (systematic exploitation)

**Analyst Response Workflow:**

Step 1: Identify the user, application, and authentication method that resulted in SFA success.

Step 2: Determine why CA policy did not enforce MFA. Check in Entra ID > Sign-in logs > [specific event] > Conditional Access tab. The CA evaluation details will show which policies evaluated, which applied, and why MFA was not required.

Step 3: Determine the root cause: is the user excluded from the CA policy? Did the sign-in use a legacy protocol that bypasses CA? Is there a CA policy gap (no policy applies to this app)?

Step 4: If root cause is a policy gap or unintentional exclusion: create a change request for the Identity Administrator to correct the CA policy. Severity: High if affecting privileged accounts, Medium if affecting standard accounts.

Step 5: If root cause is a legacy protocol sign-in: assess whether the service legitimately requires legacy auth. If not: block via CA. If yes: document as exception per Security Monitoring Policy Section 13.

Step 6: If the SFA sign-in appears to be from an attacker using stolen credentials (suspicious IP, unusual time, privilege escalation activity following sign-in): treat as account compromise, escalate to Security Lead.

**Escalation Path:**
- SFA on standard account, policy gap root cause: Identity Administrator to fix within 24 hours
- SFA on privileged account (adm-): Security Lead notification within 1 hour, treat as Severity 2 minimum
- SFA from suspicious IP with subsequent privileged activity: Severity 1 incident, immediate Security Lead escalation

**Containment Actions (if confirmed bypass attack):**
1. Block sign-in on the affected account
2. Revoke all active sessions
3. Identify and remediate the CA policy gap immediately
4. Review all actions taken during the SFA session via AuditLogs filtered by CorrelationId

**Success Criteria:**
- Post-CA deployment: zero SFA success alerts (monitored by CP-001 weekly query)
- SFA percentage trend: confirmed downward week-over-week following CA deployment
- All SFA alerts triaged within 4 hours (High severity SLA from Security Monitoring Policy Section 7.1)
- CA policy gap identified and remediated within 24 hours of detection

**Assessment Note:** 83,600 SFA successes from 203,906 total sign-ins confirmed in the live environment at time of assessment. This represents the highest-volume control gap in the entire Cyber Range security program. UC-002 is the primary detection for this confirmed active gap.

---

### UC-003: Break-Glass Account Activation

| Field | Value |
|---|---|
| Use Case ID | UC-003 |
| Use Case Name | Break-Glass Account Activation |
| Priority | Priority 1 (Critical) |
| Threat Category | Privilege Escalation, Initial Access |
| Implementing Rules | ID-001 (Break-Glass Account Sign-In) |
| Current Status | Active |

**Threat Description:**

Break-glass accounts are emergency Global Administrator accounts that exist outside the normal PIM-governed access workflow. They are the highest-privilege accounts in the tenant and are intentionally excluded from many Conditional Access policies (to function when CA policies malfunction). An attacker who discovers or compromises a break-glass credential has immediate, unrestricted Global Administrator access to the entire tenant, including the ability to disable all security controls, exfiltrate all data, and establish persistent backdoors. Simultaneously, any legitimate use of break-glass accounts is an operational event that must be investigated and documented.

**Environment Context:**

The ABC Corp cloud environment requires break-glass accounts to be stored per the PAM Standard (FIDO2 hardware keys in physically secured storage, with credentials sealed in tamper-evident envelopes). Any sign-in activity from these accounts outside a declared emergency is a potential compromise of the most sensitive credentials in the tenant.

**Detection Approach:**

The detection monitors SignInLogs for any sign-in event (successful or failed) where the UserPrincipalName starts with "bg-" or contains "breakglass" or "break-glass". The rule runs every five minutes with a five-minute lookback to achieve near-real-time detection. Any result from this query fires a Critical alert and creates a Sentinel incident immediately. There is no threshold. One event equals one alert.

**Data Sources Required:**
- SignInLogs (Entra ID connector)

**Alert Trigger Condition:**
- Any sign-in event (any ResultType) for any account matching the break-glass naming convention in the last five minutes

**False Positive Scenarios:**
- Authorized emergency use of break-glass accounts during a declared incident (Security Lead has authorized use and the analyst can confirm this via direct communication)
- Connectivity testing by the Cloud Administrator (should never occur; break-glass accounts should never be used for testing)

**True Positive Indicators (unauthorized use):**
- No active declared incident at the time of the sign-in
- Sign-in initiated from an IP that is not within expected administrative IP ranges
- Security Lead was not notified before the sign-in
- Sign-in followed by administrative actions (role assignments, policy changes, resource deletions)
- Sign-in occurred outside normal business hours with no declared emergency

**Analyst Response Workflow:**

Step 1: Page the Security Lead immediately upon receiving this alert. Do not wait for business hours. Critical alert, one-hour SLA per Security Monitoring Policy Section 7.1. This is a zero-tolerance event.

Step 2: Contact the Security Lead and the individual responsible for break-glass account custody to confirm whether the sign-in was authorized.

Step 3: If authorized: document the authorization, the reason for use, the duration of the session, and all actions taken during the session. Close the incident as Benign True Positive with documentation.

Step 4: If authorization cannot be confirmed within 30 minutes: treat as active compromise. Escalate to Severity 1 incident.

Step 5: For unconfirmed or unauthorized use: immediately disable the break-glass account in Entra ID. Notify the Security Lead. Retrieve physical FIDO2 keys from storage and confirm physical security has not been breached.

Step 6: Review all AuditLogs activity initiated by the break-glass account during the session. Identify any changes made (role assignments, policy modifications, user creation, resource changes).

Step 7: If unauthorized changes were made: begin full incident response. Treat as a full tenant compromise scenario until proven otherwise.

**Escalation Path:**
- Any alert: Security Lead paged immediately, no exceptions
- Cannot confirm authorization within 30 minutes: Severity 1 incident declared
- Unauthorized changes confirmed: All hands incident response

**Containment Actions (unauthorized use confirmed):**
1. Disable the break-glass account in Entra ID immediately
2. Revoke all active sessions for the account
3. Engage Cloud Administrator to audit all changes made during the session and reverse unauthorized changes
4. Rotate the FIDO2 hardware key by provisioning new credentials
5. Review all CA policies and role assignments modified during the session
6. Check whether any new backdoor accounts or service principals were created

**Success Criteria:**
- Alert fires within five minutes of any break-glass sign-in event
- Security Lead is notified within 15 minutes of every alert (not just ones that appear suspicious)
- Every break-glass activation has a documented authorization record
- Post-incident review completed within 48 hours for any authorized emergency use

**Assessment Note:** No monitoring existed for break-glass account usage at time of the March 2026 assessment. The PAM Standard defines break-glass accounts as the highest-privilege accounts in the tenant. Their usage must be monitored at the highest detection priority.

---

### UC-004: Impossible Travel and Session Anomaly

| Field | Value |
|---|---|
| Use Case ID | UC-004 |
| Use Case Name | Impossible Travel and Session Anomaly |
| Priority | Priority 2 (High) |
| Threat Category | Initial Access |
| Implementing Rules | ID-004 (Impossible Travel Detection), ID-009 (High-Risk User Detection) |
| Current Status | Active |

**Threat Description:**

After an attacker obtains valid credentials for an account (through phishing, credential spray, or purchase from a criminal marketplace), they sign in from their own location. If the legitimate user also signs in around the same time, two successful sign-ins will occur from geographically distant locations within a time window that makes physical travel impossible. This is one of the highest-confidence indicators of account compromise available in Entra ID telemetry.

**Environment Context:**

The student population is geographically distributed, so some international sign-in activity is expected. However, sign-ins from two different countries within a 60-minute window that do not use a known VPN exit node are strong indicators of compromise. Entra ID Identity Protection also generates its own risk signals for impossible travel, which are surfaced via the AADRiskyUsers table.

**Detection Approach:**

The KQL detection groups successful sign-in events by user and 60-minute time bins, then identifies bins where multiple distinct countries appear. This supplements (but does not replace) Entra ID Identity Protection's built-in impossible travel detection. The ID-009 rule separately monitors the AADRiskyUsers table for high-risk user designations, which include Entra ID's own impossible travel signal and additional AI-driven risk assessments.

**Data Sources Required:**
- SignInLogs (Entra ID connector)
- AADRiskyUsers (Entra ID Identity Protection)

**Alert Trigger Condition:**
- Two or more successful sign-ins from different countries for the same account within a 60-minute time window
- OR: a user account is elevated to High risk state in Entra ID Identity Protection

**False Positive Scenarios:**
- User is connected to a corporate or personal VPN that routes through a different country
- User is traveling and signed in from two devices (one using mobile data, one using hotel WiFi from different countries)
- Cloud browser or cloud PC service routing sign-ins through different geographic regions

**True Positive Indicators:**
- One of the sign-in locations is associated with known threat actor infrastructure or a Tor exit node
- Sign-in from the anomalous location was followed by privilege escalation, data access, or administrative actions
- User is unreachable and cannot confirm both sign-ins as legitimate
- Entra Identity Protection has independently elevated the account to High risk

**Analyst Response Workflow:**

Step 1: Identify the two sign-in locations, times, and applications.

Step 2: Attempt to contact the account owner to confirm whether both sign-ins are legitimate. Provide the specific locations and times.

Step 3: While awaiting user response, check the sign-in from the unexpected location for subsequent activity (AuditLogs filtered by CorrelationId of the suspicious sign-in).

Step 4: If the user confirms both sign-ins: ask them to confirm the unexpected location (VPN, travel). Document and close as false positive with explanation.

Step 5: If the user denies one of the sign-ins OR is unreachable within two hours: treat as account compromise. Escalate to Security Lead.

Step 6: For confirmed or suspected compromise: initiate containment (revoke sessions, block sign-in, reset password). Review all actions taken during the suspicious session.

**Escalation Path:**
- User confirms both sign-ins: analyst closes, no escalation
- User denies one sign-in: Security Lead notified within 30 minutes, Severity 2 incident
- Privileged account involved: Security Lead notified immediately regardless of outcome

**Containment Actions:**
1. Revoke all active sessions: Entra ID > Users > [Account] > Revoke sessions
2. Block sign-in while investigation is active
3. Reset password
4. Confirm MFA methods were not changed during the suspicious session
5. Confirm in Entra Identity Protection: mark user as Compromised to trigger automated remediation policy

**Success Criteria:**
- Alert fires within 30 minutes of the anomalous sign-in event (rule runs every 30 minutes)
- User contact attempted within 1 hour of alert
- All compromised account incidents from this use case result in session revocation within 2 hours of confirmation
- False positive rate tracked and used to tune country exclusion watchlist

---

### UC-005: Legacy Authentication Protocol Bypass

| Field | Value |
|---|---|
| Use Case ID | UC-005 |
| Use Case Name | Legacy Authentication Protocol Bypass |
| Priority | Priority 2 (High) |
| Threat Category | Defense Evasion, Initial Access |
| Implementing Rules | ID-005 (Legacy Authentication Sign-In Success) |
| Current Status | Active |

**Threat Description:**

Legacy authentication protocols (IMAP, POP3, SMTP AUTH, Exchange ActiveSync, basic auth) were designed before multi-factor authentication existed. These protocols send credentials directly to the server and have no mechanism for MFA challenge-response. A Conditional Access policy that does not explicitly block legacy authentication allows attackers to authenticate using only a username and password, even when a "Require MFA" CA policy is active. This is the primary vector through which attackers bypass MFA in Microsoft 365 and Entra ID environments.

**Environment Context:**

At time of the March 2026 assessment, no Conditional Access policy was active (zero CA policies enforced, confirmed by HTTP 401 from the CA portal). This means all sign-ins, including legacy protocol sign-ins, were completing without CA enforcement. Legacy protocol blocking must be implemented as a CA policy priority as part of POA-003 remediation, and this use case provides the ongoing detection to confirm that blocking is effective after deployment.

**Detection Approach:**

The detection monitors SignInLogs for successful sign-ins (ResultType "0") where ClientAppUsed matches the list of known legacy authentication protocol names. The list includes Exchange ActiveSync, IMAP4, POP3, SMTP, Authenticated SMTP, Outlook Anywhere (RPC over HTTP), and similar protocols that bypass CA evaluation.

**Data Sources Required:**
- SignInLogs (Entra ID connector)

**Alert Trigger Condition:**
- Successful sign-in where ClientAppUsed is in the legacy protocol list within the last 15 minutes

**False Positive Scenarios:**
- Legitimate applications that have not yet been migrated to modern authentication (MSAL, OAuth 2.0) and have a documented exception
- Scanning or monitoring tools that use basic auth for health checks
- Shared mailboxes being accessed by third-party systems via SMTP

**True Positive Indicators:**
- Legacy auth sign-in from an account that was previously using modern auth exclusively
- Legacy auth sign-in from an IP that was previously involved in credential attack activity (correlated with UC-001 alerts)
- Legacy auth sign-in on a privileged account (adm- prefix) - should never occur for privileged accounts
- Legacy auth sign-in immediately followed by email access, data download, or forwarding rule creation

**Analyst Response Workflow:**

Step 1: Identify the specific protocol, application, and account involved.

Step 2: Determine whether this account or application is on the legacy auth exception list.

Step 3: If not on the exception list: this is a CA policy gap. Identify why the legacy auth blocking CA policy did not prevent this sign-in (account excluded? Policy not covering this application?).

Step 4: Escalate to Identity Administrator for CA policy correction if a gap is identified.

Step 5: Review whether any sensitive actions were taken during the legacy auth session.

Step 6: If the account had no known legitimate use of legacy auth: treat as potential unauthorized access, review all session activity.

**Success Criteria:**
- Zero legacy auth sign-in successes on privileged accounts at all times
- Zero legacy auth sign-in successes from accounts not on the approved exception list after CA deployment
- All legacy auth alerts triaged and root cause identified within 4 hours

---

### UC-006: Privileged Role Assignment Abuse

| Field | Value |
|---|---|
| Use Case ID | UC-006 |
| Use Case Name | Privileged Role Assignment Abuse |
| Priority | Priority 1 (Critical) |
| Threat Category | Privilege Escalation |
| Implementing Rules | ID-006 (Privileged Role Assignment Outside PIM), PA-001 (Subscription Owner Assignment), PA-002 (New Global Administrator Assignment), PA-004 (Service Principal Privileged Role Assignment) |
| Current Status | Active |

**Threat Description:**

An attacker who has compromised an account with role management permissions (Privileged Role Administrator, Global Administrator) attempts to elevate the compromised account or a new backdoor account to Global Administrator or other privileged directory roles. This grants persistent high-privilege access that survives a password reset on the original compromised account. Similarly, a malicious insider with Privileged Role Administrator access can grant themselves or a confederate elevated access outside of the normal PIM-governed workflow to avoid detection.

**Environment Context:**

The PAM Standard requires all privileged role assignments to be delivered through PIM (just-in-time, time-limited, approval-required). Any direct permanent assignment to a privileged role outside PIM is a policy violation regardless of whether an attack is occurring. At time of the March 2026 assessment, 100 percent of user accounts had blank Department, Job Title, and Manager attributes, making it difficult to identify whether a newly assigned privileged account belongs to a legitimate employee without additional investigation.

**Detection Approach:**

Four separate detection rules cover the different vectors of privileged role assignment abuse. ID-006 catches direct (non-PIM) assignments to any of the twelve most sensitive directory roles. PA-001 catches Subscription Owner assignments via AzureActivity (which is a separate telemetry source from AuditLogs). PA-002 specifically monitors for any Global Administrator additions (the highest-sensitivity role). PA-004 monitors for service principals being assigned privileged directory roles, which is explicitly prohibited by the PAM Standard.

**Data Sources Required:**
- AuditLogs (Entra ID connector)
- AzureActivity (Azure Activity connector)

**Alert Trigger Condition:**
- Any addition of a user, service principal, or group to a privileged directory role outside of PIM activation within the last five minutes
- Any Azure subscription Owner role assignment within the last five minutes

**False Positive Scenarios:**
- An authorized break-glass account provisioning event (if a new break-glass account is being created, the direct Global Administrator assignment is expected and must be pre-authorized)
- A PIM configuration change that the Detection Engineer misidentified as a direct assignment

**True Positive Indicators:**
- The initiating account was not a designated Identity Administrator
- The target account was created within the last 24 hours (backdoor account pattern)
- The target account has no matching employee record or attribute data
- The assignment occurred outside business hours
- The assignment is not documented in the change management record

**Analyst Response Workflow:**

Step 1: Identify the role assigned, the target account, and the initiating account.

Step 2: Verify with the Security Lead whether this assignment was authorized. Do not rely solely on the initiating account's explanation without Security Lead confirmation.

Step 3: If the assignment was unauthorized: treat as active privilege escalation attack. Declare Severity 1 incident if it involves Global Administrator or Subscription Owner. Declare Severity 2 for other privileged roles.

Step 4: Remove the unauthorized role assignment immediately: Entra ID > Roles > [Role] > [Account] > Remove assignment.

Step 5: Investigate the initiating account for signs of compromise (recent sign-in anomalies, other unauthorized changes).

Step 6: If the initiating account is compromised: revoke all sessions, reset credentials, review all actions since compromise.

**Escalation Path:**
- Any privileged role assignment outside PIM: Security Lead notification within 30 minutes
- Global Administrator or Subscription Owner assignment: Severity 1, immediate Security Lead escalation
- Initiating account appears compromised: All hands incident response

**Containment Actions:**
1. Remove the unauthorized role assignment immediately
2. Revoke all sessions for both the target and initiating accounts
3. Conduct full review of all actions performed by the newly assigned privileged account
4. Audit all CA policies to ensure no backdoor exclusions were added

**Success Criteria:**
- No unauthorized privileged role assignments persist for more than 15 minutes without detection
- All privileged role assignment alerts triaged within 1 hour (Critical severity SLA)
- GA count never exceeds 2 (verified by CP-004 monthly query)

**Assessment Note:** No monitoring existed for privileged role assignments at time of the March 2026 assessment.

---

### UC-007: Service Principal Credential Persistence

| Field | Value |
|---|---|
| Use Case ID | UC-007 |
| Use Case Name | Service Principal Credential Persistence |
| Priority | Priority 2 (High) |
| Threat Category | Persistence, Credential Access |
| Implementing Rules | AP-003 (Service Principal New Credential Created) |
| Current Status | Active |

**Threat Description:**

An attacker who has compromised an account with application management permissions adds a new client secret or certificate to an existing service principal. This gives the attacker a persistent credential that survives the incident response actions (password reset, session revocation) applied to the compromised user account. The attacker can then continue to authenticate as the service principal with its full permissions, which may include access to Microsoft Graph, SharePoint, email, or other high-value resources.

**Environment Context:**

Service principal credential management in the ABC Corp cloud environment is governed by the Configuration Baseline Standard (AP-005: credentials must be stored in Azure Key Vault; AP-002: secrets must have expiration dates). Any new credential added to a service principal must be reviewed to confirm it was created by an authorized owner and is being stored appropriately.

**Detection Approach:**

The detection monitors AuditLogs for operations named "Add service principal credentials" or similar, which are generated whenever a new secret or certificate is added to an app registration or service principal. Every credential addition event is reviewed regardless of who initiated it.

**Data Sources Required:**
- AuditLogs (Entra ID connector)

**Alert Trigger Condition:**
- Any successful service principal credential addition event in AuditLogs within the last 15 minutes

**False Positive Scenarios:**
- Authorized credential rotation by the application owner (expected and common)
- Automated secret rotation via Azure Key Vault rotation policy
- DevOps pipeline adding deployment credentials during authorized CI/CD workflows

**True Positive Indicators:**
- The initiating account is not the registered owner of the application
- The new credential does not have an expiration date (required by CBS AP-002)
- The credential was added outside normal business hours without a change request
- The credential was added immediately after an account compromise alert for the initiating account
- The service principal has high-privilege Graph API permissions (e.g., Mail.Send, Directory.ReadWrite.All)

**Analyst Response Workflow:**

Step 1: Identify the service principal that received the new credential and the account that added it.

Step 2: Confirm whether the account that added the credential is the registered application owner.

Step 3: Verify whether there is a change request or documented reason for the credential addition.

Step 4: Check the new credential for compliance: does it have an expiration date? Is the secret being stored in Key Vault?

Step 5: If the credential addition appears unauthorized: rotate all existing credentials for the service principal immediately (revoke the new credential and all existing ones, then re-provision only the credentials that the legitimate application requires).

Step 6: Audit the service principal's recent access logs to determine whether the attacker has already used the new credential.

**Escalation Path:**
- Unauthorized addition by non-owner: Security Lead notification within 1 hour
- High-privilege service principal targeted: escalate to Severity 2 immediately

**Containment Actions:**
1. Revoke all credentials for the affected service principal
2. Re-provision only the credentials the legitimate application requires
3. Notify all owners of the affected application
4. Audit access logs for the service principal for the period from credential addition to revocation

**Success Criteria:**
- All service principal credential additions reviewed within 4 hours
- Zero unauthorized credentials persist on any service principal for more than 4 hours
- All service principal credentials have expiration dates (CBS AP-002 compliance, verified quarterly)

---

### UC-008: OAuth Consent Phishing Attack

| Field | Value |
|---|---|
| Use Case ID | UC-008 |
| Use Case Name | OAuth Consent Phishing Attack |
| Priority | Priority 2 (High) |
| Threat Category | Collection, Initial Access |
| Implementing Rules | AP-001 (High-Privilege Permission Grant), AP-004 (OAuth Consent to Unverified Publisher) |
| Current Status | Active |

**Threat Description:**

An attacker registers a malicious application in a different Azure AD tenant (which they control) and sends a phishing link to users that, when clicked, presents an OAuth consent prompt. If the user consents, the attacker's application receives delegated permissions to the user's account (email access, file access, Teams messages, calendar). Unlike credential phishing, the attacker never sees the user's password. They receive an OAuth token that grants persistent access to the account's data even if the user later changes their password.

**Environment Context:**

The Cyber Range Configuration Baseline Standard requires user consent to be disabled (OA-001) and admin consent workflow to be enabled (OA-003). If these controls are not implemented, student accounts are fully exposed to OAuth phishing. The detection covers two scenarios: a user granting consent (which should not be possible if CBS OA-001 is enforced) and an admin granting consent for high-privilege permissions (which requires review even when legitimate).

**Detection Approach:**

Two rules cover this use case. AP-001 monitors AuditLogs for "Consent to application" and "Add app role assignment" operations where the permissions granted include high-privilege Graph API scopes (Directory.ReadWrite.All, User.ReadWrite.All, Mail.Send, etc.). AP-004 specifically looks for consent grants where the publisher verification status indicates the app is from an unverified publisher.

**Data Sources Required:**
- AuditLogs (Entra ID connector)

**Alert Trigger Condition:**
- Any consent grant for an application requesting high-privilege permissions
- Any consent grant for an application from an unverified publisher

**False Positive Scenarios:**
- Authorized admin consent for a legitimate third-party integration (e.g., a security tool requiring Directory.ReadWrite.All)
- Internal application receiving Graph API permissions as part of an authorized deployment

**True Positive Indicators:**
- Consent was granted by a user account (not an administrator) - this means CBS OA-001 is not enforced
- The application is from an external tenant with no prior history
- The application name is designed to impersonate a legitimate Microsoft or enterprise application
- High-privilege permissions (Mail.Send, Files.ReadWrite.All) were granted to an application with no business justification

**Analyst Response Workflow:**

Step 1: Identify the application that received consent, the permissions granted, and the account that initiated the consent.

Step 2: If the consenting account is a user (not an admin): this is an immediate policy violation (CBS OA-001 should prevent user consent). Escalate to Identity Administrator to investigate how user consent occurred.

Step 3: Review the application: check the publisher tenant, the application name, and the permissions requested. Compare against any known legitimate integrations in the environment.

Step 4: If the application appears malicious: revoke the consent grant immediately. Entra ID > Enterprise Applications > [Application] > Permissions > Admin Consent > Revoke.

Step 5: Determine whether the attacker accessed any resources during the period between consent grant and revocation. Review audit logs for the application's service principal for any Graph API calls.

Step 6: Notify any users who consented to the malicious application. Advise them to review their account for unauthorized changes.

**Containment Actions:**
1. Revoke the consent grant for the malicious application
2. Delete the enterprise application from the tenant if it is entirely malicious
3. Review access logs for the period from consent to revocation
4. Disable user consent permanently via Entra ID > Enterprise Apps > User consent settings

**Success Criteria:**
- All consent grant alerts reviewed within 4 hours
- Zero user consent grants (should be impossible if CBS OA-001 is enforced)
- All admin consent grants reviewed and documented within 24 hours of grant

---

### UC-009: Endpoint Protection Telemetry Loss

| Field | Value |
|---|---|
| Use Case ID | UC-009 |
| Use Case Name | Endpoint Protection Telemetry Loss |
| Priority | Priority 1 (Critical) |
| Threat Category | Defense Evasion |
| Implementing Rules | EP-001 (Endpoint Protection Non-Reporting Asset), EP-002 (ProtectionStatus Not Protected), MI-001 (Heartbeat Gap), MI-002 (Connector Ingestion Gap) |
| Current Status | Partially Active (EP-001 and EP-002 pending POA-004 remediation) |

**Threat Description:**

An attacker who has gained access to a system may attempt to disable or tamper with the endpoint protection agent (Microsoft Defender for Endpoint) to prevent detection of their subsequent activities (malware execution, lateral movement, data staging). Loss of endpoint telemetry can also result from non-malicious causes (agent failure, network disruption, VM shutdown) but must be treated as a potential defense evasion event until confirmed otherwise. An environment where endpoint telemetry is absent is an environment where attackers can operate with significantly reduced detection risk.

**Environment Context:**

At time of the March 2026 assessment, the ProtectionStatus table in Microsoft Sentinel returned zero rows for all 126 assets under Tenable management. This means endpoint protection telemetry was not flowing into the monitoring platform for the entire asset inventory. This confirmed state is the exact scenario this use case detects. UC-009 covers both the individual-asset-level detection (a specific asset stops reporting) and the connector-level detection (the entire ProtectionStatus telemetry pipeline goes dark).

**Detection Approach:**

Four rules cover this use case across different detection layers. EP-001 identifies specific assets that have not reported ProtectionStatus within 24 hours (individual asset silence). EP-002 identifies assets that are reporting but show a non-Protected state or active threat. MI-001 detects the Log Analytics agent heartbeat going silent for any monitored computer. MI-002 detects the Sentinel data connector itself ceasing to ingest data (indicating a pipeline failure, not just individual asset silence).

**Data Sources Required:**
- ProtectionStatus (Microsoft Defender for Endpoint connector -- CURRENTLY ABSENT, POA-004 required)
- Heartbeat (Log Analytics agent)
- AuditLogs, SignInLogs, SecurityAlert (for MI-002 connector health check)

**Alert Trigger Condition:**
- EP-001: any asset known to Sentinel that has not reported ProtectionStatus in the last 24 hours
- EP-002: any asset reporting a ProtectionState other than "Protected" or ThreatStatusDetails indicating active threat
- MI-001: any computer whose Heartbeat has not been received in the last 2 hours
- MI-002: any critical data table (AuditLogs, SignInLogs, SecurityAlert) with no new data in the last 2 hours

**False Positive Scenarios:**
- Asset intentionally powered off for maintenance or decommissioning
- Asset on a network segment temporarily disconnected from the Log Analytics workspace
- Planned agent upgrade causing a brief reporting gap

**True Positive Indicators:**
- Asset was online and actively generating network traffic during the silent period (cross-reference network flow logs)
- Asset telemetry silence coincides with a security alert being generated on that asset
- Multiple assets go silent simultaneously (suggesting a coordinated attack or systemic agent removal)
- Agent service is stopped on a running asset (verified via AzureActivity or remote agent health check)

**Analyst Response Workflow:**

Step 1: Confirm whether the affected asset is intended to be online and active.

Step 2: Attempt remote connectivity to the asset (ping, RDP, Azure Serial Console).

Step 3: If the asset is reachable: check whether the MDE agent service is running. Azure Portal > Defender for Cloud > Inventory > [Asset] > Agent status.

Step 4: If the agent is stopped or not installed: treat as potential defense evasion. Escalate to Cloud Administrator to restore agent and investigate why it was stopped.

Step 5: Review what activity occurred on the asset during the silent period via any available telemetry (Azure Activity, NSG flow logs if available).

Step 6: If no explanation can be found for the agent stopping: treat as active threat and escalate to Security Lead.

**Escalation Path:**
- Confirmed maintenance: analyst closes with documentation
- Agent stopped with no change request: Cloud Administrator alerted within 2 hours
- Agent stopped on Tier 1 asset: Security Lead notified within 1 hour, treat as potential defense evasion

**Success Criteria:**
- EP-001 and EP-002 active after POA-004 remediation by April 5, 2026
- Zero assets with no ProtectionStatus report for more than 24 hours after full deployment
- All telemetry gap alerts investigated and closed within 4 hours

**Assessment Note:** The ProtectionStatus table was empty for all 126 assets at time of the March 2026 assessment. This use case describes the exact unmonitored gap confirmed during that assessment. EP-001 and EP-002 cannot fire until POA-004 is remediated.

---

### UC-010: Active Malware or Threat Detection

| Field | Value |
|---|---|
| Use Case ID | UC-010 |
| Use Case Name | Active Malware or Threat Detection |
| Priority | Priority 1 (Critical) |
| Threat Category | Execution, Defense Evasion |
| Implementing Rules | EP-002 (ProtectionStatus Not Protected), CR-004 (Defender for Cloud High or Critical Alert) |
| Current Status | Partially Active (EP-002 pending POA-004; CR-004 active) |

**Threat Description:**

An attacker has delivered and executed malware on a Cyber Range asset. This may be a result of a phishing attack, a drive-by download, exploitation of an unpatched vulnerability, or lateral movement from a previously compromised system. Active malware execution represents the most urgent threat scenario in the endpoint environment: the attacker has execution capability and may be establishing persistence, exfiltrating data, or preparing for lateral movement.

**Environment Context:**

The The Azure environment is an active learning environment. Students may unintentionally execute malicious code as part of lab exercises, or may introduce malware-contaminated files from personal systems. The risk is real and has occurred in similar shared lab environments. The detection must distinguish between intentional malware execution in an isolated lab VM (expected and acceptable) and unintentional malware execution in the production shared tenant environment (a genuine security incident).

**Detection Approach:**

CR-004 re-surfaces Defender for Cloud High and Critical security alerts into Sentinel to ensure they receive analyst attention. EP-002 (when ProtectionStatus data is flowing) directly monitors the threat status field to detect assets reporting an active, unremediated threat. Both rules must fire for this use case to have full coverage.

**Data Sources Required:**
- SecurityAlert (Microsoft Defender for Cloud connector - active and confirmed present)
- ProtectionStatus (Microsoft Defender for Endpoint connector - ABSENT, POA-004 required)

**Alert Trigger Condition:**
- CR-004: any SecurityAlert with AlertSeverity of High or Critical from Defender for Cloud or Defender for Endpoint within the last 15 minutes
- EP-002: any asset with ThreatStatusDetails containing an active, unremediated threat within the last 4 hours

**True Positive Indicators:**
- Alert involves a production asset (not an isolated lab VM)
- Malware family is known to be used in attacks against education sector environments
- Malware is associated with data exfiltration or credential theft
- Multiple alerts on the same asset within a short time window (multi-stage attack)
- Network connections to known C2 infrastructure observed in NSG flow logs coinciding with the alert

**Analyst Response Workflow:**

Step 1: Identify the affected asset and the nature of the threat from the Defender for Cloud alert.

Step 2: Determine whether the asset is a production asset or an isolated lab VM. Check the ResourceGroup tag.

Step 3: If the asset is a production asset: escalate to Security Lead immediately. Severity 1 incident if threat is confirmed active. Severity 2 if remediated but requiring investigation.

Step 4: Isolate the asset from the network: Azure Portal > Virtual Machines > [Asset] > Networking > add a deny-all NSG rule, or use MDE > Isolate device.

Step 5: Collect forensic artifacts via MDE Live Response before remediation.

Step 6: Remediate per Defender for Cloud RemediationSteps. Confirm remediation via re-scan.

**Escalation Path:**
- Isolated lab VM, controlled execution: document, close as expected lab activity
- Production asset, confirmed active threat: Severity 1, Security Lead immediate notification

**Success Criteria:**
- All Critical and High Defender for Cloud alerts reviewed within 1 hour (Critical SLA per SMP Section 7.1)
- Mean time from alert to isolation for production asset malware: less than 30 minutes
- Zero active unremediated threats on production assets at end of every business day

**Assessment Note:** SecurityAlert table contained unreviewed High and Critical alerts at time of the March 2026 assessment. CR-004 directly addresses the confirmed state of no triage process for these alerts.

---

### UC-011: Lateral Movement via RDP Brute Force

| Field | Value |
|---|---|
| Use Case ID | UC-011 |
| Use Case Name | Lateral Movement via RDP Brute Force |
| Priority | Priority 2 (High) |
| Threat Category | Lateral Movement, Credential Access |
| Implementing Rules | EP-003 (RDP Brute Force Threshold Breach) |
| Current Status | Active |

**Threat Description:**

An attacker who has gained initial access to the environment attempts to move laterally to other systems by brute-forcing RDP credentials. In a shared cloud environment, internet-accessible RDP (port 3389 open to 0.0.0.0/0) is one of the most commonly exploited initial access vectors and lateral movement channels. The attacker may be moving from one compromised VM to adjacent VMs to escalate privileges, access sensitive data, or establish additional footholds.

**Environment Context:**

The Configuration Baseline Standard (NS-001, NS-002) prohibits direct internet access to RDP and SSH ports on any Cyber Range asset. Any RDP brute force detected in SecurityEvent logs indicates either that an NSG rule has been misconfigured (a CBS violation) or that the brute force is occurring from within the network after an initial compromise.

**Detection Approach:**

The detection monitors SecurityEvent (Windows event log forwarding) for EventID 4625 (failed logon) with LogonType 10 (RemoteInteractive, meaning RDP). Assets generating ten or more failed RDP logons within a one-hour window are surfaced as alerts. This threshold catches automated attack tools while tolerating the occasional legitimate typo.

**Data Sources Required:**
- SecurityEvent (Windows event log forwarding via Log Analytics agent)

**Alert Trigger Condition:**
- Ten or more failed RDP logons (EventID 4625, LogonType 10) against a single computer within one hour

**False Positive Scenarios:**
- A student repeatedly mistyping their password attempting to connect to their assigned VM
- A failed automation script using stale RDP credentials

**True Positive Indicators:**
- Source IP is external (internet-routable, not RFC 1918 private IP range)
- A successful RDP logon (EventID 4624, LogonType 10) occurred from the same source after the failures
- The targeted computer is a production infrastructure asset, not a student lab VM
- The attack is occurring from multiple source IPs (distributed brute force)

**Analyst Response Workflow:**

Step 1: Identify the targeted asset and the source IP(s).

Step 2: Determine whether port 3389 is accessible from the internet on the targeted asset. Check the NSG rules: Azure Portal > [VM] > Networking > NSG inbound rules.

Step 3: If RDP is exposed to the internet (CBS NS-001 violation): engage Cloud Administrator to close the port immediately. This is an urgent remediation regardless of whether the brute force succeeded.

Step 4: Check EventID 4624 (successful logon, LogonType 10) on the same asset during or after the brute force window.

Step 5: If a successful RDP logon occurred: treat as potential lateral movement. Escalate to Security Lead. Review what activity occurred during the session via SecurityEvent logs.

Step 6: Block the source IP via NSG or CA Named Location if the attack is ongoing.

**Success Criteria:**
- Zero production assets with RDP exposed to 0.0.0.0/0 (CBS NS-001 compliance)
- All RDP brute force alerts triaged within 4 hours
- No successful RDP logons following a brute force alert without analyst review

---

### UC-012: Insider Threat via Bulk Account Manipulation

| Field | Value |
|---|---|
| Use Case ID | UC-012 |
| Use Case Name | Insider Threat via Bulk Account Manipulation |
| Priority | Priority 2 (High) |
| Threat Category | Impact, Persistence |
| Implementing Rules | ID-007 (Bulk User Account Operations) |
| Current Status | Active |

**Threat Description:**

A malicious insider with user management privileges (User Administrator, Global Administrator) abuses their access to perform bulk account manipulation: mass disabling of accounts (sabotage), bulk password resets (enabling unauthorized access), bulk deletion of accounts (destruction of records), or mass modification of user attributes. This can also be the signature of a compromised privileged account being used for destructive or reconnaissance activity.

**Environment Context:**

The Cyber Range has approximately 1,000 or more student accounts. A bulk account operation affecting hundreds of accounts could disrupt operations for an entire student cohort. Account data integrity is important for maintaining the access lifecycle records required by the ILM Procedure.

**Detection Approach:**

The detection monitors AuditLogs for account-related operations (Add user, Delete user, Update user, Disable account, Reset user password, Revoke all refresh tokens) and aggregates them by the initiating account. If any initiating account performs ten or more such operations within a one-hour window, the alert fires.

**Data Sources Required:**
- AuditLogs (Entra ID connector)

**Alert Trigger Condition:**
- Ten or more account-related operations by a single initiating account within one hour

**False Positive Scenarios:**
- Scheduled provisioning automation running a bulk onboarding or offboarding batch
- An Identity Administrator performing a large-scale role migration with documented change approval

**True Positive Indicators:**
- The operations include bulk deletions or disablements with no corresponding change request
- The initiating account is not the provisioning automation service principal
- The operations occurred outside business hours
- The affected accounts are not a cohort undergoing scheduled onboarding or offboarding

**Analyst Response Workflow:**

Step 1: Identify the initiating account, the operation types, and the affected accounts.

Step 2: Check whether the operations match a scheduled provisioning batch or known automation run.

Step 3: If the operations are not explained by automation: contact the account owner to confirm.

Step 4: If unauthorized: immediately revoke the initiating account's sessions and block sign-in. Review all affected accounts for unauthorized changes.

Step 5: Restore affected accounts from Entra ID audit log data as needed.

**Success Criteria:**
- All bulk account operation alerts reviewed within 4 hours
- Zero unauthorized bulk operations that persist without detection

---

### UC-013: Azure Subscription Privilege Escalation

| Field | Value |
|---|---|
| Use Case ID | UC-013 |
| Use Case Name | Azure Subscription Privilege Escalation |
| Priority | Priority 1 (Critical) |
| Threat Category | Privilege Escalation |
| Implementing Rules | PA-001 (Subscription Owner Assignment) |
| Current Status | Active |

**Threat Description:**

An attacker who has compromised an account with Azure RBAC role assignment permissions assigns the Subscription Owner role to themselves or a backdoor account. Azure Subscription Owner is the highest-level permission in the Azure resource plane. An Owner can delete all resources, disable all security controls, modify all networking configurations, add themselves to any resource group, and exfiltrate all data stored in Azure. A subscription Owner who also gains Entra ID Global Administrator can execute a complete tenant takeover.

**Environment Context:**

The Configuration Baseline Standard (AZ-003) limits subscription Owner assignments to a maximum of three. At time of the March 2026 assessment, no monitoring existed for subscription-level role assignment changes. Any unauthorized Owner assignment would have gone undetected indefinitely.

**Detection Approach:**

The detection monitors AzureActivity for the MICROSOFT.AUTHORIZATION/ROLEASSIGNMENTS/WRITE operation where the role being assigned is Owner (matched by name or GUID 8e3af657-a8ff-443c-a75c-2fe8c4bcb635). The rule runs every five minutes with a five-minute lookback for near-real-time detection.

**Data Sources Required:**
- AzureActivity (Azure Activity connector)

**Alert Trigger Condition:**
- Any successful Owner role assignment operation in AzureActivity within the last five minutes

**False Positive Scenarios:**
- Authorized Owner assignment during initial environment provisioning (documented in change record)
- Owner assignment as part of a subscription migration or restructuring (pre-approved)

**True Positive Indicators:**
- The assignment was initiated by an account that is not the designated Cloud Administrator
- The target account was recently created or has no history of Azure activity
- The assignment occurred outside business hours
- The assignment was followed by immediate resource enumeration or modification activity

**Analyst Response Workflow:**

Step 1: Identify the new Owner account and the initiating account.

Step 2: Immediately verify with the Security Lead and Cloud Administrator whether this was authorized.

Step 3: If not authorized: treat as Severity 1 incident. Remove the Owner assignment immediately via Azure Portal > Subscriptions > [Subscription] > IAM > [Assignment] > Remove.

Step 4: Revoke all sessions for both the initiating and target accounts.

Step 5: Audit all AzureActivity from the new Owner account during the period it held the Owner role.

Step 6: Check whether the new Owner made any changes: NSG modifications, resource deletions, new resource creations, Key Vault access.

**Success Criteria:**
- Any unauthorized Owner assignment removed within 15 minutes of detection
- Owner count maintained at 3 or fewer at all times (CBS AZ-003 compliance)

---

### UC-014: Network Perimeter Exposure via NSG Modification

| Field | Value |
|---|---|
| Use Case ID | UC-014 |
| Use Case Name | Network Perimeter Exposure via NSG Modification |
| Priority | Priority 2 (High) |
| Threat Category | Defense Evasion, Initial Access |
| Implementing Rules | CR-001 (NSG Rule Modified) |
| Current Status | Active |

**Threat Description:**

An attacker with network contributor access modifies a Network Security Group to open management ports (RDP port 3389, SSH port 22) or other sensitive ports to the internet (0.0.0.0/0). This creates a direct external access path to internal assets, either as an initial access vector for a future attack or as a persistence mechanism after achieving initial access. This is one of the most common cloud security misconfigurations exploited by attackers.

**Detection Approach:**

The detection monitors AzureActivity for successful writes to Network Security Group security rules. Every NSG modification triggers an alert for analyst review. Analysts determine whether the modification created an internet exposure (inbound allow rule for dangerous ports from 0.0.0.0/0).

**Data Sources Required:**
- AzureActivity (Azure Activity connector)

**Alert Trigger Condition:**
- Any successful NSG security rule write or NSG write operation in AzureActivity within the last 15 minutes

**True Positive Indicators:**
- The modification added an inbound allow rule for port 3389 or 22 from 0.0.0.0/0
- The modification was made by an account that is not the designated Cloud Administrator
- The modification removed a deny rule that was providing critical perimeter protection

**Analyst Response Workflow:**

Step 1: Review the NSG change in AzureActivity Properties to understand what rule was added or modified.

Step 2: Determine whether the change creates a new internet exposure (inbound allow from 0.0.0.0/0 for dangerous ports).

Step 3: If an internet exposure was created without authorization: engage the Cloud Administrator to revert the change immediately.

Step 4: If the change was authorized: document it and close the incident as Benign True Positive.

Step 5: Check CBS NS-001 and NS-002 compliance after every NSG alert.

**Success Criteria:**
- All NSG modification alerts reviewed within 4 hours
- Zero production assets with RDP or SSH exposed to 0.0.0.0/0 (CBS NS-001/NS-002)

---

### UC-015: Cloud Resource Destruction Attack

| Field | Value |
|---|---|
| Use Case ID | UC-015 |
| Use Case Name | Cloud Resource Destruction Attack |
| Priority | Priority 2 (High) |
| Threat Category | Impact |
| Implementing Rules | CR-002 (Azure Resource Deletion in Production) |
| Current Status | Active |

**Threat Description:**

An attacker with sufficient Azure RBAC permissions (Contributor or Owner) initiates bulk deletion of Azure resources. This may be a ransomware-style attack (delete resources, demand payment for restoration), a destructive attack by a disgruntled insider, or a cover-covering action after a breach (delete log storage, delete VMs to eliminate forensic evidence). Cloud resource deletion is permanent without backup recovery mechanisms and can cause significant operational disruption.

**Detection Approach:**

The detection monitors AzureActivity for DELETE operations that complete successfully in production-tagged resource groups. Every production resource deletion triggers an alert for analyst review.

**Data Sources Required:**
- AzureActivity (Azure Activity connector)

**Alert Trigger Condition:**
- Any successful DELETE operation in a production-tagged resource group within the last 15 minutes

**True Positive Indicators:**
- Multiple resource deletions in rapid succession from the same account
- Deletion of security-sensitive resources (Key Vaults, storage accounts with audit logs, VMs running Sentinel agents)
- Deletion initiated by an account with no prior resource management history

**Analyst Response Workflow:**

Step 1: Identify what was deleted, by whom, and when.

Step 2: Verify whether the deletion was authorized (change request, scheduled decommission).

Step 3: If unauthorized: attempt recovery via Azure Backup or soft-delete mechanisms. Notify Cloud Administrator immediately.

Step 4: If multiple resources are being deleted in an ongoing attack: revoke the initiating account's sessions and escalate to Severity 1.

**Success Criteria:**
- All production resource deletion alerts reviewed within 4 hours
- Recovery time objective for unauthorized deletions: resources restored within 4 hours via backup

---

### UC-016: Data Exfiltration via Storage Account Misconfiguration

| Field | Value |
|---|---|
| Use Case ID | UC-016 |
| Use Case Name | Data Exfiltration via Storage Account Misconfiguration |
| Priority | Priority 2 (High) |
| Threat Category | Exfiltration |
| Implementing Rules | CR-003 (Storage Account Public Access Enabled) |
| Current Status | Active |

**Threat Description:**

An attacker with storage account Contributor access enables public blob access on an Azure Storage Account that contains sensitive data (audit logs, backup data, user data). Once public access is enabled, anyone on the internet can read, download, or enumerate the contents of publicly accessible blob containers. This is a common cloud data breach vector.

**Detection Approach:**

The detection monitors AzureActivity for writes to storage account configuration where the requestBody contains the allowBlobPublicAccess property set to true. Any such change triggers an immediate alert.

**Data Sources Required:**
- AzureActivity (Azure Activity connector)

**Alert Trigger Condition:**
- Any storage account write where AllowBlobPublicAccess is set to true within the last 15 minutes

**True Positive Indicators:**
- Storage account contains sensitive data (audit logs, backup archives, user data)
- Change was made by an account that is not the designated Cloud Administrator
- Change was not accompanied by a change request

**Analyst Response Workflow:**

Step 1: Identify the storage account and its contents.

Step 2: Immediately disable public access: Azure Portal > Storage Account > Configuration > Allow Blob public access: Disabled.

Step 3: Review the storage access logs for any reads or downloads during the public exposure window.

Step 4: Determine whether any sensitive data was exposed. If so, treat as a data breach and notify the Security Lead.

**Success Criteria:**
- All storage public access alerts reviewed within 2 hours
- Public access disabled within 30 minutes of confirmation it was unauthorized
- Zero storage accounts with public access enabled at end of each business day (CBS AZ-006 compliance)

---

### UC-017: Monitoring Evasion via Log Source Disruption

| Field | Value |
|---|---|
| Use Case ID | UC-017 |
| Use Case Name | Monitoring Evasion via Log Source Disruption |
| Priority | Priority 1 (Critical) |
| Threat Category | Defense Evasion |
| Implementing Rules | MI-001 (Heartbeat Gap), MI-002 (Connector Ingestion Gap) |
| Current Status | Active |

**Threat Description:**

A sophisticated attacker who has gained administrative access to the environment attempts to blind the monitoring system before executing their primary attack. This may involve stopping the Log Analytics agent on specific assets (creating heartbeat gaps), modifying diagnostic settings to stop sending logs to Sentinel (breaking data connectors), or deleting the Log Analytics workspace itself. An attacker who disables logging before acting will leave far fewer forensic artifacts. This use case is considered Priority 1 because a successful logging disruption can retroactively invalidate the evidence chain for any subsequent investigation.

**Environment Context:**

At time of the March 2026 assessment, the ProtectionStatus table had zero rows for all 126 assets. While this was confirmed to be a configuration gap (POA-004) rather than an active attack, it illustrates exactly what this use case's detection is designed to catch: a complete absence of telemetry from a category of assets, detectable because the agent had never been configured rather than because it was stopped. Post-POA-004 remediation, any recurrence of a zero-row ProtectionStatus table is a potential defense evasion event.

**Detection Approach:**

MI-001 monitors the Heartbeat table for computers that were previously reporting but have gone silent for more than two hours. MI-002 monitors three critical telemetry tables (AuditLogs, SignInLogs, SecurityAlert) for complete ingestion stoppage (last record older than two hours).

**Data Sources Required:**
- Heartbeat (Log Analytics agent)
- AuditLogs, SignInLogs, SecurityAlert (for connector health verification)

**Alert Trigger Condition:**
- MI-001: any computer previously reporting heartbeat that has not sent a heartbeat in more than two hours
- MI-002: any critical telemetry table with last ingestion timestamp older than two hours

**True Positive Indicators:**
- The logging disruption coincides with other anomalous activity on the same asset or by the same account
- Logging disruption affects security-sensitive telemetry specifically (AuditLogs or SignInLogs) rather than a random subset of sources
- The Log Analytics agent service has been stopped and the asset is still online
- Diagnostic settings have been modified to stop sending logs to the workspace

**Analyst Response Workflow:**

Step 1: Identify which telemetry source has gone silent.

Step 2: Confirm whether the affected assets or connectors are intentionally offline.

Step 3: Check whether any Log Analytics diagnostic settings were modified recently: AzureActivity filtered for MICROSOFT.INSIGHTS/DIAGNOSTICSETTINGS operations.

Step 4: If diagnostic settings were modified without authorization: escalate to Security Lead immediately. This is a potential active attack.

Step 5: Restore logging connectivity. Engage Cloud Administrator for any connector restoration.

Step 6: Document the logging gap window. Any investigation of events during the gap period must note that telemetry integrity cannot be guaranteed for that timeframe.

**Success Criteria:**
- All logging gap alerts triaged within 1 hour
- No telemetry gap persists for more than 4 hours without documented explanation
- Post-incident: complete documentation of all blind-spot windows in the audit record

---

### UC-018: MFA Bypass via Authentication Method Tampering

| Field | Value |
|---|---|
| Use Case ID | UC-018 |
| Use Case Name | MFA Bypass via Authentication Method Tampering |
| Priority | Priority 2 (High) |
| Threat Category | Persistence, Defense Evasion |
| Implementing Rules | ID-008 (MFA Registration Deleted or Method Removed) |
| Current Status | Active |

**Threat Description:**

An attacker who has temporary access to an account (via phishing, SFA bypass, or a compromised session) removes the legitimate user's MFA methods and registers their own MFA device. On the next login, the attacker's MFA device receives the authentication prompt, not the legitimate user's phone. This technique, known as MFA fatigue or MFA registration abuse, gives the attacker a persistent authentication path that survives a password reset (since they will still receive the MFA challenge). It is also used to lock out legitimate users by removing their only MFA factor.

**Detection Approach:**

The detection monitors AuditLogs for operations that remove or modify MFA authentication methods, specifically filtering out addition operations (new registrations are expected during onboarding). Any removal of an MFA method triggers an alert for immediate review.

**Data Sources Required:**
- AuditLogs (Entra ID connector)

**Alert Trigger Condition:**
- Any MFA method deletion or authentication method update that is not an addition, within the last 15 minutes

**False Positive Scenarios:**
- A user legitimately replacing a lost or broken authenticator device (self-service)
- An Identity Administrator removing a stale MFA method for a departing user

**True Positive Indicators:**
- The MFA method was removed by an account other than the account owner (admin-initiated removal of another user's MFA without a ticket)
- The removal was followed immediately by a sign-in from an unfamiliar device or location
- The affected account is a privileged account (adm- prefix)
- The removal occurred outside business hours

**Analyst Response Workflow:**

Step 1: Identify the affected account, the MFA method removed, and whether it was self-service or admin-initiated.

Step 2: If admin-initiated and not expected: contact the Identity Administrator to verify.

Step 3: Check whether a new MFA method was registered for the account following the removal. If yes, confirm the new method belongs to the legitimate user.

Step 4: Check for a suspicious sign-in immediately following the removal.

Step 5: If unauthorized MFA tampering is suspected: block the account's sign-in immediately. The legitimate user may currently be locked out. Work with them to re-register their MFA device.

Step 6: If the account was used by the attacker after the MFA tampering: review all activity during the compromised session.

**Success Criteria:**
- All MFA method removal alerts reviewed within 4 hours
- Privileged account MFA removal alerts reviewed within 1 hour
- Zero accounts with zero registered MFA methods (excluding break-glass FIDO2 accounts) at end of each business day

---

## 4. Use Case Coverage Matrix

| Use Case ID | Use Case Name | Priority | Threat Category | Active? | SMP Section |
|---|---|---|---|---|---|
| UC-001 | Privileged Account Credential Attack | Critical | Credential Access | Yes | 5.1 |
| UC-002 | Single-Factor Authentication Policy Bypass | Critical | Defense Evasion | Yes | 5.1 |
| UC-003 | Break-Glass Account Activation | Critical | Privilege Escalation | Yes | 5.1 |
| UC-004 | Impossible Travel and Session Anomaly | High | Initial Access | Yes | 5.1 |
| UC-005 | Legacy Authentication Protocol Bypass | High | Defense Evasion | Yes | 5.1 |
| UC-006 | Privileged Role Assignment Abuse | Critical | Privilege Escalation | Yes | 5.1 / 5.4 |
| UC-007 | Service Principal Credential Persistence | High | Persistence | Yes | 5.4 |
| UC-008 | OAuth Consent Phishing Attack | High | Collection | Yes | 5.4 |
| UC-009 | Endpoint Protection Telemetry Loss | Critical | Defense Evasion | Partial | 5.2 |
| UC-010 | Active Malware or Threat Detection | Critical | Execution | Partial | 5.2 |
| UC-011 | Lateral Movement via RDP Brute Force | High | Lateral Movement | Yes | 5.2 |
| UC-012 | Insider Threat via Bulk Account Manipulation | High | Impact | Yes | 5.1 |
| UC-013 | Azure Subscription Privilege Escalation | Critical | Privilege Escalation | Yes | 5.5 |
| UC-014 | Network Perimeter Exposure via NSG Modification | High | Defense Evasion | Yes | 5.3 |
| UC-015 | Cloud Resource Destruction Attack | High | Impact | Yes | 5.5 |
| UC-016 | Data Exfiltration via Storage Account Misconfiguration | High | Exfiltration | Yes | 5.5 |
| UC-017 | Monitoring Evasion via Log Source Disruption | Critical | Defense Evasion | Yes | 4.3 |
| UC-018 | MFA Bypass via Authentication Method Tampering | High | Defense Evasion | Yes | 5.1 |

**Coverage Summary:**
- Total use cases: 18
- Priority 1 (Critical): 7 (UC-001, UC-002, UC-003, UC-006, UC-009, UC-010, UC-013, UC-017)
- Priority 2 (High): 11
- Fully Active: 16
- Partially Active (pending POA-004): 2 (UC-009, UC-010)
- MITRE ATT&CK tactics covered: Credential Access, Defense Evasion, Initial Access, Privilege Escalation, Persistence, Lateral Movement, Collection, Execution, Impact, Exfiltration

---

## 5. Use Case Lifecycle and Maintenance

### 5.1 Quarterly Use Case Review

The GRC Analyst must coordinate a quarterly use case review covering:

1. All use cases rated "Partially Active" must be reviewed for status change (POA-004 remediation enabling EP-001 and EP-002 should change UC-009 and UC-010 to Fully Active by April 5, 2026)
2. False positive rates for each active use case (target: below 20 percent per use case, below 30 percent overall)
3. Mean time to triage and mean time to resolve metrics by use case
4. Any new threat intelligence that should prompt a new use case
5. Any post-incident findings that should result in use case modification

### 5.2 New Use Case Intake

New use cases may be proposed by the Security Operations Analyst, Detection Engineer, or GRC Analyst. The intake process requires:

1. A documented threat description explaining the specific scenario
2. Identification of data sources required and confirmation they are available
3. A draft KQL query or description of detection logic
4. Draft analyst response workflow
5. Defined success criteria
6. Review and approval by the Security Lead

New use cases are assigned the next sequential UC-NNN ID and added to this registry.

### 5.3 Use Case Retirement

A use case may be retired when:
- The underlying threat is no longer relevant to the environment (technology change, platform migration)
- The detection has been superseded by a more effective use case covering the same threat
- The data source required is no longer available

Retired use cases are not deleted from this document. They are marked as Retired with the retirement date and rationale documented.

---

## 6. Related Documents

| Document | Relationship |
|---|---|
| Security Monitoring Policy v1.0 | Parent governance document; defines alert SLAs referenced in analyst response workflows |
| KQL Detection Rule Library v1.0 | Technical implementation of the detection logic referenced in each use case |
| Incident Response Plan v1.0 | Full incident response procedures activated by the escalation paths in this document |
| PAM Standard v1.0 | Privileged account requirements enforced by UC-003, UC-006, UC-013 |
| Configuration Baseline Standard v1.0 | Configuration requirements validated by UC-005, UC-007, UC-008, UC-013, UC-014, UC-015, UC-016 |
| ILM Procedure v1.0 | Account lifecycle requirements enforced by UC-012 |
| Vulnerability Management Policy v1.0 | Endpoint protection requirements enforced by UC-009 and UC-010 |
| Plan of Action and Milestones | POA-004 (endpoint telemetry) is a blocker for full activation of UC-009 and UC-010 |

---

*End of Document*

*This SIEM Use Case Documentation was produced as a direct output of the March 2026 multi-framework security gap assessment of the ABC Corp Azure shared tenant. Every use case traces to a specific detection gap or active threat condition confirmed during that assessment. The three highest-priority use cases (UC-001: confirmed credential attack activity from [redacted external domain]; UC-002: 83,600 SFA sign-ins with no detection; UC-009: 126 assets with zero endpoint telemetry) represent real, confirmed active conditions in a live environment at time of writing.*
