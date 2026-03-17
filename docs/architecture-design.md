# Architecture Design

**Document:** SIEM Architecture Design — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Architecture Overview

The proposed SIEM architecture for Bedminster Health is a cloud-native, multi-site log aggregation and security analytics platform built on Microsoft Sentinel. The architecture is designed to provide comprehensive, real-time visibility across all infrastructure components at all sites while remaining operationally manageable for the organisation's security team.

The architecture is structured into five logical layers:

1. **Log Sources** — The infrastructure components generating security events.
2. **Log Collection** — Agents and connectors that forward events from sources to the aggregation layer.
3. **Log Aggregation** — The centralised repository that receives, stores, and indexes all log data.
4. **SIEM Analytics** — The detection and correlation engine that analyses log data for threats.
5. **SOC Operations** — The monitoring, alerting, and incident response workflows used by the security team.

---

## 2. Log Sources

### 2.1 Server Infrastructure

Bedminster Health operates approximately 150 servers across all sites, including:

- **Windows Server** — Active Directory domain controllers, file servers, application servers, print servers.
- **Linux Servers** — Web servers, database servers, application middleware.
- **Virtualisation Hosts** — VMware vSphere or Hyper-V hypervisors hosting virtualised workloads.

**Log Types Collected:**
- Windows Security Event Log (authentication, privilege use, object access, process creation)
- Windows System and Application Event Logs
- Active Directory audit logs (account changes, group policy changes, privileged account activity)
- Linux syslog and auth.log
- Hypervisor management events

---

### 2.2 Workstations

Approximately 2,000 workstations are deployed across Bedminster Health's sites, including clinical workstations in wards and consulting rooms, and administrative workstations in support areas.

**Log Types Collected:**
- Windows Security Event Log (logon/logoff, failed authentication, privilege escalation)
- Microsoft Defender Antivirus and Endpoint Protection alerts
- PowerShell script block logging
- Process creation and command-line auditing

---

### 2.3 Firewalls

Perimeter and internal firewalls generate network traffic logs providing visibility into traffic between network segments and to/from the internet.

**Log Types Collected:**
- Firewall allow/deny logs
- Intrusion Prevention System (IPS) alerts
- VPN authentication and connection logs
- NAT translation logs

**Supported Vendors:** Palo Alto Networks, Fortinet, Cisco ASA, Check Point (via CEF/Syslog)

---

### 2.4 Routers and Network Devices

Enterprise routers and switches generate logs recording routing events, interface state changes, and authentication to network management systems.

**Log Types Collected:**
- Syslog (routing events, interface state, OSPF/BGP adjacency changes)
- NetFlow / IPFIX (network traffic metadata for anomaly detection)
- Authentication logs (TACACS+ / RADIUS for device management access)

---

### 2.5 Medical Devices

Network-connected medical devices including patient monitors, infusion pumps, imaging systems, and other clinical equipment generate varying levels of log data. Given vendor constraints, direct agent deployment is not possible on most devices.

**Log Collection Approach:**
- Network-level monitoring via NetFlow and firewall logs for device traffic patterns.
- Where supported, Syslog forwarding from device management systems.
- Clinical network segment monitoring via network tap or SPAN port for anomaly detection.

---

### 2.6 Cloud and Application Sources

- **Microsoft 365** — Exchange Online, SharePoint, Teams, OneDrive audit logs.
- **Azure Active Directory** — Sign-in logs, audit logs, conditional access events, identity protection alerts.
- **Microsoft Defender for Endpoint** — Endpoint detection and response (EDR) alerts.
- **Clinical Applications** — EMR and patient management system audit logs (where vendor-supported).

---

## 3. Log Collection Layer

### 3.1 Azure Monitor Agent (AMA)

The **Azure Monitor Agent** is the primary log collection agent for Windows and Linux endpoints, deployed via Group Policy or Microsoft Endpoint Configuration Manager (MECM).

- Deployed to all Windows servers and workstations.
- Deployed to Linux servers.
- Configured via Data Collection Rules (DCRs) defining which event channels and log types to forward.
- Communicates with the Log Analytics Workspace over HTTPS (TCP 443).

### 3.2 Syslog Forwarding (Linux Log Forwarder)

Network devices, firewalls, and other syslog-capable systems forward logs to a dedicated **Linux-based log forwarding server** deployed in each site's DMZ or server room. This server:

- Receives Syslog (UDP/TCP 514) and CEF (port 514/25226) from network devices and firewalls.
- Forwards events to the Log Analytics Workspace using the AMA agent.
- Acts as a protocol translator between legacy syslog formats and the Log Analytics ingestion API.

**Recommended sizing:** 2 vCPU, 4 GB RAM per site, supporting up to ~10,000 EPS (events per second).

### 3.3 Microsoft 365 and Azure Data Connectors

Microsoft Sentinel's native data connectors handle cloud log ingestion without requiring on-premises agents:

- **Microsoft 365 Connector** — Ingests Office 365 unified audit log.
- **Azure Active Directory Connector** — Ingests sign-in and audit logs.
- **Microsoft Defender for Endpoint Connector** — Ingests EDR alerts and device events.
- **Azure Activity Connector** — Ingests Azure subscription management events.

---

