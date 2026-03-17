# Log Ingestion Pipeline

**Document:** Log Ingestion Pipeline — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Overview

This document describes the log ingestion pipeline for the Microsoft Sentinel SIEM deployment at Bedminster Health. The pipeline defines the flow of log data from source devices through collection, transmission, aggregation, and ingestion into the SIEM platform for analysis.

A reliable and well-monitored log ingestion pipeline is fundamental to SIEM effectiveness. Gaps in the pipeline — whether due to misconfiguration, network failures, or agent issues — directly reduce detection coverage.

---

## 2. Log Ingestion Pipeline Diagram

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                    LOG INGESTION PIPELINE — BEDMINSTER HEALTH                ║
╚══════════════════════════════════════════════════════════════════════════════╝

┌─────────────────────────────────────────────────────────────────────────────┐
│                         STEP 1: LOG GENERATION                               │
│                                                                               │
│  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌──────────────┐ │
│  │    SERVERS    │  │ WORKSTATIONS  │  │   FIREWALLS   │  │   ROUTERS    │ │
│  │               │  │               │  │               │  │              │ │
│  │ Windows Sec.  │  │ Windows Sec.  │  │ Allow/Deny    │  │ Syslog       │ │
│  │ Event Log     │  │ Event Log     │  │ IPS Alerts    │  │ Interface    │ │
│  │               │  │               │  │ VPN Auth      │  │ Events       │ │
│  │ Linux Syslog  │  │ Defender EDR  │  │ NAT Logs      │  │ TACACS Auth  │ │
│  │               │  │               │  │               │  │              │ │
│  │ AD Audit Logs │  │ PowerShell    │  │ Format: CEF   │  │ Format:      │ │
│  │               │  │ Script Logs   │  │ or Syslog     │  │ Syslog       │ │
│  │ Format: EVTX  │  │ Format: EVTX  │  │               │  │              │ │
│  └───────┬───────┘  └───────┬───────┘  └───────┬───────┘  └──────┬───────┘ │
│          │                  │                  │                   │          │
│  ┌───────────────┐  ┌────────────────────────────────────────────────────┐  │
│  │MEDICAL DEVICES│  │                    CLOUD SOURCES                   │  │
│  │               │  │  ┌──────────────┐  ┌─────────────┐  ┌──────────┐  │  │
│  │Network traffic│  │  │ Microsoft    │  │  Azure AD   │  │ Defender │  │  │
│  │ monitoring    │  │  │ 365 Audit    │  │  Sign-in    │  │  for EP  │  │  │
│  │ (via firewall │  │  │ Log          │  │  Audit Logs │  │  Alerts  │  │  │
│  │  and NetFlow) │  │  │              │  │             │  │          │  │  │
│  │               │  │  │ Format: API  │  │ Format: API │  │ Format:  │  │  │
│  │ No agent      │  │  └──────────────┘  └─────────────┘  │ API      │  │  │
│  │ (vendor limit)│  │                                       └──────────┘  │  │
│  └───────┬───────┘  └───────────────────────────┬────────────────────────┘  │
└──────────┼─────────────────────────────────────── ┼─────────────────────────┘
           │                                         │
           ▼                                         ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         STEP 2: LOG COLLECTION                               │
│                                                                               │
│  ┌──────────────────────────┐         ┌────────────────────────────────┐    │
│  │   AZURE MONITOR AGENT    │         │   LINUX LOG FORWARDER VM       │    │
│  │   (Windows/Linux hosts)  │         │   (Per-site, DMZ/Server Room)  │    │
│  │                          │         │                                  │    │
│  │  Deployed via:           │         │  Receives:                       │    │
│  │  • Group Policy (GPO)    │         │  • Syslog (UDP/TCP 514)          │    │
│  │  • MECM / Intune         │         │  • CEF (TCP 514/25226)           │    │
│  │                          │         │                                  │    │
│  │  Collects:               │         │  From:                           │    │
│  │  • Security Event Log    │         │  • Palo Alto / Fortinet FW       │    │
│  │  • System/App Event Log  │         │  • Cisco / Juniper Routers       │    │
│  │  • Custom log files      │         │  • IPS/IDS Devices               │    │
│  │  • Performance counters  │         │                                  │    │
│  │                          │         │  Forwards via AMA agent:         │    │
│  │  Configured via:         │         │  • HTTPS to Log Analytics WS     │    │
│  │  Data Collection Rules   │         │                                  │    │
│  └──────────────┬───────────┘         └────────────────┬───────────────┘    │
│                 │                                        │                    │
│                 │           ┌────────────────────────┐   │                   │
│                 │           │  MICROSOFT DATA         │   │                   │
│                 │           │  CONNECTORS             │   │                   │
│                 │           │                          │   │                   │
│                 │           │  • M365 Connector        │   │                   │
│                 │           │  • AAD Connector         │   │                   │
│                 │           │  • Defender Connector    │   │                   │
│                 │           │  (Cloud-to-cloud, API)   │   │                   │
│                 │           └────────────┬─────────────┘   │                   │
└─────────────────┼────────────────────────┼─────────────────┼──────────────────┘
                  │                         │                 │
                  └─────────────────────────┘─────────────────┘
                                            │
                                     HTTPS (TLS 1.2+)
                                     TCP Port 443
                                            │
                                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                       STEP 3: LOG AGGREGATION                                │
