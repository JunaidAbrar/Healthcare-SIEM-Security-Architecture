# Threat Model

**Document:** Threat Model — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Purpose

This document identifies and assesses the principal threats facing Bedminster Health's infrastructure and patient data. The threat model informs the detection strategy and SIEM use case development, ensuring that the security monitoring capability is aligned to the organisation's realistic threat landscape.

---

## 2. Threat Landscape Overview

Healthcare organisations face threats from a broad range of adversary types, including financially motivated cybercriminal groups, nation-state actors, and malicious or negligent insiders. The following factors increase Bedminster Health's threat exposure:

- **High-value data:** Electronic Medical Records (EMR) and patient health information command high prices on criminal marketplaces.
- **Operational criticality:** Disruption to hospital operations carries life-safety consequences, increasing pressure to comply with ransom demands.
- **Large attack surface:** Over 2,000 workstations, 150 servers, and multiple network-connected medical devices distributed across six sites.
- **Complex access model:** Thousands of staff with varying levels of system access, including contractors, agency staff, and clinical vendors.
- **Legacy systems:** Clinical environments often include legacy operating systems and unpatched medical devices that cannot be updated without vendor approval.

---

## 3. Threat Identification

### 3.1 Ransomware Attacks

Ransomware represents the highest-impact threat to healthcare organisations. Adversaries encrypt critical systems and demand payment for decryption keys, often threatening to publish stolen data publicly. Healthcare networks are particularly susceptible due to their operational urgency and historical underinvestment in security controls.

**Attack pattern:**
1. Initial access via phishing email or exploitation of internet-facing vulnerability.
2. Credential harvesting and lateral movement to identify high-value targets.
3. Data exfiltration before encryption.
4. Mass deployment of ransomware payload across the network.

**Relevant systems:** All endpoints, file servers, EMR systems, backup infrastructure.

---

### 3.2 Insider Threats

Healthcare staff have legitimate access to sensitive patient data as part of their roles. This access creates risk from both malicious insiders (deliberate data theft or sabotage) and negligent insiders (accidental data exposure, policy violations).

**Attack pattern (malicious insider):**
- Accessing patient records beyond the scope of their role.
- Exfiltrating data via email, USB devices, or cloud storage.
- Modifying or deleting records.

**Attack pattern (negligent insider):**
- Opening phishing emails or malicious attachments.
- Using weak passwords or sharing credentials.
- Connecting unauthorised devices to the network.

**Relevant systems:** EMR, clinical workstations, email, administrative systems.

---

### 3.3 Credential Compromise

Compromised user credentials allow attackers to authenticate as legitimate users, bypassing perimeter controls. Credentials may be obtained through phishing, credential stuffing, password spraying, or purchase from criminal marketplaces.

**Attack pattern:**
1. Attacker obtains valid credentials via phishing or credential stuffing.
2. Logs in to VPN, web portal, or internal system using stolen credentials.
3. Conducts reconnaissance and escalates privileges.
4. Accesses sensitive systems and data while appearing as a legitimate user.

**Relevant systems:** Active Directory, VPN, email, web-facing portals, EMR.

---

### 3.4 Lateral Movement

Following initial access, attackers traverse the internal network to reach high-value targets such as domain controllers, backup servers, and clinical databases. Healthcare networks frequently have flat or under-segmented architectures that facilitate lateral movement.

**Attack pattern:**
- Pass-the-hash or pass-the-ticket attacks using harvested credentials.
- Remote execution using tools such as PsExec, WMI, or PowerShell remoting.
- Exploitation of unpatched vulnerabilities on internal systems.

**Relevant systems:** Active Directory, Windows servers, domain controllers, network segments.

---

### 3.5 Medical Device Exploitation

Network-connected medical devices (infusion pumps, patient monitors, imaging equipment) often run embedded operating systems that cannot be patched and have limited security controls. These devices represent a growing attack surface in healthcare environments.

**Attack pattern:**
- Exploitation of known vulnerabilities in device firmware or embedded OS.
- Use of compromised device as a network pivot point.
- Disruption of device operation with patient safety implications.

**Relevant systems:** IoT/medical devices, clinical networks.

---

### 3.6 Unauthorised Access to Patient Data

Deliberate or accidental access to patient records outside of legitimate clinical need represents both a security incident and a regulatory breach under the Australian Privacy Act 1988 and related health records legislation.

**Attack pattern:**
- Staff accessing records of colleagues, public figures, or family members.
- External attacker obtaining access to EMR via compromised credentials.
- Misconfigured systems exposing patient data without authentication.

**Relevant systems:** EMR, patient portals, clinical databases, file shares.

---

## 4. Risk Assessment

The following risk table assesses each identified threat based on likelihood (probability of occurrence) and impact (severity of consequences if the threat materialises).

**Rating Scale:**

| Rating | Likelihood | Impact |
|---|---|---|
| 1 | Very Unlikely | Negligible |
| 2 | Unlikely | Minor |
| 3 | Possible | Moderate |
| 4 | Likely | Significant |
| 5 | Very Likely | Critical |

**Risk = Likelihood × Impact**

| # | Threat | Likelihood | Impact | Risk Score | Risk Level |
|---|---|---|---|---|---|
| T1 | Ransomware Attack | 4 | 5 | 20 | **Critical** |
| T2 | Insider Threat (Malicious) | 3 | 4 | 12 | **High** |
| T3 | Insider Threat (Negligent) | 5 | 3 | 15 | **High** |
| T4 | Credential Compromise | 4 | 4 | 16 | **Critical** |
| T5 | Lateral Movement | 3 | 5 | 15 | **High** |
| T6 | Medical Device Exploitation | 3 | 4 | 12 | **High** |
| T7 | Unauthorised Patient Data Access | 4 | 4 | 16 | **Critical** |
| T8 | Phishing / Social Engineering | 5 | 3 | 15 | **High** |
| T9 | Denial of Service (Clinical Systems) | 2 | 5 | 10 | **High** |
| T10 | Supply Chain / Third-Party Compromise | 3 | 4 | 12 | **High** |

---

## 5. Threat-to-Control Mapping

The following table maps each identified threat to the primary SIEM detection capability that addresses it.

| Threat | SIEM Detection Capability |
|---|---|
| Ransomware Attack | Mass file modification alerts, volume shadow copy deletion, lateral movement detection |
| Insider Threat | User behaviour analytics (UEBA), after-hours access alerts, data exfiltration monitoring |
| Credential Compromise | Failed authentication monitoring, impossible travel detection, unusual login location alerts |
| Lateral Movement | SMB scanning detection, remote execution alerts, east-west traffic anomalies |
| Medical Device Exploitation | Device network anomaly detection, unexpected outbound connections |
| Unauthorised Patient Data Access | EMR access auditing, bulk record access alerts, privileged access monitoring |
| Phishing / Social Engineering | Email security integration, credential harvesting site detection |
| Denial of Service | Network traffic volume anomalies, service availability monitoring |
| Supply Chain Compromise | Third-party access monitoring, anomalous process execution alerts |

---

## 6. Assumptions

- Threat likelihood ratings are based on the Australian healthcare threat landscape informed by ACSC threat reports and publicly available incident data.
- Internal threat actors are assumed to have varying levels of technical capability.
- The organisation has no current advanced persistent threat (APT) activity known at the time of this assessment.
- Medical device inventory is assumed to be partially undocumented, consistent with typical healthcare environments.