## 4. Log Aggregation Layer

### 4.1 Azure Log Analytics Workspace

All log data is ingested into a centralised **Azure Log Analytics Workspace** deployed in the Australia East region to meet data sovereignty requirements.

- **Retention:** Hot (interactive query) retention of 90 days; archive tier for 1–7 years to meet audit retention requirements.
- **Access Control:** Role-based access control (RBAC) limits data access to authorised security personnel.
- **Data ingestion:** Direct ingestion from AMA agents, Syslog forwarders, and cloud data connectors.
- **Encryption:** All data encrypted at rest (AES-256) and in transit (TLS 1.2+).

### 4.2 Workspace Structure

A single workspace is recommended for Bedminster Health to enable cross-source correlation. If data sensitivity requirements demand separation (e.g., isolating clinical system logs), a secondary workspace with workspace-level access controls can be established.

---

## 5. SIEM Analytics Layer

### 5.1 Microsoft Sentinel

Microsoft Sentinel is deployed as an overlay on the Log Analytics Workspace, providing:

- **Analytics Rules** — Scheduled and near-real-time queries detecting known threat patterns.
- **UEBA** — User and Entity Behaviour Analytics building behavioural baselines and detecting deviations.
- **Threat Intelligence** — Microsoft Threat Intelligence feed integrated by default; custom TI feeds can be added.
- **Watchlists** — Custom reference data (e.g., privileged account lists, sensitive server lists) used in detection rules.
- **Workbooks** — Interactive dashboards providing operational visibility and compliance reporting.

### 5.2 Detection Logic

Detection rules are implemented as KQL (Kusto Query Language) queries scheduled to run at defined intervals (typically 5–60 minutes for near-real-time detection). Alerts generated by analytics rules create **Incidents** in Sentinel for SOC triage.

See [Detection Strategy](detection-strategy.md) for detailed use case specifications.

---

## 6. SOC Operations Layer

### 6.1 Incident Management

When an analytics rule triggers, Sentinel creates an **Incident** containing:

- Alert details and associated events.
- Mapped MITRE ATT&CK techniques.
- Entities involved (accounts, hosts, IP addresses).
- Severity rating (High, Medium, Low, Informational).

SOC analysts triage incidents through the Sentinel Incidents queue, investigating using built-in investigation graphs and hunting queries.

### 6.2 Dashboards and Workbooks

Sentinel Workbooks provide pre-built and custom dashboards for:

- Real-time security operations monitoring.
- Authentication and identity analytics.
- Network traffic analysis.
- Compliance reporting (ISO 27001, Privacy Act).
- Threat intelligence overview.

### 6.3 Automated Response (SOAR)

**Azure Logic Apps** (Sentinel Playbooks) enable automated response actions when incidents are triggered:

- Notify the on-call security analyst via email or Microsoft Teams.
- Block a compromised user account in Azure Active Directory.
- Isolate an endpoint via Microsoft Defender for Endpoint.
- Create a ticket in the ITSM system.
- Enrich incident with threat intelligence lookups.

### 6.4 Incident Response Workflow

```
Incident Created (Sentinel)
        ↓
Automated Triage
(Playbook enrichment, UEBA score, TI match)
        ↓
SOC Analyst Assignment
        ↓
Investigation
(Sentinel investigation graph, hunting queries, log search)
        ↓
Containment Action
(Automated via Playbook or manual by analyst)
        ↓
Eradication and Recovery
(IT and clinical operations teams)
        ↓
Incident Closure
(Documentation, lessons learned, rule tuning)
```

---

## 7. Network Architecture Considerations

### 7.1 Log Forwarding Network Requirements

| Traffic Type | Protocol | Port | Direction |
|---|---|---|---|
| AMA agent to Log Analytics Workspace | HTTPS | TCP 443 | Endpoint → Azure |
| Syslog/CEF to log forwarder | Syslog/CEF | UDP/TCP 514 | Device → Log Forwarder |
| Log forwarder to Log Analytics Workspace | HTTPS | TCP 443 | Log Forwarder → Azure |
| Microsoft 365 audit logs | HTTPS (API) | TCP 443 | Azure → Azure |

### 7.2 Bandwidth Estimation

| Site | Estimated EPS | Estimated Daily Volume |
|---|---|---|
| Main Hospital | ~2,000 EPS | ~15 GB/day |
| Satellite Hospital (×4) | ~500 EPS each | ~4 GB/day each |
| Outpatient Clinics | ~200 EPS total | ~1.5 GB/day |
| Cloud Sources | Varies | ~2 GB/day |
| **Total (estimated)** | | **~34 GB/day** |

These estimates should be validated during the pilot phase and used to select the appropriate Sentinel commitment tier.

---

## 8. Security Architecture Controls

| Control | Implementation |
|---|---|
| Data Encryption at Rest | AES-256 (Azure default) |
| Data Encryption in Transit | TLS 1.2+ |
| Access Control | Azure RBAC (Sentinel Reader, Responder, Contributor roles) |
| Multi-Factor Authentication | Azure AD MFA for all Sentinel access |
| Log Integrity | Azure Monitor immutable log storage options |
| Data Sovereignty | Australia East region deployment |
| Audit of Sentinel Access | Azure Active Directory audit logs |
