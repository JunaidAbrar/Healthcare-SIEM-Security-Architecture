# Project Overview

**Project:** Enterprise SIEM Security Architecture  
**Client:** Bedminster Health  
**Prepared by:** Security Architecture Team  
**Version:** 1.0

---

## 1. Executive Summary

Bedminster Health operates a large, distributed healthcare infrastructure spanning one major hospital, four satellite hospitals, and multiple outpatient clinics. Despite generating substantial volumes of security event logs across firewalls, servers, workstations, routers, and clinical systems, the organisation currently lacks the capability to centralise, correlate, or analyse these events in real time.

This project delivers a comprehensive Security Information and Event Management (SIEM) architecture design to address these capability gaps. The solution enables centralised log collection, real-time threat detection, event correlation across all sites, and structured incident response — materially improving Bedminster Health's security posture and compliance readiness.

---

## 2. Background and Context

### 2.1 Healthcare as a High-Value Target

Healthcare organisations are consistently ranked among the most targeted sectors for cyber attack globally. The combination of sensitive personal health information, critical operational systems (including life-safety medical devices), and the urgent operational pressures that disincentivise security controls makes healthcare an attractive target for:

- **Ransomware operators** seeking high-value targets willing to pay to restore operations.
- **Nation-state actors** targeting medical research and health data for intelligence purposes.
- **Financially motivated attackers** seeking to monetise stolen patient records.
- **Insider threats** including both malicious and negligent staff with access to sensitive systems.

The Australian Cyber Security Centre (ACSC) consistently reports healthcare as one of the top sectors by reported cyber incidents.

### 2.2 Current State at Bedminster Health

Bedminster Health generates logs from a range of infrastructure components, but these are currently managed in isolation:

- **Firewalls and routers** produce network traffic and access logs managed by the network team.
- **Windows servers and workstations** produce Windows Event Logs reviewed only upon request.
- **Clinical systems** generate application and access logs that are rarely reviewed for security indicators.
- **No centralised SIEM or log aggregation platform** exists.
- **No automated alerting** is in place for suspicious activity.
- **Incident detection is reactive**, relying on manual investigation after reports of unusual behaviour.

This state represents a material security risk. Multi-stage attacks such as ransomware campaigns can persist in an environment for weeks or months before detection, during which time attackers conduct reconnaissance, escalate privileges, and stage payloads. Without centralised correlation, individual log entries that together indicate an attack are never connected.

---

## 3. Project Scope

### 3.1 In Scope

- Assessment of the current logging environment at Bedminster Health.
- Evaluation of leading SIEM platforms for suitability.
- Design of a target SIEM architecture including log sources, collection agents, aggregation, and the SIEM platform.
- Definition of a detection strategy covering primary threat scenarios.
- Phased implementation roadmap.
- Compliance alignment mapping to ISO/IEC 27001 and the Australian Privacy Act 1988.
- Cost-benefit analysis supporting the business case.

### 3.2 Out of Scope

- Vendor procurement and commercial negotiation.
- Physical network infrastructure changes.
- Clinical system application security assessment.
- Penetration testing or red team exercises.

---

## 4. Project Objectives

| Objective | Description |
|---|---|
| Centralised Log Collection | Aggregate logs from all infrastructure components across all sites into a single platform |
| Real-Time Threat Detection | Enable automated detection of suspicious activity through correlation rules and analytics |
| Event Correlation | Connect related events across disparate sources to identify multi-stage attack patterns |
| Incident Response Improvement | Provide structured workflows and tooling to reduce mean time to respond (MTTR) |
| Compliance Support | Generate audit-ready logs and reports aligned to regulatory requirements |
| Security Visibility | Deliver dashboards and reports providing continuous visibility of the security posture |

---

## 5. Stakeholders

| Role | Interest |
|---|---|
| Chief Information Security Officer (CISO) | Overall security posture improvement and regulatory compliance |
| IT Infrastructure Team | Log source configuration and agent deployment |
| SOC / Security Team | Day-to-day use of the SIEM platform for monitoring and incident response |
| Compliance and Risk Team | Audit reporting and regulatory alignment |
| Executive Leadership | Risk reduction and organisational resilience |
| Clinical Operations | Minimal disruption to clinical systems during implementation |

---

## 6. Key Deliverables

| Deliverable | Description |
|---|---|
| Threat Model | Documented threat landscape and risk assessment for Bedminster Health |
| SIEM Platform Evaluation | Structured comparison of Splunk, Elastic Security, and Microsoft Sentinel |
| Architecture Design | Detailed technical architecture including log pipeline and component specifications |
| Detection Strategy | Use case library with detection logic for primary threat scenarios |
| Implementation Roadmap | Phased deployment plan with milestones and resource estimates |
| Cost-Benefit Analysis | Financial justification and expected return on security investment |

---

## 7. Assumptions and Constraints

### Assumptions

- Bedminster Health has existing network connectivity between all sites capable of supporting log forwarding.
- An Azure subscription will be established or is available to support Microsoft Sentinel deployment.
- IT staff with relevant expertise will be available to support agent deployment and configuration.
- Clinical system vendors will be engaged to enable log forwarding from relevant systems.

### Constraints

- Clinical systems may have limitations on agent installation due to vendor support agreements.
- Regulatory requirements (Australian Privacy Act, HIPAA-equivalent obligations) impose constraints on how log data containing patient identifiers is stored and accessed.
- Log data retention requirements must be balanced against storage costs.

---

## 8. Success Criteria

The SIEM implementation will be considered successful when:

- All primary log sources are onboarded and ingesting data into the SIEM platform.
- Core detection use cases are operational and generating validated alerts.
- Mean time to detect (MTTD) for known threat scenarios is measurably reduced compared to the baseline.
- The SOC team is able to triage and respond to incidents using the SIEM platform.
- Compliance reports can be generated from the SIEM platform for ISO/IEC 27001 and Privacy Act obligations.
