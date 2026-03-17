# Implementation Roadmap

**Document:** SIEM Implementation Roadmap — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Overview

This document presents a phased implementation plan for deploying Microsoft Sentinel as the SIEM platform at Bedminster Health. The roadmap is structured in three phases, progressing from foundation infrastructure through to full operational capability and continuous improvement.

The phased approach is designed to:
- Deliver early security value without requiring full deployment before any benefit is realised.
- Manage risk by stabilising each phase before progressing to the next.
- Allow the SOC team to build capability incrementally alongside the technical deployment.
- Minimise disruption to clinical operations.

**Estimated Total Duration:** 6–9 months (subject to resource availability and stakeholder engagement)

---

## 2. Phase 1 — Foundation (Months 1–3)

### Objective

Establish the core SIEM infrastructure, onboard primary log sources, and validate data ingestion. By the end of Phase 1, all primary log sources should be ingesting data into Sentinel and the SOC team should have a functional platform for basic monitoring.

### Workstreams

#### 1.1 Log Source Identification and Scoping

| Task | Description | Owner | Duration |
|---|---|---|---|
| Log source inventory | Document all log sources across all sites (servers, workstations, firewalls, routers, medical devices, cloud) | IT Infrastructure | 2 weeks |
| Log source prioritisation | Rank log sources by security criticality | Security Architect | 1 week |
| Data volume estimation | Estimate daily log volume per source type | IT Infrastructure | 1 week |
| Connector assessment | Identify appropriate Sentinel connector for each log source | Security Architect | 1 week |

#### 1.2 Azure Environment Setup

| Task | Description | Owner | Duration |
|---|---|---|---|
| Azure subscription provisioning | Establish or configure Azure subscription in Australia East region | IT / Azure Admin | 1 week |
| Log Analytics Workspace creation | Deploy workspace with appropriate retention settings (90 days hot, 1 year archive) | Azure Admin | 1 day |
| Microsoft Sentinel activation | Enable Sentinel on the Log Analytics Workspace | Azure Admin | 1 day |
| RBAC configuration | Configure Sentinel roles for SOC analysts, senior analysts, and read-only stakeholders | Azure Admin | 1 week |
| Network connectivity | Validate outbound HTTPS from all sites to Azure endpoints (TCP 443) | Network Team | 1 week |

#### 1.3 Agent Deployment

| Task | Description | Owner | Duration |
|---|---|---|---|
| AMA pilot deployment | Deploy Azure Monitor Agent to pilot group (10 servers, 20 workstations) | IT Infrastructure | 1 week |
| AMA organisation-wide rollout | Deploy AMA to all Windows servers and workstations via Group Policy / MECM | IT Infrastructure | 3 weeks |
| Linux agent deployment | Deploy AMA to Linux servers | IT Infrastructure | 1 week |
| Data Collection Rules configuration | Configure DCRs defining event channels and log types to collect | Security Architect | 1 week |

#### 1.4 Network Device Log Forwarding

| Task | Description | Owner | Duration |
|---|---|---|---|
| Linux log forwarder deployment | Deploy log forwarder VMs at main hospital and each satellite hospital | IT Infrastructure | 2 weeks |
| Firewall syslog configuration | Configure firewalls to forward logs to site log forwarder (CEF/Syslog) | Network Team | 1 week |
| Router syslog configuration | Configure routers to forward Syslog to site log forwarder | Network Team | 1 week |
| Ingestion validation | Validate all network device logs are appearing in Sentinel | Security Team | 1 week |

#### 1.5 Cloud Connector Enablement

| Task | Description | Owner | Duration |
|---|---|---|---|
| Microsoft 365 connector | Enable Office 365 unified audit log connector | Azure Admin | 1 day |
| Azure Active Directory connector | Enable AAD sign-in and audit log connector | Azure Admin | 1 day |
| Microsoft Defender for Endpoint connector | Enable MDE connector | Azure Admin | 1 day |

### Phase 1 Success Criteria

