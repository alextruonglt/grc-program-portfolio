# 05 - Security Monitoring

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Effective Date:** March 5, 2026
**Environment:** Live Production Azure Shared Tenant (~1,000+ Users | Microsoft Sentinel SIEM)

---

> ⚠️ **Disclaimer:** All documents in this folder are sanitized versions of security monitoring documents produced following a gap assessment conducted on a real, live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. These documents are published for portfolio and educational purposes only.

---

## What This Folder Contains

| File | Document Type | Purpose |
|---|---|---|
| `security-monitoring-policy.md` | Policy | Establishes the organizational requirements for what must be monitored, alert response SLAs, analytics rule coverage mandates, incident management, and reporting cadence |
| `kql-detection-rule-library.md` | Technical Library | 29 production-ready KQL detection rules and 5 compliance posture queries, organized by threat category with full Sentinel configuration metadata |
| `siem-use-case-documentation.md` | Operational Registry | 18 detection use cases that provide the operational context, analyst response workflows, escalation paths, and success criteria for every major detection scenario |

These three documents form the complete security monitoring program. The policy defines the governance layer. The KQL library provides the technical layer. The use case documentation provides the operational layer that connects them. Each serves a different audience and a different purpose, and none of them is complete without the other two.

---

## Why These Documents Exist

The entire security monitoring program in this folder was built from zero. At time of the March 2026 assessment, the Microsoft Sentinel workspace had no active detection capability of any kind. The gaps were not minor or incremental. They were total.

**Confirmed at assessment time (real data from a live environment):**

| Finding | Source | Impact |
|---|---|---|
| Zero analytics rules active in Sentinel | Sentinel Analytics blade | No automated detection of any threat scenario |
| ProtectionStatus table returned 0 rows for all 126 assets | Live KQL query | Endpoint telemetry entirely absent from monitoring platform |
| High and Critical alerts in SecurityAlert table with no triage evidence | Live KQL query | Active threat signals unreviewed for unknown duration |
| Unactioned incidents in SecurityIncident table, no owner assigned | Live KQL query | Confirmed security events never investigated or closed |
| 83,600 of 203,906 sign-ins on single-factor authentication | Live KQL query (CP-001) | No detection rule existed for this condition |
| Active credential attack: 512 failed sign-ins from single external source | SignInLogs analysis | Attack was occurring with zero detection response |
| No alert triage SLAs existed | Governance documentation review | No response standard for any severity level |
| No escalation path existed for Critical alerts | Governance documentation review | No on-call coverage, no Security Lead notification procedure |
| No monitoring coverage requirements documented | Governance documentation review | No defined scope for what the SIEM was supposed to detect |

The ProtectionStatus finding and the zero-analytics-rules finding are the two most significant. A SIEM with no analytics rules is not a security monitoring platform. It is a data store. And a data store that has never received endpoint telemetry for any of its 126 assets provides no visibility into the threat surface that matters most.

---

## Document Summaries

### Security Monitoring Policy

The governance document that defines what the monitoring program must do, not how it does it. It establishes the mandatory requirements that the KQL library and use case documentation implement.

Key elements:

**Monitoring coverage requirements across five domains:** The policy explicitly lists every activity that must be detected within identity and access monitoring (14 activities), endpoint and infrastructure monitoring (8 activities), network and perimeter monitoring (5 activities), application and service principal monitoring (4 activities), and cloud resource and configuration monitoring (5 activities). Each activity is tied to a specific data source. This is not a generic list. It was derived from the actual threat surface of the assessed environment.

**18 mandatory analytics rule categories:** Every category in Section 6.1 maps to a rule in the KQL Detection Rule Library. Every category was absent at time of assessment. Break-glass account usage. Privileged role assignment outside PIM. Single-factor authentication success. Malware detection. Subscription Owner assignment. All 18 were completely unmonitored on March 5, 2026.

**Alert response SLAs with real enforcement:** Critical alerts require triage within one hour at any time of day with an on-call analyst designated weekly. High alerts require triage within four hours during business hours and eight hours after hours. These SLAs exist because the assessment confirmed unreviewed alerts of both severities had been sitting in the SecurityAlert table for an unknown duration.

