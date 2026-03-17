# Enterprise SIEM Security Architecture for Healthcare Infrastructure

**Client:** Bedminster Health  
**Project Type:** Security Architecture Design  
**Domain:** Enterprise Cybersecurity / SIEM Implementation  
**Status:** Architecture Design Phase

---

## 1. Project Overview

Large healthcare organisations face an increasingly hostile threat landscape. The combination of highly sensitive patient data, critical operational systems, and complex distributed infrastructure makes healthcare a prime target for ransomware groups, state-sponsored actors, and opportunistic attackers. Regulatory obligations under frameworks such as ISO/IEC 27001 and the Australian Privacy Act 1988 further demand that organisations maintain robust controls over data access, integrity, and availability.

A Security Information and Event Management (SIEM) platform provides the foundational capability to aggregate, correlate, and analyse security events across an enterprise environment in real time. Without centralised log analysis, security teams are unable to detect threats proactively, correlate indicators of compromise across disparate systems, or meet audit and compliance obligations effectively.

This project presents a comprehensive SIEM architecture design for Bedminster Health, including platform evaluation, architecture recommendations, detection strategy, and an implementation roadmap.

---

## 2. Organisation Profile

**Bedminster Health** is a large healthcare organisation operating across multiple sites in Australia.

| Component | Detail |
|---|---|
| Main Hospital | 1 large acute care facility |
| Satellite Hospitals | 4 regional satellite hospitals |
| Outpatient Clinics | Multiple outpatient and specialist clinics |
| Staff | Over 6,000 employees |
| Workstations | Approximately 2,000 endpoints |
| Servers | Approximately 150 servers (on-premises and cloud-hosted) |
| Network Infrastructure | Router-based enterprise network with perimeter firewalls |
| Log Sources | Firewalls, routers, servers, workstations, medical devices, and applications |

The organisation's infrastructure spans clinical systems (Electronic Medical Records, patient monitoring), administrative systems, and network devices distributed across geographically separated sites connected via a routed enterprise WAN.

---

## 3. Security Problem

Bedminster Health currently generates logs across its infrastructure, including firewalls, servers, workstations, routers, and clinical systems. However, these logs are collected in silos without centralised aggregation or analysis.

Key security gaps include:

- **No centralised log correlation:** Events from different systems cannot be correlated to identify multi-stage attacks.
- **Reactive detection only:** Suspicious activity is typically identified only after an incident has already occurred.
- **No real-time alerting:** Security staff have no mechanism to receive automated alerts for suspicious behaviour.
- **Limited visibility:** Security personnel lack a unified view of the organisation's security posture across all sites.
- **Compliance risk:** Without audit-ready log management, the organisation faces challenges meeting regulatory obligations.

These gaps expose Bedminster Health to significant risk from ransomware, insider threats, credential-based attacks, and lateral movement across its network.

---

## 4. Project Objectives

This project aims to:

- Design a scalable SIEM architecture appropriate for Bedminster Health's distributed healthcare environment.
- Evaluate leading SIEM platforms and recommend the most suitable solution.
- Define log ingestion pipelines covering all relevant infrastructure and clinical log sources.
- Develop a detection strategy with use cases aligned to realistic healthcare threat scenarios.
- Produce a phased implementation roadmap suitable for enterprise deployment.
- Align the architecture with applicable compliance frameworks including ISO/IEC 27001 and the Australian Privacy Act 1988.

---

## 5. Proposed Solution

A SIEM platform enables centralised collection of security events from across the enterprise, applies correlation rules to identify suspicious patterns, generates alerts for the security operations team, and supports structured incident response workflows.

### Platform Evaluation

Three leading SIEM platforms were evaluated for suitability:

| Platform | Deployment | Key Strength |
|---|---|---|
| **Splunk** | On-premises / Cloud | Mature platform with powerful query language |
| **Elastic Security** | On-premises / Cloud | Open-source flexibility, strong search capabilities |
| **Microsoft Sentinel** | Cloud-native (Azure) | Native cloud integration, scalability, built-in connectors |

