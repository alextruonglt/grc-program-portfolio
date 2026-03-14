# 09 - Program Policies

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Effective Date:** March 5, 2026
**Environment:** Live Production Azure Shared Tenant (~1,000+ Users)

---

> ⚠️ **Disclaimer:** All documents in this folder are sanitized versions of program-level policy documents produced following a gap assessment conducted on a real, live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. These documents are published for portfolio and educational purposes only.

---

## What This Folder Contains

| File | Document Type | Purpose |
|---|---|---|
| `information-security-policy.md` | Governing Policy | The foundational security policy that establishes organizational commitment, defines 10 core security domains, sets minimum requirements across the entire program, and provides the authority from which all other policies and procedures derive |
| `incident-response-plan.md` | Operational Plan | The formal incident response plan covering the six-phase IR lifecycle, six specific playbooks built from real alert types in the live environment, KQL detection queries, escalation matrix, and testing requirements |

These two documents anchor the entire security program at its highest and most operational levels. The Information Security Policy is the foundational document that says what the organization is committed to and why. The Incident Response Plan is the document that says what the organization does when something goes wrong. Every other document in the repository sits between these two: it is either governed by the ISP or it feeds into the IRP.

---

## Why These Documents Do Not Fit in Any Other Folder

Every other folder in this repository contains documents that address a specific security domain: identity governance, vulnerability management, security monitoring, configuration, third-party risk. The documents in this folder are different. They do not belong to any single domain because they govern or respond to all of them simultaneously.

The Information Security Policy references all ten security domains that the other folders address. It cannot go in `/03-identity-access` because it also governs vulnerability management. It cannot go in `/05-security-monitoring` because it also governs data classification. It is the policy that everything else lives under.

The Incident Response Plan references every other folder in the repository. A compromised account triggers Playbook 8.1, which involves `/03-identity-access` procedures. A malware detection triggers Playbook 8.3, which involves `/04-vulnerability-management` tools. A PowerShell alert triggers Playbook 8.5, which involves `/05-security-monitoring` queries. The IRP is the operational document that connects the entire program into a coherent response capability.

---

## Information Security Policy

The ISP is the document that did not exist before March 5, 2026. The NIST 800-53 assessment rated PM (Program Management) at Gap Rating 1. The NIST CSF assessment rated the GV (Govern) function at Tier 1. The SOC 2 assessment rated CC1 (Control Environment) as Partially in Place. In all three cases, the primary finding was the same: no formal information security policy was documented at the organizational level.

**What the policy covers:**

Ten policy domains, each grounded in a real finding from the March 2026 assessment:

| Domain | Section | Key Real Finding Cited |
|---|---|---|
| Access Control and Identity Management | 4.1 | 100% of 2,315 accounts with blank Department fields; 5 permanent GA accounts |
| Authentication Requirements | 4.2 | 83,600 SFA sign-ins (41% of total); zero CA policies enforced; zero PIM eligible assignments |
| Vulnerability and Patch Management | 4.3 | 185 Critical and 391 High findings; zero remediations; ad hoc scan schedule; DISA-STIG scan never executed |
| Incident Response | 4.4 | No IR plan; 24,905 PowerShell alerts with no triage; 50 alert types unaddressed |
| Audit Logging and Monitoring | 4.5 | 30+ days of log data with no review cadence; ProtectionStatus table empty for all 126 assets |
| Configuration Management | 4.6 | SecurityBaseline table returned no data; no OS-level compliance data available |
| Data Classification and Protection | 4.7 | No data classification policy existed |
| Third-Party and Supplier Security | 4.8 | Four cloud services active with no inventory, no contracts with security terms, no monitoring |
| Security Awareness and Training | 4.9 | Training program existed but completion was not tracked |
| Business Continuity and Recovery | 4.10 | Cloud backup enrolled but no RTO/RPO documented, no recovery plan tested |

**Why this policy is different from a template:** Every policy statement in Section 4 is accompanied by a "Driving Evidence" block that cites the specific real finding that made the statement necessary. Policy section 4.2.1 does not just say "MFA is required for all users." It says that and then cites "83,600 of 203,906 sign-in events (41%) completed on single-factor authentication, with zero Conditional Access policies enforced at time of assessment." The policy requirement and the evidence that demanded it are presented together. That is the difference between a policy written from evidence and a policy copied from a framework.

**The hierarchy this policy creates:** The ISP is explicitly the parent document for every other policy in the repository. The Vulnerability Management Policy, Security Monitoring Policy, Access Control Policy, and all other domain-specific policies derive their authority from this document. An auditor who wants to understand the governance structure reads the ISP first, then follows the references to domain-specific documents.

---

## Incident Response Plan

The IRP is the document that converts the detection capability built in `/05-security-monitoring` into an operational response capability. Detection without response is a data collection exercise. The IRP closes that gap.

**The six-phase lifecycle:**

The plan covers NIST SP 800-61 Rev 2's standard IR lifecycle fully:

