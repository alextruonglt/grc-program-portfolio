# Plan of Action and Milestones (POA&M)

**Organization:** ABC Corp | Microsoft Entra ID Tenant
**Prepared by:** Alex Truong | Identity and Access Governance Analyst
**Audit Date:** March 5, 2026
**Classification:** CONFIDENTIAL

> **Disclaimer:** This is a sanitized version of a Plan of Action and Milestones produced from a gap assessment conducted on a live Azure cloud environment. All organization names, account names, and domain references have been replaced with generic equivalents to protect confidentiality. No proprietary organizational data is represented. This document is published for portfolio and educational purposes only.

**Assessment Standards Applied:** NIST SP 800-53 Rev 5 | NIST CSF 2.0 | ISO/IEC 27001:2022 | SOC 2 TSC

**Assessment Note:** This POA&M was produced from a real, read-only gap assessment conducted against a live Azure shared tenant environment. All findings are evidence-based and drawn from live Microsoft Sentinel Log Analytics Workspace telemetry, Tenable vulnerability scan exports, and Microsoft Entra ID data at the time of assessment. No configuration changes were made during the assessment.

---

## POA&M Summary

| Metric | Value |
|---|---|
| Total Open Items | 20 |
| Critical Priority Items | 5 |
| High Priority Items | 9 |
| Medium Priority Items | 6 |
| Items 0-30 Days | 7 |
| Items 31-90 Days | 8 |
| Items 91+ Days | 5 |
| Fixed/Closed Items | 0 |

---

## POA&M Tracking Table

