# Proposed SIEM Architecture

**Document:** Proposed SIEM Architecture — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Overview

This document presents the proposed Microsoft Sentinel SIEM architecture for Bedminster Health. The architecture replaces the current siloed log collection model with a centralised, cloud-native security monitoring platform that provides real-time detection, event correlation, and structured incident response across all sites.

---

## 2. High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                    PROPOSED SIEM ARCHITECTURE — BEDMINSTER HEALTH               │
│                         Microsoft Sentinel (Azure — Australia East)              │
└─────────────────────────────────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────────────────────────────┐
│                              LOG SOURCE LAYER                                  │
│                                                                                 │
│   [Main Hospital]         [Satellite Hospitals ×4]    [Outpatient Clinics]     │
│   ┌─────────────┐         ┌──────────────────────┐    ┌──────────────────┐    │
│   │ Servers     │         │ Servers              │    │ Workstations     │    │
│   │ Workstations│         │ Workstations         │    │ Local Apps       │    │
│   │ Firewalls   │         │ Firewalls / Routers  │    └──────────────────┘    │
│   │ Routers     │         └──────────────────────┘                             │
│   │ Med. Devices│         [Cloud Sources]                                      │
│   └─────────────┘         ┌──────────────────────┐                             │
│                            │ Microsoft 365        │                             │
│                            │ Azure Active Dir.    │                             │
│                            │ Defender for Endpoint│                             │
│                            └──────────────────────┘                             │
└────────────────────────────┬──────────────────────────────────────────────────┘
                              │
                              ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                           LOG COLLECTION LAYER                                  │
│                                                                                 │
│   Windows/Linux Endpoints          Network Devices          Cloud Sources       │
│   ┌─────────────────────┐         ┌─────────────────┐      ┌─────────────┐    │
│   │ Azure Monitor Agent │         │ Linux Log       │      │ Microsoft   │    │
│   │ (AMA)               │         │ Forwarder       │      │ Data        │    │
│   │                     │         │ (CEF / Syslog)  │      │ Connectors  │    │
│   │ Per-site deployment │         │ Per-site VM     │      │ (API-based) │    │
│   └──────────┬──────────┘         └────────┬────────┘      └──────┬──────┘    │
│              │                              │                       │           │
│              └──────────────────────────────┴───────────────────────┘           │
│                                             │                                   │
│                                    HTTPS (TCP 443)                              │
└────────────────────────────────────────────┼───────────────────────────────────┘
                                              │
                                              ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                          LOG AGGREGATION LAYER                                  │
│                                                                                 │
│                    ┌──────────────────────────────────┐                        │
│                    │   Azure Log Analytics Workspace   │                        │
│                    │   (Australia East Region)         │                        │
│                    │                                   │                        │
│                    │   • Centralised log storage       │                        │
│                    │   • 90-day hot retention          │                        │
│                    │   • 1-year archive retention      │                        │
│                    │   • Role-based access control     │                        │
│                    │   • AES-256 encryption at rest    │                        │
│                    └──────────────────┬───────────────┘                        │
└───────────────────────────────────────┼───────────────────────────────────────┘
                                         │
                                         ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                          SIEM ANALYTICS LAYER                                   │
│                                                                                 │
│                    ┌──────────────────────────────────┐                        │
│                    │       Microsoft Sentinel          │                        │
│                    │                                   │                        │
│                    │  ┌──────────────┐ ┌───────────┐  │                        │
│                    │  │  Analytics   │ │   UEBA    │  │                        │
│                    │  │  Rules (KQL) │ │ Behaviour │  │                        │
│                    │  └──────────────┘ │ Analytics │  │                        │
│                    │                   └───────────┘  │                        │
│                    │  ┌──────────────┐ ┌───────────┐  │                        │
│                    │  │   Threat     │ │ Watchlists│  │                        │
│                    │  │Intelligence  │ │           │  │                        │
│                    │  └──────────────┘ └───────────┘  │                        │
│                    └──────────────────┬───────────────┘                        │
└───────────────────────────────────────┼───────────────────────────────────────┘
                                         │
                                         ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                          SOC OPERATIONS LAYER                                   │