**Eight defined monitoring KPIs:** Including mean time to detect (target: less than one hour for Critical), mean time to triage, alert false positive rate (target: below 30 percent), and a specific KPI for unactioned alerts older than SLA (target: zero). The zero target for unactioned alerts is a direct response to the confirmed state of multiple unactioned alerts in the live environment at assessment time.

### KQL Detection Rule Library

The technical document. Thirty-four total entries: 29 detection rules ready for direct import into Sentinel as scheduled analytics rules, and 5 compliance posture queries for ongoing audit and measurement.

Rules are organized into six threat categories:

| Category | Rules | Coverage |
|---|---|---|
| Identity and Authentication | 10 rules (ID-001 through ID-010) | Break-glass, SFA bypass, credential attacks, impossible travel, legacy auth, privileged role assignment, bulk operations, MFA tampering, high-risk user, GA sign-in |
| Endpoint and Infrastructure | 5 rules (EP-001 through EP-005) | ProtectionStatus non-reporting, protection state failure, RDP brute force, local admin creation, VM provisioning outside windows |
| Privilege and Access Escalation | 4 rules (PA-001 through PA-004) | Subscription Owner assignment, GA addition, PIM outside business hours, service principal privileged role |
| Application and Service Principal | 4 rules (AP-001 through AP-004) | High-privilege consent, new app registration, service principal credential, OAuth consent unverified publisher |
| Cloud Resource and Configuration | 4 rules (CR-001 through CR-004) | NSG modification, production resource deletion, storage public access, Defender for Cloud alerts |
| Monitoring Integrity | 2 rules (MI-001 through MI-002) | Heartbeat gap, connector ingestion gap |

The five compliance posture queries (CP-001 through CP-005) are not alerting rules. They are measurement tools:

- CP-001 (SFA volume) was the query that returned 83,600 SFA successes at assessment time. Target post-CA deployment: zero.
- CP-002 (blank account attributes) was the query that returned 2,315 accounts with blank Department, Job Title, and Manager. Target: zero.
- CP-004 (GA count) confirms Global Administrator assignments against the PAM Standard maximum of two.
- CP-005 (alert backlog) confirms zero unreviewed High and Critical alerts. This query would have returned confirmed-greater-than-zero results if run during the assessment.

EP-001 and EP-002 are listed as Pending Data Source because the ProtectionStatus table was empty at time of assessment. They will become fully active once POA-004 is resolved by April 5, 2026.

### SIEM Use Case Documentation

The operational document. Eighteen use cases that bridge the gap between "a rule fired" and "an analyst knows what to do." Each use case provides the full picture that a detection rule alone cannot: why the threat matters in this specific environment, what distinguishes a true positive from a false positive, who gets notified at each escalation level, what the containment actions are, and how success is measured.

Priority 1 (Critical) use cases trace directly to confirmed active conditions in the live environment:

| Use Case | Why It Is Priority 1 | Real Finding |
|---|---|---|
| UC-001: Privileged Account Credential Attack | Active credential attack confirmed at assessment time | 512 failed sign-ins from single external source in SignInLogs |
| UC-002: SFA Policy Bypass | Largest volume control gap in the entire program | 83,600 SFA successes from 203,906 sign-in events |
| UC-003: Break-Glass Account Activation | Highest-privilege accounts in the tenant; any use is an anomaly | No monitoring existed for break-glass sign-ins |
| UC-006: Privileged Role Assignment Abuse | Direct path to tenant compromise | No monitoring existed for role assignment changes |
| UC-009: Endpoint Telemetry Loss | 126 assets with zero telemetry is a confirmed defense evasion state | ProtectionStatus returned 0 rows for all 126 assets |
| UC-013: Azure Subscription Privilege Escalation | Subscription Owner is the highest Azure resource permission | No monitoring existed for subscription-level role changes |
| UC-017: Monitoring Evasion via Log Disruption | Attacker blinding the SIEM before acting is the most dangerous scenario | Confirmed complete absence of telemetry in multiple tables |

UC-009 and UC-010 are currently Partially Active because they depend on the ProtectionStatus table that POA-004 will restore. Every other Priority 1 use case is fully active.

---

## The Three-Layer Architecture

A common weakness in security monitoring portfolios is presenting KQL queries as the entire monitoring program. This folder demonstrates why that is insufficient.

The **Security Monitoring Policy** answers: what are we legally and organizationally required to detect, who is responsible, and what happens if an alert is not triaged within the required window?