- All 150 servers ingesting Windows/Linux logs into Sentinel.
- All 2,000 workstations ingesting security event logs.
- All perimeter firewalls ingesting CEF/Syslog logs.
- Microsoft 365 and Azure AD logs ingesting.
- Sentinel receiving data from all primary log source categories.
- SOC team has access to the Sentinel portal and basic querying capability.

---

## 3. Phase 2 — Detection and Operations (Months 3–6)

### Objective

Deploy and tune detection rules, establish SOC operational workflows, and configure alerting and dashboards. By the end of Phase 2, the SOC team is fully operational with active detection, alert triage, and incident response capability.

### Workstreams

#### 2.1 Detection Rule Configuration

| Task | Description | Owner | Duration |
|---|---|---|---|
| Built-in rule review | Review and enable Sentinel built-in analytics rules relevant to healthcare | Security Engineer | 2 weeks |
| Custom rule development | Develop custom KQL rules for use cases in the detection strategy | Security Engineer | 3 weeks |
| Watchlist creation | Create watchlists (privileged accounts, sensitive servers, approved admin IPs) | Security Engineer | 1 week |
| MITRE ATT&CK mapping | Map all active rules to MITRE ATT&CK techniques | Security Engineer | 1 week |

#### 2.2 Alert Tuning

| Task | Description | Owner | Duration |
|---|---|---|---|
| Pilot alert monitoring | Monitor new rules for false positives over a 30-day period | SOC Team | 4 weeks |
| Threshold adjustment | Tune thresholds based on observed false positive rates | Security Engineer | 2 weeks |
| Whitelist management | Establish and document approved exclusions for known legitimate activity | SOC Team | 2 weeks |
| Alert severity review | Validate severity ratings and escalation thresholds | Security Architect | 1 week |

#### 2.3 UEBA and Threat Intelligence

| Task | Description | Owner | Duration |
|---|---|---|---|
| UEBA activation | Enable Sentinel UEBA and configure entity tracking (users, hosts) | Security Engineer | 1 week |
| Baseline establishment | Allow UEBA to build user and entity behaviour baselines (21-day learning period) | Sentinel (automated) | 3 weeks |
| Threat intelligence connector | Enable Microsoft Threat Intelligence and configure additional TI feeds | Security Engineer | 1 week |

#### 2.4 SOC Workflow Development

| Task | Description | Owner | Duration |
|---|---|---|---|
| Incident response playbooks | Document response procedures for each high-severity use case | Security Analyst | 3 weeks |
| Playbook automation | Implement Azure Logic App playbooks for automated triage (account block, endpoint isolation, Teams notification) | Security Engineer | 3 weeks |
| Escalation procedures | Define escalation paths for P1/P2 incidents including out-of-hours | Security Architect | 1 week |
| ITSM integration | Integrate Sentinel with ITSM (ServiceNow or equivalent) for incident ticketing | IT / Security | 2 weeks |

#### 2.5 Dashboard Configuration

| Task | Description | Owner | Duration |
|---|---|---|---|
| SOC operations dashboard | Configure real-time SOC monitoring workbook | Security Engineer | 2 weeks |
| Identity analytics workbook | Configure authentication monitoring dashboard | Security Engineer | 1 week |
| Compliance workbook | Configure ISO 27001 / Privacy Act compliance reporting workbook | Security Analyst | 2 weeks |

### Phase 2 Success Criteria

- All detection use cases from the detection strategy are operational.
- Alert false positive rate is below 20% across all active rules.
- SOC team has documented response playbooks for all high and critical severity use cases.
- Automated playbooks are operational for account block and endpoint isolation.
- Incident management is integrated with the ITSM platform.
- SOC dashboards are deployed and in use.

---

## 4. Phase 3 — Optimisation and Continuous Improvement (Months 6–9+)

### Objective

Mature the SIEM capability through threat intelligence enrichment, extended automation, continuous monitoring improvement, and formal security operations metrics. Phase 3 establishes the ongoing operational model for the SIEM platform.

### Workstreams

#### 3.1 Threat Intelligence Integration

