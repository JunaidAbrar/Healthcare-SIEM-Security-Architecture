# Current Network Architecture

**Document:** Current Network Architecture — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Overview

This document describes the current network architecture at Bedminster Health prior to SIEM implementation. The architecture represents the baseline state against which the proposed SIEM architecture is compared.

The current architecture lacks centralised security monitoring. Logs are generated across all infrastructure components but are collected and reviewed independently, with no aggregation or correlation capability.

---

## 2. Current Architecture Diagram

The following diagram represents the current state of Bedminster Health's network infrastructure.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          BEDMINSTER HEALTH NETWORK                          │
│                           (Current State — No SIEM)                         │
└─────────────────────────────────────────────────────────────────────────────┘

                              ┌──────────────────────┐
                              │       INTERNET        │
                              └──────────┬───────────┘
                                         │
                              ┌──────────▼───────────┐
                              │   PERIMETER FIREWALL  │
                              │   (Logs: Local only)  │
                              └──────────┬───────────┘
                                         │
                    ┌────────────────────▼────────────────────┐
                    │            ENTERPRISE WAN / ROUTER       │
                    │           (Logs: Local syslog only)      │
                    └──┬─────────────┬─────────────┬──────────┘
                       │             │             │
         ┌─────────────▼──┐   ┌──────▼──────┐  ┌──▼──────────────┐
         │  MAIN HOSPITAL  │   │  SATELLITE  │  │ OUTPATIENT      │
         │                 │   │  HOSPITALS  │  │ CLINICS         │
         │  ┌───────────┐  │   │  (×4 sites) │  │ (Multiple)      │
         │  │  Servers  │  │   │             │  │                 │
         │  │  (×~120)  │  │   │  ┌────────┐ │  │  ┌──────────┐  │
         │  │ Logs: Local│  │   │  │Servers │ │  │  │Workstns  │  │
         │  └───────────┘  │   │  │(×~5-8) │ │  │  │(×50-100) │  │
         │                 │   │  │Logs:   │ │  │  └──────────┘  │
         │  ┌───────────┐  │   │  │Local   │ │  │                 │
         │  │Workstns   │  │   │  └────────┘ │  │                 │
         │  │ (×~1,000) │  │   │             │  │                 │
         │  │Logs: Local│  │   │  ┌────────┐ │  │                 │
         │  └───────────┘  │   │  │Workstns│ │  │                 │
         │                 │   │  │(×~200) │ │  │                 │
         │  ┌───────────┐  │   │  │Logs:   │ │  │                 │
         │  │ Medical   │  │   │  │Local   │ │  │                 │
         │  │ Devices   │  │   │  └────────┘ │  │                 │
         │  │No logging │  │   └─────────────┘  └─────────────────┘
         │  └───────────┘  │
         └─────────────────┘

  ┌───────────────────────────────────────────────────────────────────────┐
  │                      CURRENT SECURITY MONITORING STATE                 │
  │                                                                         │
  │  ❌  No centralised SIEM or log aggregation platform                   │
  │  ❌  No real-time alerting for security events                          │
  │  ❌  No cross-source event correlation                                  │
  │  ❌  Logs reviewed manually and reactively upon incidents               │
  │  ❌  Medical devices not monitored                                      │
  │  ❌  No unified security visibility across sites                        │
  │                                                                         │
  └───────────────────────────────────────────────────────────────────────┘
```

---

## 3. Log Source Inventory (Current State)

The following table documents current log sources and their collection status prior to SIEM implementation.

| Log Source | Type | Volume (Est.) | Current Collection | Security Review |
|---|---|---|---|---|
| Windows Servers (~150) | Windows Event Log | High | Local (Event Viewer) | Ad hoc, reactive |
| Workstations (~2,000) | Windows Event Log | Very High | Local (Event Viewer) | None |
| Perimeter Firewalls | Syslog / CEF | Medium | Local (device storage) | Ad hoc |
| Internal Firewalls | Syslog / CEF | Medium | Local (device storage) | Ad hoc |
| WAN Routers | Syslog | Low | Local (device storage) | None |
| Medical Devices | Varies | Low | None / Vendor-only | None |
| Active Directory | Windows Event Log | High | Local (domain controllers) | Ad hoc |
| Microsoft 365 | Office 365 Audit | Medium | Microsoft 365 Admin Centre | None |
| Azure AD | AAD Sign-in Logs | Medium | Azure AD Portal | None |

---

## 4. Key Security Gaps in Current Architecture

### 4.1 No Centralised Log Aggregation

Logs from all infrastructure components are stored locally on the devices that generate them. There is no mechanism to query logs across multiple systems simultaneously, making it impossible to trace an attacker's path through the environment.

### 4.2 No Real-Time Detection

Without a SIEM, there is no capability to apply detection rules to incoming log streams in real time. Security events such as failed login attempts, privilege escalation, or suspicious process execution occur and are logged, but no alert is generated.

### 4.3 Reactive Security Model

The security team currently detects incidents only after operational impact becomes apparent — a system becomes unavailable, a user reports unusual behaviour, or external parties (law enforcement, threat researchers) notify the organisation. This reactive model results in long dwell times and greater breach scope.

### 4.4 Network Segmentation Gaps

The current network architecture has limited micro-segmentation between clinical and administrative zones, and between sites. This allows attackers who achieve initial access on one segment to move laterally with limited network controls.

### 4.5 Medical Device Blind Spot

Network-connected medical devices are not monitored for security events. Devices run embedded operating systems, are infrequently updated, and represent an expanding attack surface with no visibility.

---

## 5. Architecture Diagram Notes

For production use, this diagram should be rendered using a professional diagramming tool such as:

- **Microsoft Visio** — For formal architecture documentation.
- **draw.io / diagrams.net** — For collaborative, version-controlled diagrams.
- **Lucidchart** — For professional architecture diagrams with export options.

The diagram should use standard network topology symbols for firewalls, routers, servers, and endpoints, and should clearly distinguish between site boundaries and network zones.
