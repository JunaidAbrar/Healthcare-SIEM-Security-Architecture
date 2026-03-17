# Healthcare Security and Compliance

**Document:** Healthcare Security and Compliance Research — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Overview

This document outlines the regulatory and compliance landscape applicable to Bedminster Health as a large healthcare organisation operating in Australia. It explains the key frameworks that govern information security and privacy obligations, and describes how the Microsoft Sentinel SIEM implementation supports compliance with these requirements.

---

## 2. Healthcare Security Compliance Landscape

Healthcare organisations face a complex and overlapping set of regulatory obligations governing the collection, storage, access, and protection of patient and personal information. Non-compliance with these obligations can result in regulatory penalties, mandatory public notification of data breaches, civil liability, and significant reputational damage.

For Bedminster Health, the primary compliance frameworks are:

---

## 3. Key Regulatory Frameworks

### **ISO/IEC 27001**

ISO/IEC 27001 is the internationally recognised standard for Information Security Management Systems (ISMS). It provides a systematic framework for establishing, implementing, maintaining, and continually improving an organisation's approach to managing information security risk.

**Core Structure:**  
ISO/IEC 27001 is structured around a risk-based approach, requiring organisations to:

- Identify information assets and assess the risks to their confidentiality, integrity, and availability.
- Implement a comprehensive set of security controls from Annex A, selected based on risk assessment.
- Operate a management system to govern, review, and improve security controls over time.
- Undergo independent third-party certification audits to demonstrate compliance.

**Relevance to Bedminster Health:**  
Certification to ISO/IEC 27001 would demonstrate that Bedminster Health has implemented a structured, risk-managed approach to information security across its entire organisation. Key control areas directly relevant to Bedminster Health include:

| ISO/IEC 27001 Control Area | Relevance |
|---|---|
| A.8 — Asset Management | Inventory of servers, workstations, medical devices, and data assets |
| A.9 — Access Control | User authentication, privileged access management, EMR access controls |
| A.10 — Cryptography | Encryption of patient data at rest and in transit |
| A.12 — Operations Security | Log management, malware protection, vulnerability management |
| A.16 — Information Security Incident Management | Incident response procedures, evidence preservation |
| A.18 — Compliance | Monitoring of compliance with legal and regulatory obligations |

**SIEM Alignment with ISO/IEC 27001:**  
Microsoft Sentinel directly supports multiple ISO/IEC 27001 control requirements:

- **Logging and monitoring (A.12.4):** Centralised log collection and review capability.
- **Event logging (A.12.4.1):** Automated collection of security events across all systems.
- **Protection of log information (A.12.4.2):** Immutable log storage in Azure, protected from modification.
- **Administrator and operator logs (A.12.4.3):** Privileged account activity monitoring.
- **Incident management (A.16.1.2):** Incident detection, classification, and response workflow.
- **Evidence collection (A.16.1.7):** Preservation of forensic evidence through centralised, tamper-evident log storage.

---

### **Australian Privacy Act 1988**

The Privacy Act 1988 (Cth) is the principal Australian legislation governing the handling of personal information by Australian Government agencies and private sector organisations with an annual turnover exceeding $3 million. Healthcare providers of any size are specifically covered regardless of turnover.

**Australian Privacy Principles (APPs):**  
The Privacy Act 1988 is operationalised through 13 Australian Privacy Principles (APPs) that govern how personal information must be collected, held, used, disclosed, and destroyed. The most relevant APPs for Bedminster Health's security programme are:

| APP | Title | Relevance |
|---|---|---|
| APP 1 | Open and transparent management | Documented security policies and procedures |
| APP 6 | Use or disclosure of personal information | Controls preventing unauthorised access to patient records |
| APP 11 | Security of personal information | Technical and organisational measures protecting personal information from misuse, loss, or unauthorised access |
| APP 12 | Access to personal information | Audit trails of who accessed patient information |

**APP 11 — Security of Personal Information:**  
APP 11 requires organisations to take reasonable steps to protect personal information from:
- Misuse
- Interference
- Loss
- Unauthorised access
- Modification
- Disclosure

"Reasonable steps" is interpreted contextually based on the nature and sensitivity of the information held, the potential harm from a breach, and the cost of security measures relative to the risk. For a healthcare organisation holding sensitive health information on thousands of patients, reasonable steps include robust technical controls including centralised security monitoring.

**Mandatory Data Breach Notification Scheme (Notifiable Data Breaches):**  
The Notifiable Data Breaches (NDB) scheme under Part IIIC of the Privacy Act requires organisations to notify:
- The Office of the Australian Information Commissioner (OAIC)
- Affected individuals

