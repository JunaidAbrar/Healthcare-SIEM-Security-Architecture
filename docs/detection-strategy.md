# Detection Strategy

**Document:** SIEM Detection Strategy — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Overview

This document defines the detection strategy for the Microsoft Sentinel deployment at Bedminster Health. It describes the primary use cases for security monitoring, the correlation logic used to detect each threat scenario, and the alerting and triage approach.

The detection use cases are aligned to the threat model documented in [threat-model.md](threat-model.md) and mapped to MITRE ATT&CK techniques where applicable.

---

## 2. Detection Engineering Principles

The following principles guide the development and operation of detection rules at Bedminster Health:

**Signal over noise:** Detection rules are tuned to minimise false positives. High-volume, low-fidelity rules that generate excessive alerts without actionable context degrade SOC effectiveness. Rules are reviewed and tuned regularly.

**Layered detection:** No single detection rule should be relied upon to catch a threat. Multiple overlapping detections covering different stages of an attack chain improve detection probability.

**MITRE ATT&CK alignment:** Use cases are mapped to MITRE ATT&CK tactics and techniques, enabling consistent classification of threats and alignment with industry frameworks.

**Severity tiering:** Alerts are assigned severity levels (Critical, High, Medium, Low) based on the risk they represent, enabling the SOC team to prioritise triage effort.

**Documentation:** Each use case includes description, data requirements, detection logic, and expected response actions.

---

## 3. Detection Use Cases

---

### UC-001: Brute Force Login Attempts

**Description:**  
Detects repeated failed authentication attempts against user accounts, indicative of a brute force or password spray attack. High volumes of failed logins from a single source or against multiple accounts within a short timeframe are flagged.

**MITRE ATT&CK Mapping:**  
- Tactic: Credential Access (TA0006)  
- Technique: Brute Force — Password Spraying (T1110.003)

**Data Sources Required:**  
- Windows Security Event Log: Event ID 4625 (Failed Logon)
- Azure Active Directory Sign-in Logs
- VPN Authentication Logs

**Detection Logic:**  
Count failed authentication events (EventID 4625) per source IP address within a 10-minute window. Alert if count exceeds 10 failures from a single source, or if failures are distributed across more than 5 distinct accounts from the same source (password spray pattern).

```kusto
SecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count(), DistinctAccounts = dcount(TargetAccount) 
    by IpAddress, bin(TimeGenerated, 10m)
| where FailedAttempts > 10 or DistinctAccounts > 5
| project TimeGenerated, IpAddress, FailedAttempts, DistinctAccounts
```

**Severity:** High  
**Response:** Investigate source IP, determine if legitimate (e.g., misconfigured service account) or malicious. Block source IP at firewall if confirmed attack. Reset compromised accounts.

---

### UC-002: Privilege Escalation Events

**Description:**  
Detects events indicating a user account has been added to a privileged group (such as Domain Admins or Local Administrators), or that privilege escalation tools or techniques have been used. Privilege escalation is a common step in lateral movement and ransomware attack chains.

**MITRE ATT&CK Mapping:**  
- Tactic: Privilege Escalation (TA0004)  
- Technique: Valid Accounts — Domain Accounts (T1078.002), Abuse Elevation Control Mechanism (T1548)

**Data Sources Required:**  
- Windows Security Event Log: Event ID 4728 (Member Added to Security-Enabled Global Group), 4732 (Member Added to Security-Enabled Local Group), 4756 (Member Added to Universal Group)
- Active Directory Audit Logs

**Detection Logic:**  
Alert when any account is added to a sensitive group (Domain Admins, Enterprise Admins, Local Administrators, Backup Operators) outside of an approved change window. Correlate with watchlist of privileged groups.

```kusto
SecurityEvent
| where EventID in (4728, 4732, 4756)
| where TargetUserName in~ ("Domain Admins", "Enterprise Admins", "Administrators", "Backup Operators")
| project TimeGenerated, SubjectAccount, MemberName, TargetUserName, Computer
```

**Severity:** Critical  
**Response:** Immediately verify change with IT change management. If unauthorised, remove account from group, investigate how escalation occurred, and initiate incident response.

---

### UC-003: Suspicious Lateral Movement

**Description:**  
Detects patterns indicative of lateral movement across the internal network, including remote execution via common administrative protocols, Mimikatz-style credential dumping, and anomalous SMB connections between workstations.

**MITRE ATT&CK Mapping:**  
- Tactic: Lateral Movement (TA0008)  
- Technique: Remote Services — SMB/Windows Admin Shares (T1021.002), Lateral Tool Transfer (T1570)

**Data Sources Required:**  
- Windows Security Event Log: Event ID 4624 (Successful Logon, Logon Type 3), Event ID 4648 (Explicit Credential Logon)
- Process Creation Events: Event ID 4688 (with command-line auditing enabled)
- Network Firewall Logs (SMB traffic, TCP 445)

**Detection Logic:**  
Alert on logon type 3 (network logon) connections to multiple distinct hosts from a single source account within a short timeframe, particularly where the source is a workstation rather than a server. Also alert on execution of known lateral movement tools (PsExec, WMIC remoting).

```kusto
SecurityEvent
| where EventID == 4624 and LogonType == 3
| summarize DistinctTargetHosts = dcount(Computer) by SubjectUserName, bin(TimeGenerated, 30m)
| where DistinctTargetHosts > 5 and SubjectUserName !endswith "$"
| project TimeGenerated, SubjectUserName, DistinctTargetHosts
```