1. Preparation: maintains detective controls, response readiness, and pre-authorized response actions
2. Detection and Analysis: triage process, initial severity classification, incident record requirements
3. Containment: account-level and endpoint-level containment action tables with triggers and methods
4. Eradication: step-by-step verification that attacker presence is fully removed
5. Recovery: re-enablement conditions, 72-hour monitoring requirement post-restoration
6. Post-Incident Review: required agenda, required outputs, risk register and POA&M update triggers

**The six playbooks built from real alert data:**

This is the element that most distinguishes this IRP from a generic template. Every playbook was written for a specific alert type or threat scenario that was confirmed present in the live environment at time of assessment.

| Playbook | Incident Type | Real Assessment Connection |
|---|---|---|
| 8.1 | Compromised User Account | 30 accounts with 5+ failed sign-ins, including one with 512 failures and a successful sign-in following the failure pattern |
| 8.2 | Privileged Account Compromise or Abuse | 5 permanent GA accounts with no PIM, no approval workflow, and no monitoring |
| 8.3 | Malware or Ransomware Detection | ProtectionStatus table empty for 126 assets: no visibility into malware state |
| 8.4 | Credential Stuffing or Brute Force Attack | Confirmed: 30 accounts exceeded the 5-failure threshold; top account at 512 failures from external domain |
| 8.5 | Suspicious PowerShell Execution | Confirmed: 24,905 PowerShell encoded command alerts, highest-volume alert type, zero triage evidence |
| 8.6 | Unauthorized Data Access or Exfiltration | No DLP controls existed; this playbook establishes the response if exfiltration is detected |

Playbook 8.5 is particularly notable. The 24,905 PowerShell alerts were not just a detection metric. They were an unresolved question: is this attacker tooling, authorized activity, or misconfigured systems generating noise? The playbook addresses all three possibilities and provides the KQL query to correlate the alerts with sign-in activity to begin answering that question. That is what a practitioner-level playbook looks like.

**The six KQL detection queries:** Section 9 includes six production-ready queries with assessment baselines documenting what each query returned when executed against the live environment. The MFA coverage check returned a 59% MFA rate. The failed sign-in summary would have returned 30 accounts exceeding the threshold, with 512 as the top count. These are not example queries. They were the assessment queries, repackaged as operational detection tools.

---

## How These Documents Connect to the Rest of the Repository

**Information Security Policy:**
- Is the parent document for every other policy in the repository by explicit reference in each document's "Related Documents" section
- Section 4.1 is operationalized by the Access Control Policy and ILM Procedure in `/03-identity-access`
- Section 4.2 is operationalized by the PAM Standard in `/03-identity-access`
- Section 4.3 is operationalized by the Vulnerability Management Policy and Patch Management Procedure in `/04-vulnerability-management`
- Section 4.5 is operationalized by the Security Monitoring Policy and KQL Detection Rule Library in `/05-security-monitoring`
- Section 4.6 is operationalized by the Configuration Baseline Standard in `/06-configuration-baseline`
- Section 4.8 is operationalized by the TPRM Template in `/07-third-party-risk`
- POA-017 in `/08-risk-tracking` tracked the absence of this policy as an open item; this document closes that item

**Incident Response Plan:**
- POA-009 in `/08-risk-tracking` tracked the absence of a formal IR plan as an open High-priority item; this document closes that item
- The Security Monitoring Policy in `/05-security-monitoring` Section 8 defines incident management requirements; this IRP is the document that fulfills those requirements
- The SIEM Use Case Documentation in `/05-security-monitoring` escalation paths all reference "Security Lead" and "Incident Commander" roles defined in this IRP's Section 3
- The PAM Standard in `/03-identity-access` Section 6.4 (Break-Glass Post-Use Review) and Section 5.4 (Emergency Activation) both reference "incident record" requirements that this IRP defines
- RISK-009 in `/08-risk-tracking` (No Formal Incident Response Capability, residual score 12, High) is directly addressed by this document

---

## Framework Coverage

| Control | NIST 800-53 | NIST CSF 2.0 | ISO 27001:2022 | SOC 2 TSC |
|---|---|---|---|---|
| Information security policies | PM-1, PL-1 | GV.PO-01 | A.5.1, Clause 5 | CC1.1, CC2.1 |
| Roles and responsibilities | PM-2 | GV.RR | A.5.2 | CC1.2 |
| Incident response plan | IR-8 | RS.MA-01 | A.5.24, A.5.25 | CC7.3 |
| Incident classification and response | IR-4, IR-5, IR-6 | RS.MA-02, RS.AN | A.5.25, A.5.26 | CC7.4 |
| Post-incident review | IR-4, CA-2 | ID.IM-04 | A.5.27 | CC7.5 |
| Evidence handling | AU-9, IR-9 | RS.MA-04 | A.5.28 | CC7.3 |
| Communication and notification | IR-6, IR-7 | RS.CO | A.5.26 | CC7.3 |