...when an eligible data breach occurs. An eligible data breach is one where:
1. There has been unauthorised access to, or disclosure of, personal information held by the organisation.
2. The breach is likely to result in serious harm to individuals whose information is involved.

Penalties for failure to comply with the NDB scheme can include civil penalties of up to $50 million for serious or repeated breaches (following the Privacy Legislation Amendment (Enhancing Online Privacy and Other Measures) Bill 2021 strengthening).

**SIEM Alignment with the Australian Privacy Act:**  
Microsoft Sentinel directly supports Privacy Act compliance in the following ways:

| Privacy Act Requirement | SIEM Capability |
|---|---|
| Protect personal information from unauthorised access (APP 11) | Real-time detection of unauthorised access to EMR and patient data systems |
| Detect data breaches promptly | Automated alerting on mass data access, unusual authentication, and exfiltration indicators |
| Notify of eligible breaches within 30 days of becoming aware | SIEM evidence supports rapid investigation to determine breach eligibility |
| Maintain audit trails | Centralised, tamper-evident log storage provides audit evidence |
| Demonstrate reasonable security measures | SIEM implementation evidences a systematic, technical approach to security monitoring |

---

## 4. Additional Relevant Frameworks

### 4.1 My Health Records Act 2012

The My Health Records Act 2012 (Cth) governs the management of health information stored in the My Health Record system. Healthcare providers accessing or contributing to My Health Records must implement security controls to protect that information, including access controls and audit logging.

**SIEM Relevance:** Audit logs of access to systems that connect to My Health Records, captured by Sentinel, provide evidence for compliance with My Health Records access requirements.

---

### 4.2 Australian Government Information Security Manual (ISM)

The ISM, published by the Australian Cyber Security Centre (ACSC), provides a comprehensive set of cybersecurity controls for Australian government organisations and is widely adopted as a reference framework by critical infrastructure and healthcare organisations.

Key ISM controls relevant to Bedminster Health's SIEM programme include:

| ISM Control Category | Relevance |
|---|---|
| Guidelines for System Monitoring | Centralised log collection and SIEM-based monitoring requirements |
| Guidelines for Event Logging and Auditing | Mandatory logging of authentication, privileged access, and configuration changes |
| Guidelines for Incident Response | Detection and response capability requirements |

---

### 4.3 ACSC Essential Eight

The ACSC Essential Eight is a prioritised set of mitigation strategies designed to reduce the likelihood of successful cyber attacks. While not a regulatory requirement, it represents the baseline standard for cybersecurity in Australian organisations.

| Essential Eight Strategy | SIEM Contribution |
|---|---|
| Application Control | Sentinel can detect unauthorised application execution (Event ID 4688) |
| Patch Applications | SIEM monitoring of vulnerability exploitation attempts |
| Configure Microsoft Office Macro Settings | Sentinel alerts on macro execution from email attachments |
| User Application Hardening | Monitoring for exploitation of hardened settings |
| Restrict Administrative Privileges | Privileged access monitoring and escalation alerts |
| Patch Operating Systems | Vulnerability exploitation detection |
| Multi-Factor Authentication | MFA bypass and anomalous authentication detection |
| Regular Backups | Detection of backup deletion (ransomware indicator) |

---

## 5. Compliance Summary

The following table summarises how Microsoft Sentinel supports each compliance framework.

| Framework | Key Obligation | SIEM Support |
|---|---|---|
| ISO/IEC 27001 | ISMS, logging, incident management | Centralised logging, incident detection and management, audit evidence |
| Australian Privacy Act 1988 (APP 11) | Protect personal information | Real-time detection of unauthorised access, breach notification support |
| Notifiable Data Breaches Scheme | Detect and notify eligible breaches | Rapid detection, investigation support, audit evidence |
| My Health Records Act 2012 | Audit access to health records | Audit trail of access to connected systems |
| ACSC ISM | Centralised monitoring, event logging | Comprehensive log collection and real-time monitoring |
| ACSC Essential Eight | Multi-control security baseline | Detection support for multiple Essential Eight controls |

---

## 6. Compliance Monitoring in Sentinel

Microsoft Sentinel includes built-in compliance workbooks that map detected events and controls to regulatory frameworks:

- **ISO/IEC 27001 Workbook** — Maps Sentinel coverage to ISO 27001 control areas, supporting audit evidence generation.
- **ACSC ISM Workbook** — Aligns monitoring activity to ISM control requirements.
- **Custom Compliance Dashboards** — Can be built to track specific APP 11 and NDB scheme obligations.

These workbooks enable the compliance team to demonstrate regulatory alignment to internal auditors, the OAIC, and external certification bodies, reducing the administrative overhead of compliance reporting.
