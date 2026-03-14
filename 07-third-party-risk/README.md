# 07 - Third-Party Risk

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Effective Date:** March 5, 2026
**Environment:** Live Production Azure Shared Tenant (~1,000+ Users | 2 Active Tier 1 Vendors)

---

> ⚠️ **Disclaimer:** All documents in this folder are sanitized versions of third-party risk management documents produced following a gap assessment conducted on a real, live production Microsoft Azure environment. All organization names, vendor account references, and domain-specific details have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. These documents are published for portfolio and educational purposes only.

---

## What This Folder Contains

| File | Document Type | Purpose |
|---|---|---|
| `tprm-template.md` | Template and Procedure | Complete third-party risk assessment instrument covering vendor intake, tiering, 8-domain security questionnaire, composite scoring, risk treatment, and ongoing monitoring |

This is a single-document folder. The TPRM template is both the procedural guide that defines how assessments are conducted and the assessment instrument itself. A completed instance of this template for a specific vendor constitutes the formal audit record for that vendor relationship. Appendix A contains a fully worked example assessment for Tenable.io, one of the two Tier 1 vendors in the live ABC Corp environment at time of assessment.

---

## Why This Document Exists

No third-party risk management program of any kind existed in the ABC Corp cloud environment before this document was written. At time of the March 2026 assessment, three conditions were confirmed simultaneously: a Tier 1 SaaS vendor had agent software running on all 126 production assets with no risk assessment on record, no Data Processing Agreements were documented for any active vendor relationship, and no sub-processor inventory existed for any vendor. That combination, a deeply integrated vendor with full production asset access and zero governance around the relationship, is among the most significant risk exposures in the program.

**Confirmed at assessment time (real data from a live environment):**

| Finding | Source | Impact |
|---|---|---|
| No formal TPRM program or vendor risk registry existed | Governance documentation review | Vendor security risks were entirely unmanaged |
| No DPAs documented for any vendor relationship | Governance documentation review | No contractual data protection obligations for any vendor |
| No sub-processor inventory for any active vendor | Governance documentation review | Unknown parties may have access to ABC Corp data |
| Tenable.io agents deployed on all 126 production assets with no risk assessment | Tenable asset inventory; governance review | Tier 1 vendor with deepest production access had never been formally assessed |
| ABC Corp security controls entirely dependent on Microsoft Azure with no formal risk assessment | Governance documentation review | Single-vendor dependency for Sentinel, Defender, Entra ID with no documented risk acceptance |

The Tenable finding is worth understanding in its full context. A Nessus Agent installed on a production system has code execution capability on that system. It runs at elevated privilege to perform its scanning function. It receives updates from the Tenable cloud platform automatically. A compromised Tenable platform, a malicious update, or an overly permissive agent configuration is a supply-chain attack vector against all 126 assets simultaneously. This was not a theoretical risk. It was a real, active, unassessed dependency in a live environment.

---

## What the Template Covers

The TPRM template is structured as a six-phase assessment process covering every element of vendor risk evaluation from intake to ongoing monitoring.

### The Six Assessment Phases

**Phase 1: Vendor Intake (Part A)**
Vendor identification, service description, data access scope documentation, and tier classification. The tier classification matrix determines whether the vendor is Tier 1 (Critical), Tier 2 (High), or Tier 3 (Moderate), with the highest applicable criterion governing the assignment. If a vendor meets even one Tier 1 criterion, they are a Tier 1 vendor.

**Phase 2: Documentation Collection**
Defines the minimum documentation required by tier. Tier 1 requires SOC 2 Type II or ISO 27001, penetration test summary, DPA, subprocessor list, incident response policy, and business continuity plan summary. Tier 2 requires SOC 2 or ISO 27001 and DPA at minimum. Tier 3 accepts a vendor-completed self-assessment.

**Phase 3: Questionnaire Administration (Part B)**
Eight security control domains with 45 questions and 347 maximum points. Domain weights are calibrated to the specific risk profile of the ABC Corp cloud environment. Questions marked [T3] are the minimum required for Tier 3 vendors.