### Recommended Solution: Microsoft Sentinel

**Microsoft Sentinel** is recommended as the SIEM platform for Bedminster Health based on the following factors:

- **Cloud-native architecture:** Fully managed SaaS platform eliminates on-premises infrastructure overhead.
- **Scalability:** Scales automatically to accommodate log volumes across all sites without capacity planning.
- **Integration capabilities:** Native connectors for Microsoft 365, Azure Active Directory, Defender suite, and third-party sources.
- **Built-in analytics:** Pre-built detection rules, UEBA (User and Entity Behaviour Analytics), and threat intelligence feeds.
- **Cost model:** Consumption-based pricing aligns with Bedminster Health's log volume and budget requirements.
- **Compliance support:** Built-in compliance workbooks support ISO 27001 and Australian Privacy Act reporting obligations.

---

## 6. Architecture Overview

The proposed SIEM architecture implements a multi-layer log pipeline:

```
Infrastructure Devices
        ↓
   Log Collectors
  (Azure Monitor Agent / MMA)
        ↓
  Log Aggregation Layer
  (Log Analytics Workspace)
        ↓
   SIEM Platform
  (Microsoft Sentinel)
        ↓
   Detection Rules
  (Analytics Rules / KQL)
        ↓
   SOC Monitoring
  (Dashboards / Workbooks)
        ↓
  Incident Response
  (Incidents / Playbooks / SOAR)
```

Log sources include servers, workstations, firewalls, routers, medical devices, and cloud services. Agents deployed to endpoints forward logs to a centralised Log Analytics Workspace ingested by Microsoft Sentinel. Correlation rules trigger alerts that are triaged by the SOC team and resolved through documented incident response workflows.

---

## 7. Repository Structure

```
healthcare-siem-security-architecture/
│
├── README.md
│
├── docs/
│   ├── project-overview.md
│   ├── threat-model.md
│   ├── siem-evaluation.md
│   ├── architecture-design.md
│   ├── detection-strategy.md
│   ├── implementation-roadmap.md
│   └── cost-benefit-analysis.md
│
├── diagrams/
│   ├── current-network-architecture.md
│   ├── proposed-siem-architecture.md
│   └── log-ingestion-pipeline.md
│
└── research/
    └── healthcare-security-compliance.md
```

---

## 8. Skills Demonstrated

| Skill Area | Description |
|---|---|
| **Enterprise Security Architecture** | Designing multi-site SIEM solutions for complex healthcare environments |
| **SIEM Evaluation** | Structured platform comparison across deployment, scalability, and cost dimensions |
| **Threat Detection Engineering** | Creating detection use cases and correlation logic for realistic threat scenarios |
| **Security Monitoring Design** | Designing SOC dashboards, alerting, and triage workflows |
| **Compliance Analysis** | Mapping SIEM capabilities to ISO/IEC 27001 and Australian Privacy Act 1988 requirements |
| **DevSecOps Considerations** | Integrating security monitoring into CI/CD pipelines and cloud infrastructure |

---

## 9. Documentation

| Document | Description |
|---|---|
| [Project Overview](docs/project-overview.md) | Full project context and scope |
| [Threat Model](docs/threat-model.md) | Healthcare threat landscape and risk assessment |
| [SIEM Evaluation](docs/siem-evaluation.md) | Platform comparison and recommendation |
| [Architecture Design](docs/architecture-design.md) | Detailed SIEM architecture specification |
| [Detection Strategy](docs/detection-strategy.md) | Use cases and correlation rule design |
| [Implementation Roadmap](docs/implementation-roadmap.md) | Phased deployment plan |
| [Cost-Benefit Analysis](docs/cost-benefit-analysis.md) | Financial and operational justification |
| [Compliance Research](research/healthcare-security-compliance.md) | Regulatory framework alignment |

---

*This project is part of a professional cybersecurity portfolio demonstrating enterprise security architecture capabilities.*