| POA&M ID | Control ID (NIST 800-53) | ISO 27001 | CSF 2.0 | Finding Description | Evidence Basis | Risk Level | Responsible Owner | Planned Remediation Action | Milestone: Start | Milestone: Complete | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|
| POA-001 | IA-2, IA-2(1), IA-2(2) | A 8.5 | PR.AA | MFA enforced on only 59% of sign-ins. 83,600 of 203,906 sign-in events completed on single-factor authentication. Conditional Access portal was inaccessible during assessment; zero CA policies confirmed enforced across all sign-ins. | SigninLogs: MFA-protected 120,306 / Total 203,906. CA notApplied: 203,906. CA Enforced: 0. | Critical | Identity Admin | Configure and enforce at least one Conditional Access policy requiring MFA for all users. Block legacy authentication protocols via CA. Validate enforcement via SigninLogs query after deployment. | 2026-03-06 | 2026-03-20 | Open |
| POA-002 | IA-2, AC-6, AC-2 | A 8.2 | PR.AA | Five of eight privileged accounts hold permanent Global Administrator assignments. No Privileged Identity Management (PIM) is configured. Zero eligible assignments exist. No just-in-time access mechanism is in place for any privileged role. | Entra ID export: GA count 5, PIM Eligible 0, PIM Active (permanent) 5. No P1/P2 license confirmed. | Critical | Identity Admin | Reduce GA count to maximum 2. Evaluate Entra ID P2 licensing to enable PIM. Convert remaining privileged accounts to lower-privilege roles. Document justification for each retained GA. | 2026-03-06 | 2026-04-05 | Open |
| POA-003 | SI-2, RA-5 | A 8.8 | PR.PS, ID.RA | 185 critical and 391 high severity vulnerabilities identified across 126 assets. Zero findings have been remediated. All 805 new findings remain open with no remediation evidence. Scan schedule is ad hoc with no defined cadence. | Tenable export: Critical 185, High 391, Medium 491, Low 277. Fixed 0. New 805. Scan schedule: Ad Hoc/Manual. | Critical | Vulnerability Management | Establish formal remediation SLAs: Critical within 15 days, High within 30 days, Medium within 90 days, Low within 180 days. Begin triage of all 185 Critical findings. Transition scans to scheduled weekly cadence. | 2026-03-06 | 2026-03-21 | Open |
| POA-004 | SI-3 | A 8.7 | PR.IR | No endpoint protection telemetry is available. The ProtectionStatus table in the SIEM returned no data. Total endpoints, protected count, and protection type are entirely unknown. | SIEM ProtectionStatus query: No data returned. Total endpoints: No data. | Critical | Endpoint Admin | Deploy Microsoft Defender for Endpoint or equivalent on all identified assets. Verify ProtectionStatus data populates in the SIEM within 7 days of deployment. Report coverage percentage as a recurring metric. | 2026-03-06 | 2026-04-05 | Open |
| POA-005 | AU-6, IR-4, SI-3 | A 8.16 | DE.AE | Top security alert is PowerShell encoded command execution with 24,905 occurrences. No documented triage, investigation, or response to this alert volume is evidenced. Alert fatigue risk is high. | SIEM SecurityAlert: PowershellEncoded alert count 24,905. Severity: High and Medium. No corresponding investigation documented. | Critical | SOC Analyst | Investigate and classify the 24,905 PowerShell encoded alerts. Determine whether activity represents authorized behavior, attacker tooling, or misconfiguration. Implement suppression rules or automated playbook response. Document triage decision and outcome. | 2026-03-06 | 2026-03-20 | Open |
| POA-006 | CM-2, CM-6 | A 8.9 | PR.PS | SIEM SecurityBaseline table returned no data. No OS-level configuration compliance data exists. DISA-STIG compliance scan ("Copy of DISA-STIG implementation") has never been executed; status is Empty. | SIEM SecurityBaseline query: No data returned. DISA-STIG scan status: Empty - never executed. | High | Cloud Admin | Enable Defender for Cloud security baseline agent on all Azure-connected endpoints. Execute the existing DISA-STIG scan. Document baseline pass rates per OS type. Target 90% pass rate benchmark. | 2026-03-10 | 2026-04-10 | Open |
| POA-007 | AC-2, AC-6 | A 8.3 | ID.AM | 100% of 2,315 user accounts have a blank Department field. No organizational attribute management is in place. Role-based or attribute-based access reviews cannot be conducted without this data. | Entra ID export: Accounts with blank Department = 100% (2,315 of 2,315). | High | Identity Admin | Initiate account attribute hygiene campaign to populate Department, Job Title, and Manager fields for all users. Establish mandatory field enforcement for new account creation. Set completion target of 90% within 60 days. | 2026-03-15 | 2026-05-15 | Open |
| POA-008 | AC-2, AU-6 | A 8.5 | PR.AA | 30 accounts recorded more than 5 failed sign-in attempts. Top failing account logged 512 failed attempts. No automated lockout policy or investigation trigger was evidenced. | SIEM SigninLogs query: 30 accounts with >5 failures. Top account: [sanitized] with 512 failures. | High | Identity Admin | Investigate the 30 accounts with excessive failed sign-in attempts for potential brute-force or credential-stuffing activity. Implement account lockout policy. Escalate the 512-failure account for immediate review and potential block. | 2026-03-06 | 2026-03-20 | Open |
| POA-009 | IR-8, IR-4 | A 5.26 | RS.MA | No formal Incident Response plan was identified. SecurityIncident records exist in New, Active, Closed, and Deleted states, confirming incidents are being generated but no structured response process is documented. | SIEM SecurityIncident: Statuses present (New, Active, Closed, Deleted). IR plan documentation: Not found. | High | GRC Analyst | Develop and approve a formal Incident Response plan covering severity definitions, response team roles, detection and triage steps, containment, recovery, post-incident review, and escalation matrix. | 2026-03-15 | 2026-04-15 | Open |
| POA-010 | RA-3, RA-5 | Clause 6.1.2 | GV.RM, ID.RA | No formal risk management process exists. No risk register is maintained. 1,344 open vulnerability findings exist with no corresponding risk register entries, risk owners, or treatment decisions documented. | Governance review: Risk register = No. Formal risk management process = No. Tenable total open findings: 1,344. | High | GRC Analyst | Establish a risk register capturing: risk description, source, likelihood (1-5), impact (1-5), inherent score, control in place, residual score, risk owner, and treatment decision. Populate with all Critical and High findings as initial entries. | 2026-03-15 | 2026-04-30 | Open |
| POA-011 | CA-2, CA-7 | Clause 9.2 | GV.OV | No internal audit program exists. No management review of the security program has been conducted or documented. No nonconformity or corrective action process is in place. | Documentation review: Internal audit program = No. Management review = No. Nonconformity process = No. | High | GRC Analyst | Develop an internal audit plan with defined scope, frequency, and methodology. Schedule first audit within 90 days. Establish management review meeting cadence (minimum annual). | 2026-03-20 | 2026-06-05 | Open |
| POA-012 | SA-9, SR-3 | A 5.19, A 5.20 | GV.SC | Third-party providers have not been formally inventoried. No supplier security requirements exist in contracts. No third-party monitoring process is in place. Four cloud and SaaS services are known: Microsoft 365, Azure, Tenable, and one additional SaaS platform. | Governance review: Third-party inventory = No. Supplier security requirements in contracts = No. Third-party monitoring = No. | High | GRC Analyst | Create a formal third-party service inventory. Review existing agreements for security terms. Establish minimum security requirements for each provider. Implement an annual supplier review process. | 2026-03-20 | 2026-05-01 | Open |
| POA-013 | AU-2, AU-6 | A 8.15 | DE.CM | No formal audit log review process is evidenced. Log data is present in the SIEM (30+ days of history) but no review cadence, alert thresholds, or escalation procedures have been documented. Log retention policy is not documented. | SIEM AuditLogs: 30+ days of history. Operations captured include user updates, service principal management, group membership changes, and password resets. No formal review procedure found. | High | SOC Analyst | Establish formal audit log review cadence (minimum weekly for High and Critical alerts). Document log retention policy (minimum 90 days online). Create automated alerting rules for high-frequency alert types. | 2026-03-15 | 2026-04-15 | Open |
| POA-014 | PL-2, CA-9 | Clause 4.3 | GV.PO | No System Security Plan exists. No formal scope document has been produced. System boundaries and security architecture are documented but have not been codified into a formal SSP or scope statement. | Documentation review: SSP = No. Scope document = No. Security architecture documented = Yes. System boundaries defined = Yes. | Medium | GRC Analyst | Develop a System Security Plan using existing architecture and boundary documentation as inputs. Document inherited controls. Map implemented controls to NIST 800-53 families. Target completion within 90 days. | 2026-04-01 | 2026-06-30 | Open |
| POA-015 | PM-9, RA-3 | Clause 6.1 | GV.RM | No formal risk management strategy or methodology is documented. Risks are communicated to leadership informally but no risk identification, analysis, evaluation, or treatment methodology is defined. | Governance review: Formal risk management process = No. Risk management strategy = No. Risks communicated to leadership = Yes (informal). | Medium | GRC Analyst | Document a formal risk management process covering identification, analysis, evaluation, treatment, and periodic review. Align to ISO 27005 or NIST SP 800-39. Link to risk register (POA-010). | 2026-03-20 | 2026-05-15 | Open |
| POA-016 | SC-7, SC-8 | A 8.20 | PR.PS | 391 High and 185 Critical vulnerability findings include network and communications exposure items. No network segmentation documentation is in place beyond platform defaults. CA enforcement is zero, meaning no location-based or device-compliance boundary controls are active. | Tenable: 185 Critical and 391 High findings. CA enforced: 0. Network segmentation documentation: Not found. | Medium | Cloud Admin | Review findings for network-specific CVEs. Prioritize remediation of network-layer findings. Document Virtual Network topology and firewall rulesets. | 2026-03-20 | 2026-05-01 | Open |
| POA-017 | PM-1, PM-2 | Clause 5.1 | GV.OC | No cybersecurity budgeting evidence was identified. Management commitment is present informally but has not been formalized through signed policy endorsement or documented resource allocation. | Governance review: Cybersecurity budgeting evidence = No. Named cybersecurity owner = Yes. Cybersecurity policy = Yes. | Medium | CISO / Leadership | Document formal management endorsement of the security program. Establish and record a cybersecurity budget line item. Produce a GRC Program Charter defining program authority, scope, and governance structure. | 2026-04-01 | 2026-05-15 | Open |
| POA-018 | AT-2, PM-13 | A 6.3 | PR.AT | Security awareness training program exists but training completion is not tracked. No mechanism exists to verify that individuals with security responsibilities have completed required training. | Governance review: Security awareness training program = Yes. Training completion tracked = No. | Medium | HR / GRC | Implement training completion tracking. Define training requirements by role. Establish minimum completion target (90% annually). Report completion rates to management. | 2026-04-01 | 2026-05-15 | Open |
| POA-019 | CP-2, CP-9 | A 8.13, A 5.30 | RC.RP | Cloud backup is enrolled, representing a foundational recovery control. However, no RTO or RPO has been documented, and no recovery plan testing has been performed. Backup integrity and restoration procedures are unvalidated. | Governance review: Backup enrolled = Yes. RTO/RPO documented = No. Recovery plan tested = No. | Medium | IT / Cloud Admin | Define and document RTO and RPO targets. Develop a formal recovery plan. Conduct at least one technical restoration test annually. | 2026-04-15 | 2026-06-15 | Open |
| POA-020 | PL-2 | Clause 6.1.3 | GV.PO | No Statement of Applicability exists. This is a mandatory document under ISO 27001:2022 Clause 6.1.3 and a prerequisite for any Stage 1 certification audit. All 93 Annex A controls require applicability determination and implementation status documentation. | Documentation review: Statement of Applicability = No. ISO 27001 Clause 6.1.3 requirement confirmed. | Medium | GRC Analyst | Produce a Statement of Applicability listing all 93 Annex A controls with: applicability (Yes/No), implementation status, justification for exclusion, and evidence of implementation where applicable. | 2026-04-15 | 2026-07-01 | Open |