**Phase 4: Scoring and Risk Rating (Part C)**
Weighted composite score calculation with five risk rating tiers (Low Risk through Critical Risk). Seven mandatory risk flags that override a favorable composite score regardless of the numerical result.

**Phase 5: Risk Treatment Decision (Part D)**
Six treatment options from Accept to Reject with defined approval authority by tier. Conditions and remediation tracking table for vendors accepted with conditions. Ongoing monitoring requirements table.

**Phase 6: Conclusion and Approval (Part E)**
Assessment summary, signature block (GRC Analyst, Business Owner, Security Lead), document control fields including a 7-year retention period.

---

## The Eight Domain Questionnaire

The questionnaire is the technical core of the assessment. Each domain is weighted to reflect its relative importance in the ABC Corp cloud environment.

| Domain | Weight | Max Points | What It Measures |
|---|---|---|---|
| Data Protection and Privacy | 18% | 63 | Encryption at rest and in transit, breach notification SLA, data residency, retention and disposal, DPIA practices |
| Access Control and Identity Management | 16% | 50 | MFA enforcement, least privilege, PAM controls, access reviews, offboarding SLA, credential management |
| Incident Response and Business Continuity | 14% | 46 | IR plan testing cadence, breach notification commitment, 24/7 SOC capability, BCP/DRP testing, RTO/RPO, uptime SLA |
| Network and Infrastructure Security | 14% | 48 | Network segmentation, WAF/DDoS protection, vulnerability scanning frequency, penetration test cadence, patching SLA |
| Organizational Security Governance | 12% | 38 | Information security policy, CISO presence, security awareness training, background screening, vendor's own TPRM, cyber insurance |
| Application and Software Security | 12% | 40 | SSDLC documentation, SAST/DAST in pipeline, dependency scanning, bug bounty, code signing, OWASP Top 10 assessment |
| Compliance and Audit | 8% | 37 | SOC 2 Type II or ISO 27001, scope alignment, adverse findings, regulatory compliance, customer audit rights |
| Supply Chain and Sub-Processor Risk | 6% | 25 | Sub-processor assessment process, sub-processor list availability, advance notice of changes, contractual flow-down |

Data protection carries the highest weight because data protection failures have direct and immediate impact on individuals. Access control is second because credential compromise is the most common initial vector in vendor-related breaches. Governance weight reflects the reality that a vendor without program-level governance will eventually fail their technical controls regardless of current scores.

### The Five Risk Rating Tiers

| Composite Score | Risk Rating | Default Treatment |
|---|---|---|
| 85 to 100 | Low Risk | Accept |
| 70 to 84 | Low-Moderate Risk | Accept with Documentation |
| 55 to 69 | Moderate Risk | Accept with Conditions |
| 40 to 54 | High Risk | Escalate for Review |
| Below 40 | Critical Risk | Reject or Escalate |

### The Seven Mandatory Risk Flags

These seven conditions override a favorable composite score and require automatic Security Lead escalation regardless of the numerical result. A vendor scoring 90 with one mandatory flag still cannot be accepted without Security Lead sign-off.

1. Recent data breach involving customer data in the last 24 months
2. No MFA enforcement on systems processing ABC Corp data
3. No encryption at rest for ABC Corp data
4. Adverse finding or qualified opinion in the most recent SOC 2 report
5. No contractual breach notification SLA
6. Regulatory enforcement action in the last three years
7. Refusal to disclose sub-processor list

---

## Appendix A: The Tenable.io Worked Example

The most important part of this document for a portfolio reviewer is not the template itself. It is Appendix A. A template without a completed example is a form. Appendix A shows the template applied to a real vendor that was genuinely integrated with the live environment at time of assessment.

The Tenable.io assessment demonstrates:

**Why Tenable is Tier 1:** Agent-based access to all 126 production assets. Processes detailed security telemetry including configuration data, open ports, installed software, and vulnerability details. The Vulnerability Management Program operationally depends on Tenable. A compromised Nessus Agent update is a supply-chain attack vector against all 126 assets simultaneously.