| Task | Description | Owner | Duration |
|---|---|---|---|
| Healthcare-specific TI feeds | Onboard Health-ISAC and ACSC threat intelligence feeds | Security Engineer | 2 weeks |
| TI-based detection rules | Create detection rules that trigger on IOC matches from TI feeds | Security Engineer | 2 weeks |
| TI enrichment playbooks | Automate TI lookups for IP addresses and domains in new incidents | Security Engineer | 2 weeks |

#### 3.2 Extended Automation

| Task | Description | Owner | Duration |
|---|---|---|---|
| Auto-triage playbooks | Automate low-fidelity alert triage based on entity risk score and TI match | Security Engineer | 3 weeks |
| Threat hunting automation | Schedule proactive KQL hunting queries to surface subtle indicators | Security Engineer | 2 weeks |
| Reporting automation | Schedule automated compliance and security status reports to stakeholders | Security Engineer | 1 week |

#### 3.3 Continuous Monitoring Programme

| Task | Description | Owner | Duration |
|---|---|---|---|
| SIEM health monitoring | Implement data connector health monitoring and alerting for ingestion failures | Security Engineer | 1 week |
| Quarterly rule review | Establish quarterly review cycle for detection rules | Security Architect | Ongoing |
| Annual threat model review | Review and update threat model and use case library annually | Security Architect | Ongoing |
| Purple team exercises | Conduct simulated attack exercises to validate detection coverage | Security Team | Bi-annual |

#### 3.4 EMR and Clinical System Integration

| Task | Description | Owner | Duration |
|---|---|---|---|
| EMR vendor engagement | Engage EMR vendor to enable audit log forwarding to Sentinel | Security Architect + IT | 4 weeks |
| Clinical system audit logging | Onboard clinical application audit logs for insider threat detection | IT + Security | 4 weeks |
| Medical device monitoring | Implement network-based monitoring for clinical device traffic anomalies | Network + Security | 3 weeks |

### Phase 3 Success Criteria

- Healthcare-specific threat intelligence feeds operational.
- Automated triage handling at least 30% of low-severity alerts without analyst intervention.
- Clinical system and EMR audit logs onboarded.
- SIEM health monitoring operational with alerting for ingestion failures.
- Quarterly review cycle established and first review completed.
- Security metrics and KPIs reported to CISO monthly.

---

## 5. Resource Requirements

| Role | Phase 1 | Phase 2 | Phase 3 |
|---|---|---|---|
| Security Architect | 0.5 FTE | 0.5 FTE | 0.25 FTE |
| Security Engineer (Sentinel) | 0.5 FTE | 1.0 FTE | 0.5 FTE |
| IT Infrastructure (agent/network) | 1.0 FTE | 0.5 FTE | 0.25 FTE |
| SOC Analyst | 0.25 FTE | 1.0 FTE | 1.0 FTE (ongoing) |
| Azure Administrator | 0.5 FTE | 0.25 FTE | 0.25 FTE |

---

## 6. Key Milestones

| Milestone | Target Date | Phase |
|---|---|---|
| Azure environment and workspace deployed | Month 1, Week 2 | 1 |
| Agent deployment complete (servers) | Month 2, Week 2 | 1 |
| Agent deployment complete (workstations) | Month 2, Week 4 | 1 |
| All primary log sources ingesting | Month 3, Week 2 | 1 |
| Core detection rules deployed | Month 4, Week 4 | 2 |
| SOC operational with incident response | Month 5, Week 4 | 2 |
| Phase 2 tuning complete | Month 6, Week 4 | 2 |
| TI feeds and automation operational | Month 7, Week 4 | 3 |
| Clinical system integration complete | Month 9, Week 2 | 3 |
| Full operational capability | Month 9 | 3 |

---

## 7. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Clinical vendor resists agent deployment | Medium | High | Engage vendor early; use network-based monitoring as fallback |
| Log volumes exceed cost estimates | Medium | Medium | Implement ingestion filters; review commitment tier pricing |
| Staff resource availability delays deployment | Medium | High | Engage specialist implementation partner if required |
| AMA agent compatibility issues | Low | Medium | Pilot deployment on representative systems before full rollout |
| Azure network connectivity gaps at remote sites | Low | High | Network connectivity validated in Phase 1 before proceeding |