---

## Remediation Timeline Summary

### 0-30 Days: Critical Priority (Immediate Action Required)

| POA&M ID | Item | Owner |
|---|---|---|
| POA-001 | Enforce MFA via Conditional Access for all users | Identity Admin |
| POA-002 | Reduce GA count; evaluate PIM licensing | Identity Admin |
| POA-003 | Begin Critical vulnerability remediation; establish SLAs | Vulnerability Management |
| POA-004 | Deploy endpoint protection on all identified assets | Endpoint Admin |
| POA-005 | Investigate 24,905 PowerShell encoded alerts | SOC Analyst |
| POA-008 | Investigate 30 accounts with failed sign-in anomalies | Identity Admin |
| POA-013 | Establish audit log review cadence and retention policy | SOC Analyst |

### 31-90 Days: High Priority (Short-Term Remediation)

| POA&M ID | Item | Owner |
|---|---|---|
| POA-006 | Enable security baseline monitoring; execute DISA-STIG scan | Cloud Admin |
| POA-007 | Account attribute hygiene for all users | Identity Admin |
| POA-009 | Develop and approve Incident Response Plan | GRC Analyst |
| POA-010 | Build risk register from vulnerability findings | GRC Analyst |
| POA-011 | Establish internal audit program and management review | GRC Analyst |
| POA-012 | Third-party inventory; supplier security requirements | GRC Analyst |
| POA-015 | Document formal risk management methodology | GRC Analyst |
| POA-016 | Network security documentation; CVE remediation | Cloud Admin |