**Severity:** High  
**Response:** Investigate the source account and host for signs of compromise. Review process execution history. Isolate affected endpoints if compromise is confirmed.

---

### UC-004: Unusual Authentication Behaviour

**Description:**  
Detects authentication patterns that deviate significantly from a user's established baseline, including logins from new geographic locations, logins outside of normal business hours, logins from new devices, and impossible travel scenarios (login from two geographically distant locations in an implausibly short time).

**MITRE ATT&CK Mapping:**  
- Tactic: Initial Access (TA0001)  
- Technique: Valid Accounts (T1078)

**Data Sources Required:**  
- Azure Active Directory Sign-in Logs
- Microsoft Sentinel UEBA (entity behaviour baseline)

**Detection Logic:**  
Microsoft Sentinel UEBA provides built-in behavioural analytics that detect anomalous authentication automatically. Custom rules supplement UEBA with explicit detection logic, including impossible travel detection based on sign-in location and time delta.

```kusto
SigninLogs
| where ResultType == 0  // Successful sign-in
| summarize Locations = make_set(Location), Count = count() by UserPrincipalName, bin(TimeGenerated, 1h)
| where array_length(Locations) > 1
| mv-expand Location = Locations
| project TimeGenerated, UserPrincipalName, Locations, Count
```

**Severity:** Medium–High (severity escalated if combined with other indicators)  
**Response:** Contact user to verify authentication. If user did not authenticate, assume account compromise — initiate credential reset, review recent activity, and escalate to incident response.

---

### UC-005: Potential Ransomware Activity

**Description:**  
Detects indicators associated with ransomware preparation and execution phases, including mass file modifications (encryption), deletion of volume shadow copies, and disabling of backup services. These actions are characteristic of the final stages of a ransomware attack chain.

**MITRE ATT&CK Mapping:**  
- Tactic: Impact (TA0040)  
- Technique: Data Encrypted for Impact (T1486), Inhibit System Recovery (T1490)

**Data Sources Required:**  
- Windows Security Event Log: Event ID 4688 (Process Creation)
- Microsoft Defender for Endpoint alerts
- File access audit logs (where file auditing is enabled)

**Detection Logic:**  
Alert on execution of commands known to be used in ransomware preparation: deletion of shadow copies via `vssadmin`, `wbadmin`, or `wmic shadowcopy delete`; disabling of backup services via `sc stop` or `net stop`; and mass file rename events within a short timeframe.

```kusto
SecurityEvent
| where EventID == 4688
| where CommandLine has_any ("vssadmin delete shadows", "wmic shadowcopy delete", 
    "bcdedit /set {default} recoveryenabled No", "wbadmin delete catalog")
| project TimeGenerated, Computer, SubjectUserName, CommandLine, ParentProcessName
```

**Severity:** Critical  
**Response:** Immediate escalation — this is a P1 incident. Isolate affected systems from the network immediately using Defender for Endpoint network isolation. Activate the incident response plan. Do not reboot systems before forensic imaging. Engage CISO and executive leadership.

---

### UC-006: Mass Data Access — Potential Insider Threat

**Description:**  
Detects unusually high volumes of patient record access by a single user account within a short timeframe, which may indicate bulk data exfiltration by an insider threat or a compromised account accessing the EMR system.

**MITRE ATT&CK Mapping:**  
- Tactic: Collection (TA0009)  
- Technique: Data from Information Repositories (T1213)

**Data Sources Required:**  
- EMR application audit logs
- Microsoft Sentinel UEBA entity baseline

**Detection Logic:**  
Alert when a user accesses significantly more patient records than their established baseline within a defined time window. Threshold is set based on role-specific baselines (e.g., a GP accessing 50+ records in an hour versus their typical 5–10 would trigger an alert).

**Severity:** High  
**Response:** Review accessed records against the user's clinical workload. Contact the user's line manager. If exfiltration is suspected, suspend the account and preserve evidence. Notify the Privacy Officer given the potential for a notifiable data breach under the Australian Privacy Act.

---

## 4. Detection Rule Summary

| ID | Use Case | Severity | MITRE Tactic | Data Source |
|---|---|---|---|---|
| UC-001 | Brute Force Login Attempts | High | Credential Access | Windows Event Log, AAD Sign-ins |
| UC-002 | Privilege Escalation Events | Critical | Privilege Escalation | Windows Event Log, AD Audit |
| UC-003 | Suspicious Lateral Movement | High | Lateral Movement | Windows Event Log, Firewall |
| UC-004 | Unusual Authentication Behaviour | Medium–High | Initial Access | AAD Sign-ins, UEBA |
| UC-005 | Potential Ransomware Activity | Critical | Impact | Windows Event Log, Defender |
| UC-006 | Mass Data Access | High | Collection | EMR Audit Logs, UEBA |

---

## 5. Alert Tuning and Maintenance

Detection rules require ongoing maintenance to remain effective:

- **False positive review:** Newly deployed rules are monitored for false positive rate in the first 30 days and tuned accordingly.
- **Threshold adjustment:** Thresholds are reviewed quarterly based on observed activity patterns and seasonal variations.
- **New use case development:** Threat intelligence feeds and incident findings inform the development of new use cases.
- **MITRE ATT&CK coverage review:** The detection library is reviewed annually against the MITRE ATT&CK framework to identify coverage gaps.
- **Rule versioning:** All rule changes are documented in a change log with version numbers and rationale.