The **KQL Detection Rule Library** answers: given a specific threat scenario, what exact query detects it, how often does Sentinel run it, and what does the output look like?

The **SIEM Use Case Documentation** answers: when that query fires and creates an incident, what does the analyst do in the next four hours? Who do they call? How do they tell a true positive from a false positive? What do they do to stop the attack?

All three layers must exist for a monitoring program to function. A query without a response workflow is a detection that produces noise. A policy without queries is a governance document with no enforcement mechanism. A use case without a policy is an operational procedure with no authority. This folder has all three.

---

## Key Statistics from the Live Environment

| Metric | Value at Assessment | Target | Source |
|---|---|---|---|
| Active Sentinel analytics rules | 0 | 29 (this library) | Sentinel Analytics blade |
| ProtectionStatus rows for 126 assets | 0 | 126 (one per asset) | Live KQL query |
| SFA sign-in successes (7-day window) | 83,600 of 203,906 total | 0 | CP-001 compliance query |
| Unreviewed High/Critical alerts | Confirmed greater than zero | 0 (daily target) | CP-005 compliance query |
| Unactioned incidents | Confirmed greater than zero | 0 | Live SecurityIncident query |
| Active credential attacks detected | 0 (no rules) | Detected by ID-003 | UC-001 assessment note |
| Alert triage SLA (Critical) | None (no SLA existed) | 1 hour | SMP Section 7.1 |

---

## Framework Coverage

| Control | NIST 800-53 | NIST CSF 2.0 | ISO 27001:2022 | SOC 2 TSC | CIS Controls v8 |
|---|---|---|---|---|---|
| Event logging and log management | AU-2, AU-3, AU-11 | DE.CM-01 | A.8.15 | CC4.1 | Control 8 |
| Audit record review and analysis | AU-6 | DE.AE-02 | A.8.16 | CC7.2 | Control 8 |
| Security monitoring and continuous monitoring | CA-7, SI-4 | DE.CM, DE.AE | A.8.16 | CC7.2 | Controls 8, 13 |
| Incident response and handling | IR-4, IR-5, IR-6 | RS.MA-01, RS.MA-02 | A.5.24, A.5.26 | CC7.3 | Control 17 |
| Threat intelligence | SI-5, PM-16 | ID.RA-02 | A.5.7 | CC3.2 | Control 7 |
| Security alerts and advisories | SI-5 | DE.AE-04 | A.8.16 | CC7.2 | Control 8 |
| Malicious code protection monitoring | SI-3 | DE.CM-01 | A.8.7 | CC6.8 | Control 10 |

---

## Connection to the Rest of the Repository

- The framework assessments in `/01-framework-assessments` rated AU and IR as Gap Rating 2 (Partially Implemented) in the NIST 800-53 assessment. The NIST CSF assessment rated DE (Detect) as Tier 2 and RS (Respond) as Tier 1. The ISO 27001 assessment rated A.8.16 as Partially Implemented with NC-findings against Clause 9.2 (no internal audit) and Clause 10.1 (no corrective action process). The SOC 2 assessment rated CC4 and CC7 as Partially in Place. This folder closes all of those gaps.
- The GRC Program Charter in `/02-program-governance` lists OBJ-003 (deploy minimum 10 Sentinel analytics rules), OBJ-007 (establish alert triage process), and OBJ-008 (deploy all 18 mandatory analytics rule categories) as immediate program objectives. All three are implemented by documents in this folder.
- The PAM Standard in `/03-identity-access` defines break-glass account monitoring requirements. UC-003 (Break-Glass Account Activation) and ID-001 implement that requirement operationally. The PAM Standard says a break-glass sign-in must generate an alert within five minutes. ID-001 runs every five minutes.
- The Vulnerability Management Policy in `/04-vulnerability-management` requires endpoint protection coverage to be confirmed in Sentinel. EP-001, EP-002, and UC-009 implement that requirement. They are currently Pending Data Source because POA-004 (from `/04-vulnerability-management`) has not yet been resolved.
- The POA&M in `/08-risk-tracking` tracks POA-002 (zero analytics rules), POA-004 (endpoint telemetry), and POA-006 (no monitoring governance) as open items. All three are directly remediated by documents in this folder.
