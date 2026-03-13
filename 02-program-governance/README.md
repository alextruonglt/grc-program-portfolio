# 02 - Program Governance

**Organization:** ABC Corp | Microsoft Azure Cloud Environment
**Prepared by:** Alex Truong | GRC Analyst, Governance Risk and Compliance
**Effective Date:** March 5, 2026
**Environment:** Live Production Azure Shared Tenant (~1,000+ Users)

---

> ⚠️ **Disclaimer:** All documents in this folder are sanitized versions of governance documents developed for a real, live production Microsoft Azure environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. These documents are published for portfolio and educational purposes only.

---

## What This Folder Contains

The framework assessments in folder 01 identified what was broken. These two documents establish the formal authority and system documentation required to fix it.

| File | Document Type | Standard | Purpose |
|---|---|---|---|
| `grc-program-charter.md` | Program Charter | NIST CSF 2.0 GV | Formally authorizes the GRC program, establishes governance structure, defines roles, sets KPIs, and commits to remediation objectives |
| `system-security-plan.md` | System Security Plan | NIST SP 800-18 Rev 1 / NIST SP 800-53 Rev 5 | Formally describes the system, its security categorization, authorization boundary, and the implementation status of all 20 NIST 800-53 control families |

Neither document was written from a template. Both were written from the actual findings of the March 2026 live environment assessment documented in folder 01.

---

## Why These Two Documents Come Before Everything Else

Every policy, procedure, standard, and technical control in the folders that follow requires authorization to exist. The GRC Program Charter provides that authorization. Without it, the GRC Analyst is a person writing documents that anyone can ignore. With it, the GRC Analyst has formal delegated authority from the Security Lead to require evidence, assign remediation, and escalate non-compliance.

The System Security Plan provides the system context that everything else depends on. You cannot write a meaningful Vulnerability Management Policy without knowing what assets are in scope. You cannot write a Security Monitoring Policy without knowing what data the SIEM is collecting. You cannot assign control ownership without knowing who administers what. The SSP answers all of those questions first.

The order is not arbitrary. Assessment identifies the gaps. Charter authorizes the program to close them. SSP documents the system the program protects. Everything after that is the documented execution.

---

## GRC Program Charter

The charter is the capstone governance document for the entire program. It was written last, after all ten GRC deliverables were produced, because its job is to authorize and connect them, not to precede them.

**What it establishes:**

- Formal delegation of authority from the Security Lead to the GRC Analyst
- Governance tier structure with decision authority matrix
- Eight immediate objectives tied directly to confirmed POA&M items, each with a target date, owner, and measurable success criterion
- Seven risk items scored using Likelihood x Impact methodology, all derived from real assessment findings
- Twelve KPIs with measurement methods, targets, and escalation thresholds, each tied to a specific KQL query or tracking mechanism
- Full RACI for 21 GRC program activities across six roles
- Compliance obligation mapping for FERPA, GDPR, CCPA, and Washington State privacy law
- Complete deliverables inventory with document IDs, owners, and review schedules for all 16 program documents

**What makes it real rather than theoretical:**

Every objective in Section 6 traces to a POA&M item. Every KPI in Section 12 has a named KQL query that measures it. Every risk in Section 10 has a confirmed finding behind it. The three Critical-scored risks (all scoring 20/25) are scored that way because the environment had zero CA policies, an empty ProtectionStatus table, and zero Sentinel analytics rules. Those are not hypothetical risks. They are measured conditions from a live system on March 5, 2026.

---

## System Security Plan

The SSP is the most technically comprehensive document in the repository. It is a NIST SP 800-18 Rev 1 structured security plan covering all 20 NIST SP 800-53 Rev 5 control families across 126 individual controls, with implementation status determined by actual observation rather than assumption.

**What it documents:**

- FIPS 199 security categorization: MODERATE across Confidentiality, Integrity, and Availability
- Authorization status: Interim Authority to Test and Operate (IATO), pending formal ATO by June 5, 2026
- Full system boundary definition separating what is in scope from what is inherited from Microsoft Azure
- System interconnection table with TPRM assessment references for each external dependency
- Control-by-control implementation statements for all 20 NIST 800-53 Rev 5 families using [I] Implemented, [P] Partially Implemented, [L] Planned, [INH] Inherited, and [N/A] notation
- Continuous monitoring strategy tied to the 12 KPIs from the GRC Program Charter
- Complete POA&M summary with 8 tracked items including one closed item

**The 45% number:**

The effective implementation rate (Implemented + Inherited + N/A) across all 126 controls reviewed was 45% at time of the March 2026 assessment. That number is in the document because it is accurate. Most SSPs written as portfolio exercises claim 70-80% implementation without verifying anything. This one says 45% because that is what the data showed.

The three control families at 0% effective implementation are AT (Awareness and Training), CP (Contingency Planning), and PT (PII Processing and Transparency). Configuration Management is at 17% because the CBS compliance score was 20-30% and almost nothing was formally documented at assessment time. These numbers are uncomfortable. They are also honest, and that honesty is the point.

**The IATO / ATO distinction:**

The SSP documents the system as operating under an Interim Authority to Test and Operate rather than a full Authorization to Operate. This is the accurate status for a system where three Critical-tier POA&M items are still open. The authorization conditions in Section 12 are specific: POA-002, POA-003, and POA-004 must be resolved by April 5, 2026 before the system can transition to full ATO. A reviewer who understands how ATOs work will immediately recognize this as a real program operating under real authorization constraints, not a checkbox exercise.

---

## How These Documents Connect to the Rest of the Repository

| This Document | Authorizes or Informs |
|---|---|
| GRC Program Charter Section 6 (Objectives) | All documents in folders 03-07, each of which closes one or more OBJ items |
| GRC Program Charter Section 8 (Deliverables Inventory) | Every document in every folder of this repository by document ID |
| GRC Program Charter Section 10 (Risk Profile) | The POA&M summary visible in the SSP and referenced throughout all policy documents |
| GRC Program Charter Section 12 (KPIs) | The KQL Detection Rule Library (folder 04) compliance posture queries CP-001 through CP-005 |
| SSP Section 6 (Interconnections) | The TPRM assessments in folder 07 (GRC-TPRM-002 and GRC-TPRM-003) |
| SSP Section 8 (Control Implementation) | Every policy and procedure document, each of which implements one or more [P] or [L] controls |
| SSP Section 11 (POA&M Summary) | The risk tracking documents planned for folder 08 |

Nothing in this repository is a standalone document. Every document exists because a gap was found, and every gap was found because an assessment was done on a real system. The charter and SSP are the documents that make that chain of causation explicit.

---

## Key Statistics from These Documents

| Metric | Value | Source |
|---|---|---|
| GRC program deliverables authorized by the charter | 16 (10 active, 6 planned) | Charter Section 8 |
| KPIs defined and tracked | 12 | Charter Section 12 |
| POA&M items tracked at charter date | 8 (1 closed, 7 open) | Charter Section 10 / SSP Section 11 |
| Critical-scored risks at assessment baseline | 3 (all scored 20/25) | Charter Section 10.3 |
| NIST 800-53 Rev 5 control families documented in SSP | 20 of 20 | SSP Section 8 |
| Individual controls reviewed in SSP | 126 | SSP Section 9 |
| Effective control implementation rate at assessment | 45% | SSP Section 9.1 |
| System FIPS 199 categorization | MODERATE | SSP Section 2 |
| Authorization status at SSP date | IATO (pending ATO by June 5, 2026) | SSP Section 3.2 |