│                                                                               │
│              ┌──────────────────────────────────────────┐                   │
│              │    AZURE LOG ANALYTICS WORKSPACE          │                   │
│              │    (Australia East — Data Sovereignty)    │                   │
│              │                                            │                   │
│              │  Tables (examples):                        │                   │
│              │  • SecurityEvent (Windows security logs)  │                   │
│              │  • Syslog (Linux and network device logs) │                   │
│              │  • CommonSecurityLog (CEF firewall logs)  │                   │
│              │  • SigninLogs (Azure AD authentication)   │                   │
│              │  • OfficeActivity (Microsoft 365 audit)   │                   │
│              │  • DeviceEvents (Defender for Endpoint)   │                   │
│              │                                            │                   │
│              │  Retention: 90 days (hot) + 1yr (archive) │                   │
│              │  Encryption: AES-256 at rest               │                   │
│              │  Access: Azure RBAC enforced               │                   │
│              └───────────────────┬──────────────────────┘                   │
└──────────────────────────────────┼──────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                       STEP 4: SIEM INGESTION AND ANALYSIS                    │
│                                                                               │
│              ┌──────────────────────────────────────────┐                   │
│              │          MICROSOFT SENTINEL               │                   │
│              │                                            │                   │
│              │  ┌──────────────────────────────────┐     │                   │
│              │  │ Analytics Rules (KQL)             │     │                   │
│              │  │ Scheduled queries (5–60 min)      │     │                   │
│              │  │ Near-real-time (NRT) rules         │     │                   │
│              │  └──────────────────────┬────────────┘     │                   │
│              │                          │                  │                   │
│              │                    ALERT TRIGGERED          │                   │
│              │                          │                  │                   │
│              │  ┌──────────────────────▼────────────┐     │                   │
│              │  │           INCIDENT CREATED         │     │                   │
│              │  │  Severity: Critical / High /       │     │                   │
│              │  │           Medium / Low             │     │                   │
│              │  │  MITRE ATT&CK Technique Mapped     │     │                   │
│              │  │  Entities Extracted                │     │                   │
│              │  └──────────────────────┬────────────┘     │                   │
│              └──────────────────────── ┼──────────────────┘                   │
└───────────────────────────────────────┼─────────────────────────────────────┘
                                         │
                                         ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                       STEP 5: SOC TRIAGE AND RESPONSE                        │
│                                                                               │
│  ┌────────────────┐    ┌──────────────────┐    ┌────────────────────────┐   │
│  │ SOC Analyst    │    │ Automated         │    │ Incident Response      │   │
│  │ Triage         │    │ Playbook          │    │ Actions                │   │
│  │                │    │                   │    │                        │   │
│  │ Review incident│    │ Notify Teams/     │    │ • Contain endpoint     │   │
│  │ Investigate    │    │ Email             │    │ • Reset credentials    │   │
│  │ entities       │    │                   │    │ • Block IP at firewall │   │
│  │ Review evidence│    │ Enrich with TI    │    │ • Escalate to CISO     │   │
│  │                │    │                   │    │ • Create ITSM ticket   │   │
│  └────────────────┘    └──────────────────┘    └────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Pipeline Health Monitoring

The log ingestion pipeline must be continuously monitored to detect gaps in data collection. Pipeline failures can create blind spots that reduce detection coverage without the SOC team being aware.

### 3.1 Health Monitoring Mechanisms

| Monitoring Point | Method | Alert Condition |
|---|---|---|
| AMA agent connectivity | Heartbeat table in Log Analytics | Agent not seen in >30 minutes |
| Log volume anomalies | Baseline query on daily ingest per source | Volume drops >50% from 7-day average |
| Data connector status | Sentinel data connector health workbook | Connector shows disconnected state |
| Log forwarder VM availability | Azure Monitor VM availability metrics | VM unavailable for >5 minutes |

### 3.2 Heartbeat Monitoring Query

The following KQL query identifies agents that have not sent a heartbeat in the last 30 minutes, indicating a potential collection gap:

```kusto
Heartbeat
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where LastHeartbeat < ago(30m)
| project Computer, LastHeartbeat, MinutesSinceHeartbeat = datetime_diff('minute', now(), LastHeartbeat)
| order by MinutesSinceHeartbeat desc
```

---

## 4. Data Flow Summary

| Stage | Component | Protocol/Port | Direction |
|---|---|---|---|
| Log Generation | Endpoints, devices | N/A | Source |
| Collection (endpoints) | Azure Monitor Agent | HTTPS / TCP 443 | Endpoint → Azure |
| Collection (network devices) | Linux Log Forwarder | Syslog / UDP 514 or CEF / TCP 514 | Device → Forwarder |
| Collection (cloud) | Microsoft Data Connectors | HTTPS / TCP 443 (API) | Azure → Azure |
| Transmission (forwarder) | AMA on Log Forwarder | HTTPS / TCP 443 | Forwarder → Azure |
| Aggregation | Log Analytics Workspace | Internal | Azure |
| Analysis | Microsoft Sentinel | Internal | Azure |
| Alerting | SOC / Playbooks | HTTPS, Teams webhook | Azure → SOC |