**How the scoring works in practice:** Eight domains scored against publicly available Tenable documentation and their SOC 2 Type II certification scope. Domain 8 (Supply Chain) scored 19 of 25, not full marks, because the standard Tenable agreement does not contractually commit to advance notice of sub-processor changes. This is a real gap, not a hypothetical one. The template captures it.

**The composite result:** 87.7 (Low Risk). Zero mandatory risk flags. Risk treatment: Accept. The key remediation note from the assessment: Nessus Agent updates should be tested in a non-production asset before broad deployment. That recommendation traces directly to the supply-chain attack surface identified in the tier classification rationale.

**What it means for the portfolio:** A reviewer who understands TPRM will see an analyst who did not just produce a template but applied it to a real vendor, understood why that vendor is high-stakes, scored them honestly rather than inflating the score, and identified a specific actionable risk item from the assessment. That is practitioner-level work.

---

## Current Vendor Tier Registry

| Vendor | Service | Tier | Last Assessed | Next Due |
|---|---|---|---|---|
| Microsoft Corporation | Azure Cloud Platform, Entra ID, Microsoft Sentinel, Defender for Cloud, Defender for Endpoint | Tier 1 | March 5, 2026 | March 2027 |
| Tenable, Inc. | Tenable.io Vulnerability Management (126 assets under management) | Tier 1 | March 5, 2026 (see Appendix A) | March 2027 |

Both vendors were identified during the March 2026 gap assessment as Tier 1 vendors with no prior formal risk assessment. Both assessments were initiated as part of the initial GRC program buildout. Both are scheduled for annual reassessment in March 2027.

---

## Framework Coverage

| Control | NIST 800-53 | NIST CSF 2.0 | ISO 27001:2022 | SOC 2 TSC | CIS Controls v8 |
|---|---|---|---|---|---|
| External system services and vendor management | SA-9, SA-9(3) | GV.SC-01, GV.SC-02 | A.5.19, A.5.20 | CC9.2 | Control 15 |
| Supply chain risk management plan | SR-2, SR-3 | GV.SC-03, GV.SC-06 | A.5.21, A.5.22 | CC9.2 | Control 15 |
| Supplier assessment and reviews | SR-6 | GV.SC-07 | A.5.22 | CC9.2 | Control 15 |
| Information exchange and data protection | CA-3, SC-8 | GV.SC-06 | A.5.20 | CC9.2 | Control 15 |
| Supply chain inspection and monitoring | SR-10 | GV.SC-08 | A.5.21 | CC3.2 | Control 15 |

---

## Connection to the Rest of the Repository

- The framework assessments in `/01-framework-assessments` rated SA and SR as Gap Rating 2 (Partially Implemented) in the NIST 800-53 assessment. The ISO 27001 assessment logged NC-findings against A.5.19 (information security in supplier relationships) and A.5.20 (addressing security within supplier agreements). The SOC 2 assessment rated CC9 (Risk Mitigation) as Not in Place, with no vendor inventory and no supplier security requirements as the primary findings. This document closes all of those gaps.
- The GRC Program Charter in `/02-program-governance` lists OBJ-005 (Tier 1 TPRM assessments for Microsoft and Tenable) as an immediate program objective with a target date of April 5, 2026. The Tenable example in Appendix A represents the initial assessment that satisfies that objective.
- The SSP in `/02-program-governance` documents Tenable.io as a formal system interconnection with a TPRM assessment reference (GRC-TPRM-003). The completed example in Appendix A is that referenced document.
- The POA&M in `/08-risk-tracking` tracks POA-008 (no TPRM program, no vendor risk assessments on file) as an open item. This document and the example assessment in Appendix A constitute the closure evidence for POA-008.
- The Vulnerability Management Policy in `/04-vulnerability-management` Section 4.3 requires Tenable scanning service account credentials to be stored in Azure Key Vault. That requirement is directly traceable to the access control findings from the Tenable TPRM assessment in Appendix A.