### 91+ Days: Medium Priority (Program Maturation)

| POA&M ID | Item | Owner |
|---|---|---|
| POA-014 | Develop System Security Plan | GRC Analyst |
| POA-017 | Formalize cybersecurity budget and program charter | CISO / Leadership |
| POA-018 | Training completion tracking implementation | HR / GRC |
| POA-019 | Document RTO/RPO; conduct recovery plan testing | IT / Cloud Admin |
| POA-020 | Produce Statement of Applicability (all 93 Annex A controls) | GRC Analyst |

---

## Framework Coverage Reference

| POA&M ID | NIST 800-53 | NIST CSF 2.0 | ISO 27001 | SOC 2 TSC |
|---|---|---|---|---|
| POA-001 | IA-2, IA-2(1), IA-2(2) | PR.AA | A 8.5 | CC6.1, CC6.3 |
| POA-002 | AC-6, IA-2, AC-2 | PR.AA | A 8.2 | CC6.2, CC6.3 |
| POA-003 | SI-2, RA-5 | PR.PS, ID.RA | A 8.8 | CC7.1, CC4.1 |
| POA-004 | SI-3 | PR.IR | A 8.7 | CC6.8 |
| POA-005 | AU-6, IR-4, SI-3 | DE.AE | A 8.16 | CC7.2, CC7.3 |
| POA-006 | CM-2, CM-6 | PR.PS | A 8.9 | CC7.1 |
| POA-007 | AC-2, AC-6 | ID.AM | A 8.3 | CC6.2 |
| POA-008 | AC-2, AU-6 | PR.AA | A 8.5 | CC6.1, CC6.7 |
| POA-009 | IR-8, IR-4 | RS.MA | A 5.26 | CC7.3, CC7.4 |
| POA-010 | RA-3, RA-5 | GV.RM, ID.RA | Clause 6.1.2 | CC3.2 |
| POA-011 | CA-2, CA-7 | GV.OV | Clause 9.2 | CC4.1 |
| POA-012 | SA-9, SR-3 | GV.SC | A 5.19, A 5.20 | CC9.2 |
| POA-013 | AU-2, AU-6 | DE.CM | A 8.15 | CC7.2 |
| POA-014 | PL-2, CA-9 | GV.PO | Clause 4.3 | CC2.1 |
| POA-015 | PM-9, RA-3 | GV.RM | Clause 6.1 | CC3.1 |
| POA-016 | SC-7, SC-8 | PR.PS | A 8.20 | CC6.6 |
| POA-017 | PM-1, PM-2 | GV.OC | Clause 5.1 | CC1.1 |
| POA-018 | AT-2, PM-13 | PR.AT | A 6.3 | CC2.2 |
| POA-019 | CP-2, CP-9 | RC.RP | A 8.13, A 5.30 | A1.2 |
| POA-020 | PL-2 | GV.PO | Clause 6.1.3 | CC2.1 |

---

*End of Document*

**Classification: CONFIDENTIAL**
**Assessment Date: March 5, 2026**
**Organization: ABC Corp | Microsoft Entra ID Tenant**

*This POA&M was produced from a real gap assessment conducted against a live Azure cloud environment. All organization names, account identifiers, and domain references have been sanitized. This document is published for portfolio and educational purposes only.*
