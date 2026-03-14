# 06 - Configuration Baseline

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Effective Date:** March 5, 2026
**Environment:** Live Production Azure Shared Tenant (~1,000+ Users | 126 Assets Under Management)

---

> ⚠️ **Disclaimer:** All documents in this folder are sanitized versions of configuration standards produced following a gap assessment conducted on a real, live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. These documents are published for portfolio and educational purposes only.

---

## What This Folder Contains

| File | Document Type | Purpose |
|---|---|---|
| `configuration-baseline-standard.md` | Technical Standard | Defines the minimum required security configuration for every configurable component in the Azure environment, with specific measurable values, verification methods, and compliance scoring |

This is a single-document folder. That is intentional. A configuration baseline standard is not a stack of documents. It is one comprehensive reference that an administrator can open and answer a single question: is this system configured correctly? Everything needed to answer that question for the ABC Corp Azure environment is in this document.

---

## Why This Document Exists

No hardening standard of any kind existed for any component of this environment at time of the March 2026 assessment. Not for Conditional Access. Not for endpoints. Not for Sentinel. Not for service principals. Not for network security groups. The environment was running on defaults, assumptions, and inherited configurations with no documented baseline to measure against.

**Confirmed at assessment time (real data from a live environment):**

| Finding | Source | Impact |
|---|---|---|
| Conditional Access portal returned HTTP 401 for all policy queries | Azure portal | Zero CA policies enforced; no technical enforcement of any access control requirement |
| 83,600 of 203,906 sign-in events completed on single-factor authentication | Live KQL query (CP-001) | Any account in the tenant accessible with a password alone |
| ProtectionStatus table returned zero rows for all 126 assets | Live KQL query | Endpoint protection posture completely unverifiable for entire asset inventory |
| 100% of 2,315 user accounts had blank Department, Job Title, and Manager fields | Entra ID export | Account management, access reviews, and anomaly detection all impaired |
| No Azure Policy assignments active at the subscription level | Azure portal review | No automated configuration compliance enforcement |
| No analytics rules documented or active in Sentinel | Sentinel Analytics blade | No detection layer; SIEM was a data store with no detection function |
| No screen lock, patch, or system hardening standard existed | Governance documentation review | No security floor defined for any asset class |

The estimated compliance score at time of assessment was 20 to 30 percent against the requirements now defined in this standard. That number is documented in Section 9.3 of the standard itself, alongside the scoring methodology that will track improvement from that baseline going forward.

---

## What the Standard Covers

The Configuration Baseline Standard defines requirements across eight configuration domains organized into 14 sections, with over 90 individually numbered requirements, each with a required value and a verification method.

### Coverage Map

| Section | Domain | Key Requirements |
|---|---|---|
| 4.1 | Conditional Access (9 requirements) | Five named CA policies required; CA-001 through CA-009; SFA sign-in rate target: zero |
| 4.2 | User Account Configuration (8 requirements) | UA-001 through UA-008; all three blank attribute gaps addressed directly |
| 4.3 | Privileged Account Configuration (10 requirements) | PA-001 through PA-010; max 2 GAs; zero permanent active Tier 0/1 roles; PIM config |
| 4.4 | Guest Account Configuration (4 requirements) | GA-001 through GA-004; invite restrictions; access level; expiration requirements |
| 4.5 | MFA Requirements (7 requirements) | MF-001 through MF-007; standard vs. privileged vs. break-glass method requirements |
| 4.6 | Password and Authentication Policy (7 requirements) | PW-001 through PW-007; lockout thresholds; SSPR; TAP; breach-based expiration |
| 4.7 | Entra ID Diagnostic Settings (5 requirements) | DS-001 through DS-005; every log category that must flow to the LAW |
| 5.1 | Azure Subscription Controls (6 requirements) | AZ-001 through AZ-006; Defender for Cloud; Owner count; resource locks |
| 5.2 | Azure Policy Assignments | 8 named policies that must be assigned; all were absent at assessment |
| 5.3 | Defender for Cloud Configuration (7 requirements) | DC-001 through DC-007; plan requirements; auto-provisioning; regulatory compliance dashboard |
| 5.4 | Network Security Group Requirements (6 requirements) | NS-001 through NS-006; no RDP or SSH to 0.0.0.0/0; JIT VM access; flow logs |
| 6.1 | OS Hardening (8 requirements) | OS-001 through OS-008; patch level; screen lock; encryption; Windows Firewall; audit logging |
| 6.2 | Endpoint Protection (7 requirements) | EP-001 through EP-007; all currently unverifiable pending POA-004 |
| 6.3 | Administrative Account Configuration (4 requirements) | AC-001 through AC-004; LAPS; no shared accounts; UAC |
| 6.4 | Remote Access Configuration (4 requirements) | RA-001 through RA-004; NLA for RDP; session timeout |
| 7.1 to 7.4 | Sentinel Configuration (log retention, connectors, analytics rules) | SW-001 through SW-004; full connector requirements; 10 mandatory rule categories |
| 8.1 to 8.3 | Application and Service Principal Configuration | AP-001 through AP-005; SP-001 through SP-005; OA-001 through OA-003 |

### The Structure Each Reviewer Will Notice

Every requirement in this standard follows the same format: a requirement ID, the requirement description, the required value stated with enough specificity to be unambiguous, and a verification method that names exactly how to confirm compliance. There is no "ensure adequate access controls are in place" language anywhere in this document. Every requirement can be tested with a yes or no answer.