│                                                                                 │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌────────────┐  │
│   │   Incidents  │    │  Dashboards  │    │  Automation  │    │  Reporting │  │
│   │   Queue      │    │  Workbooks   │    │  Playbooks   │    │  Compliance│  │
│   │              │    │              │    │  (SOAR)      │    │  Workbooks │  │
│   │ SOC Analyst  │    │ Real-time    │    │              │    │            │  │
│   │ Triage       │    │ Visibility   │    │ Auto-block   │    │ ISO 27001  │  │
│   └──────────────┘    └──────────────┘    │ Auto-isolate │    │ Privacy Act│  │
│                                            └──────────────┘    └────────────┘  │
└───────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Component Descriptions

### 3.1 Log Source Layer

All infrastructure components across Bedminster Health's six sites generate security-relevant events. Log sources are categorised as:

- **Endpoint sources:** Windows servers, Linux servers, Windows workstations.
- **Network sources:** Perimeter and internal firewalls, WAN routers, switches.
- **Medical device sources:** Network-connected clinical equipment (monitored via network telemetry).
- **Cloud sources:** Microsoft 365, Azure Active Directory, Microsoft Defender for Endpoint.

### 3.2 Log Collection Layer

Two collection mechanisms are used:

**Azure Monitor Agent (AMA)** — Installed on Windows and Linux endpoints. Collects Windows Security Event Log, Syslog, and custom log sources as defined by Data Collection Rules. Transmits logs directly to the Log Analytics Workspace over HTTPS.

**Linux Log Forwarder** — A lightweight Linux VM deployed at each site. Receives Syslog and CEF-formatted logs from network devices (firewalls, routers) and forwards them to the Log Analytics Workspace using the AMA agent running on the forwarder.

**Microsoft Data Connectors** — Cloud-to-cloud connectors that pull logs from Microsoft 365, Azure AD, and Defender services directly into Sentinel without requiring on-premises infrastructure.

### 3.3 Log Aggregation Layer

The **Azure Log Analytics Workspace** serves as the centralised log repository. All data from all sources is ingested, indexed, and stored here. The workspace is deployed in the Azure Australia East region for data sovereignty compliance.

### 3.4 SIEM Analytics Layer

**Microsoft Sentinel** provides the detection and analytics engine:

- **Analytics Rules** — KQL-based queries scheduled to run at intervals, detecting known threat patterns.
- **UEBA** — Builds behavioural baselines for users and entities, detecting deviations.
- **Threat Intelligence** — Matches incoming events against known malicious indicators (IP addresses, domains, file hashes).
- **Watchlists** — Reference data used to enrich and contextualise detection rules.

### 3.5 SOC Operations Layer

The SOC team operates through the Sentinel interface:

- **Incidents Queue** — All triggered alerts create incidents that are triaged by analysts.
- **Investigation Graph** — Visual representation of entities involved in an incident for investigation.
- **Hunting Queries** — Proactive KQL queries to search for subtle indicators not covered by automated rules.
- **Playbooks (SOAR)** — Automated response actions triggered by incidents.
- **Workbooks** — Interactive dashboards for operational monitoring and compliance reporting.

---

## 4. Security Zones

The proposed architecture maintains clear separation between:

| Zone | Description |
|---|---|
| Clinical Network | Patient-facing systems including medical devices and clinical workstations |
| Administrative Network | Staff workstations, email, and office systems |
| Server Zone | Servers including Active Directory, file servers, and application servers |
| DMZ | Log forwarder VMs and external-facing services |
| Management Zone | Network management and SIEM administration |
| Azure (Cloud) | Log Analytics Workspace, Microsoft Sentinel, and Microsoft 365 services |

All log forwarding traverses network zones using encrypted channels (HTTPS/TLS 1.2+) with no lateral access permitted between zones.

---

## 5. Architecture Diagram Notes

For production documentation, this architecture should be rendered in a professional diagramming tool such as:

- **Microsoft Visio** — Using Azure icon sets for cloud components.
- **draw.io / diagrams.net** — Using the Azure and network shape libraries.
- **Lucidchart** — For collaborative architecture documentation.

The production diagram should clearly distinguish cloud and on-premises boundaries, show all network traffic flows with protocol and port labels, and include site-level details for each Bedminster Health location.
