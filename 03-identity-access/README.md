# 03 - Identity and Access Governance

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Effective Date:** March 5, 2026
**Environment:** Live Production Azure Shared Tenant (~1,000+ Users)

---

> ⚠️ **Disclaimer:** All documents in this folder are sanitized versions of identity governance documents produced following a gap assessment conducted on a real, live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. These documents are published for portfolio and educational purposes only.

---

## What This Folder Contains

| File | Document Type | Purpose |
|---|---|---|
| `access-control-policy.md` | Policy | Establishes the organizational rules and requirements governing all access to the ABC Corp environment |
| `pam-standard.md` | Technical Standard | Defines the specific configuration requirements, activation procedures, and monitoring controls for all privileged access |
| `identity-lifecycle-management-procedure.md` | Operational Procedure | Defines the step-by-step processes for provisioning, modifying, reviewing, and deprovisioning every identity in the tenant |

These three documents form a deliberate hierarchy. The Access Control Policy sets the rules. The PAM Standard implements those rules for privileged accounts specifically. The ILM Procedure operationalizes both for every identity type across the full account lifecycle. Each document references the others and none of them can stand alone without the other two.

---

## Why These Documents Exist

Every document in this folder was written in direct response to confirmed identity governance failures in a live production environment. These are not hypothetical controls for a hypothetical system. The assessment found the following on March 5, 2026:

**Identity Attribute Failures:**
- 100% of 2,315 user accounts had blank Department, Job Title, and Manager fields
- No attribute-based access reviews were possible
- No RBAC group assignment by business function was possible
- Account ownership could not be established for any account in the tenant

**Privileged Access Failures:**
- 5 permanent Global Administrator accounts with zero PIM configuration
- PIM portal confirmed zero eligible assignments, zero active assignments, zero activation history
- No approval workflow existed for any privileged role activation
- No notification was configured for any privileged role event
- No naming convention distinguished privileged accounts from standard accounts

**Account Lifecycle Failures:**
- No formal provisioning process existed
- No deprovisioning records existed
- 2 guest accounts with no sponsor, no expiration date, and no review record
- 30 accounts with 5 or more failed sign-in attempts with no investigation
- Top account: 512 failed sign-in attempts from an external domain with no lockout and no response

Every section, every requirement, and every SLA in these three documents closes one of these gaps.

---

## Document Summaries

### Access Control Policy

The parent document for the entire identity governance stack. It establishes the organizational authority and requirements that the PAM Standard and ILM Procedure implement. Covers account provisioning standards, access classification, MFA requirements, remote access rules, access review cadence, and enforcement consequences.

A hiring manager or auditor reading this document should understand: what access is permitted, who approves it, how it is reviewed, and what happens when the rules are violated. Those four questions are answered explicitly.

### PAM Standard

The most technically detailed document in the folder. It defines the privileged role taxonomy (Tier 0 through Tier 3), the PIM configuration requirements for each tier, the activation policies including maximum durations and approval workflows, the break-glass account configuration and testing requirements, and the KQL monitoring queries that enforce privileged access visibility on a daily and weekly basis.

The driving finding for this document was five permanent Global Administrator accounts with zero PIM in a tenant of 1,000+ users. The PAM Standard specifies exactly what that environment needed to look like instead, down to the maximum account counts per role, the required extensionAttribute values, and the 5-minute alerting SLA for break-glass account use.

Key numbers from the real environment documented in this standard:

| Finding | Real Value | Required Value |
|---|---|---|
| Permanent Global Administrators | 5 | Maximum 2 |
| PIM eligible assignments | 0 | All Tier 0 and Tier 1 roles |
| PIM activation history entries | 0 | Required for every privileged operation |
| Approval workflows configured | 0 | Required for Tier 0 and Tier 1 |
| Break-glass monitoring alerts | 0 | 5-minute SLA required |

### Identity Lifecycle Management Procedure

The operational document that turns the policy requirements into executable steps. It covers the full joiner-mover-leaver lifecycle for five identity types: standard users, privileged users, guest accounts, service accounts, and break-glass accounts.

Every step has a named owner, an SLA, and a documentation requirement. The procedure includes four KQL queries for ongoing lifecycle monitoring: new account detection, deprovisioning verification, failed sign-in threshold alerting, and MFA registration event tracking. The failed sign-in query was the query that would have surfaced the 512-failure account at assessment time had any monitoring existed.

---

## The Hierarchy in Practice

An interviewer or reviewer will often ask how your policy documents relate to each other. The answer for this folder is explicit:

The Access Control Policy (parent) says: all privileged access must use just-in-time activation with approval.

The PAM Standard (child standard) says: Tier 0 activation requires Security Lead approval within 30 minutes, maximum 1-hour duration, Microsoft Authenticator or FIDO2 only, and must generate an immediate notification to the Security Lead.

The ILM Procedure (child procedure) says: here are the exact steps to configure that PIM eligible assignment, here is the form the justification must take, here is what happens if the approver does not respond within 30 minutes, and here is the KQL query that confirms the activation appeared in AuditLogs.

That chain of causation from policy to standard to procedure is what a mature identity governance program looks like. This folder demonstrates it end to end.

---

## Framework Coverage

| Control | NIST 800-53 | NIST CSF 2.0 | ISO 27001:2022 | SOC 2 TSC | CIS Controls v8 |
|---|---|---|---|---|---|
| Account management and lifecycle | AC-2, AC-2(1), AC-2(3) | PR.AA-01, PR.AA-03 | A.5.16, A.5.18 | CC6.1, CC6.2 | Control 5 |
| Least privilege and privileged access | AC-3, AC-6 | PR.AA-02, PR.AA-04 | A.8.2 | CC6.3 | Control 5 |
| MFA and authenticator management | IA-2, IA-2(1), IA-5 | PR.AA-05, PR.AA-06 | A.8.5 | CC6.1 | Control 6 |
| Identifier management and attributes | IA-4 | PR.AA-01 | A.5.15, A.5.16 | CC6.2 | Control 5 |
| Access reviews | AC-2(4), AC-2(7) | PR.AA-05 | A.5.18 | CC6.3 | Control 6 |
| Session management | AC-11, AC-12 | PR.AA-05 | A.8.3 | CC6.1 | Control 6 |

---

## Connection to the Rest of the Repository

The identity governance documents in this folder are referenced by almost every other folder in the repository:

- The framework assessments in `/01-framework-assessments` identified all of the gaps these documents close. The NIST 800-53 assessment rated AC and IA as Gap Rating 1 (Not Implemented). The ISO 27001 assessment logged NC-10, NC-11, and NC-12 directly from the privileged access and MFA findings. The SOC 2 assessment rated CC6 as Not in Place.
- The GRC Program Charter in `/02-program-governance` lists OBJ-001 (CA policy deployment), OBJ-004 (attribute remediation), and POA-007 (blank account attributes) as immediate program objectives. All three are closed by documents in this folder.
- The Security Monitoring Policy and KQL Detection Rule Library in `/05-security-monitoring` contain detection rules ID-001 through ID-010 that monitor the privileged access and identity lifecycle controls defined in this folder.
- The POA&M in `/08-risk-tracking` tracks POA-002 (PIM not configured) and POA-007 (blank mandatory attributes) as the two highest-priority identity-related remediation items.