For example, CA-002 does not say "MFA should be required." It says "Enabled via CA policy; no exclusions permitted" with verification method "CA policy: Require MFA for all users -- confirm grant = MFA required." That level of specificity is what makes a baseline standard usable rather than decorative.

---

## The 20-30% Baseline Score

Section 9.3 of the standard documents the estimated compliance score at time of the March 2026 assessment as 20 to 30 percent. This is probably the most honest single data point in the entire repository, and it is worth understanding what it means.

A compliance score of 20 to 30 percent does not mean the organization was negligent. It means there was no defined baseline to measure against until this document was written. Before this standard existed, there was no way to answer the question "is this system configured correctly?" because "correctly" had never been defined. The 20 to 30 percent score is the measured starting point, not a judgment.

The scoring methodology in Section 9.3 is straightforward: each requirement counts as one control item; each is marked Compliant, Non-Compliant, or Deviation with formal documentation; score equals compliant controls divided by total controls. Four tiers: Compliant (90 to 100), Needs Improvement (75 to 89), At Risk (60 to 74), and Critical Non-Compliance (below 60). At time of assessment the score was Critical Non-Compliance. The target is Compliant within 12 months.

---

## The Six KQL Compliance Queries

Section 9.2 includes six KQL queries that form the ongoing measurement backbone for this standard. These are not one-time assessment queries. They are the recurring measurement tools that confirm baseline compliance is being maintained.

| Query | What It Measures | Real Result at Assessment |
|---|---|---|
| SFA sign-in rate | Conditional Access enforcement effectiveness | 83,600 SFA successes of 203,906 total sign-ins |
| User attribute completeness | UA-001, UA-002, UA-003 compliance | 2,315 accounts with blank mandatory attributes |
| GA count verification | PA-001 compliance (max 2 GAs) | 5 permanent GAs confirmed |
| ProtectionStatus compliance | EP-001 through EP-004 compliance | 0 rows returned; table was empty |
| Legacy authentication sign-ins | CA-004 enforcement effectiveness | Query would return results; CA policies absent |
| Sentinel connector health | SW-001 through SW-004; DC connector status | Heartbeat gaps indicate unmonitored assets |

After the CA policies mandated by Section 4.1 are deployed, the SFA query should return zero rows. After POA-004 is resolved, the ProtectionStatus query should return one row per asset with State equal to Protected. These queries are the measurement mechanism that turns policy language into verifiable facts.

---

## How This Document Connects to Everything Else

The Configuration Baseline Standard is the most cross-referenced document in the repository because it defines what "correctly configured" means for the components that every other document relies on. Almost every other document either depends on or verifies against the CBS.

| This CBS Requirement | Operationalized By |
|---|---|
| CA-001 through CA-009 (Conditional Access) | Access Control Policy Section 7; ILM Procedure Section 8 (MFA registration) |
| PA-001 through PA-010 (Privileged Accounts) | PAM Standard (entire document); ILM Procedure Section 4.2 |
| EP-001 through EP-007 (Endpoint Protection) | Vulnerability Management Policy Section 9; KQL rules EP-001 and EP-002 |
| NS-001 through NS-006 (Network Security) | KQL rule CR-001 (NSG modification detection); SIEM Use Case UC-014 |
| SW-001 through SW-004 + Analytics rules (Sentinel) | Security Monitoring Policy Section 4 and Section 6; KQL Detection Rule Library |
| OA-001 through OA-003 (OAuth Consent) | SIEM Use Case UC-008 (OAuth Consent Phishing detection) |
| OS-001 through OS-002 (Patch Compliance) | Patch Management Procedure Section 5 (SLA alignment) |
| AZ-003 (Subscription Owner count) | SIEM Use Case UC-013 (Subscription Privilege Escalation); PA-001 |

The framework assessments in `/01-framework-assessments` identified CM (Configuration Management) as Gap Rating 1 (Not Implemented) in the NIST 800-53 assessment. The ISO 27001 assessment rated A.8.9 (Configuration Management) as not evidenced. The SOC 2 assessment rated CC5 (Control Activities) as Not in Place, with zero CA policies enforced as the primary finding. The GRC Program Charter in `/02-program-governance` lists OBJ-001 (CA policy deployment), OBJ-002 (ProtectionStatus remediation), and OBJ-004 (attribute remediation) as immediate program objectives. All three trace directly to CA, EP, and UA requirements in this standard.

---

## Framework Coverage

| Control | NIST 800-53 | NIST CSF 2.0 | ISO 27001:2022 | SOC 2 TSC | CIS Controls v8 |
|---|---|---|---|---|---|
| Configuration baseline and hardening | CM-6, CM-7 | PR.PS-01, PR.PS-02 | A.8.9 | CC5.1 | Control 4 |
| Identity and access configuration | AC-2, IA-2, IA-5 | PR.AA-01, PR.AA-05 | A.5.16, A.8.5 | CC6.1 | Controls 5, 6 |
| Endpoint protection configuration | SI-3, SI-4 | DE.CM-01, PR.IR-01 | A.8.7 | CC6.8 | Control 10 |
| Network security configuration | SC-7, SC-8 | PR.IR-01 | A.8.20, A.8.22 | CC6.6 | Controls 12, 13 |
| Audit logging configuration | AU-2, AU-12 | DE.CM-01 | A.8.15 | CC4.1 | Control 8 |
| Vulnerability and patch management | RA-5, SI-2 | ID.RA-01 | A.8.8 | CC7.1 | Control 7 |
