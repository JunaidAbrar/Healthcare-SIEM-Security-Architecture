# SIEM Platform Evaluation

**Document:** SIEM Platform Evaluation — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Purpose

This document evaluates three leading SIEM platforms against the requirements of Bedminster Health's security monitoring programme. The evaluation is intended to provide an evidence-based recommendation for SIEM platform selection, considering the organisation's technical environment, operational capability, budget constraints, and compliance requirements.

---

## 2. Evaluation Criteria

The following criteria were used to assess each platform. Each criterion was weighted based on its relative importance to Bedminster Health's environment.

| Criterion | Description | Weighting |
|---|---|---|
| Deployment Model | Cloud-native, on-premises, or hybrid; alignment with IT strategy | High |
| Scalability | Ability to handle log volumes across all sites and future growth | High |
| Detection Capabilities | Quality of built-in rules, analytics, UEBA, and custom rule support | High |
| Integration Capabilities | Native connectors for Microsoft, network, and clinical systems | High |
| Cost | Total cost of ownership including licensing, infrastructure, and operations | Medium |
| Operational Complexity | Ease of deployment, management, and staffing requirements | Medium |

---

## 3. Platforms Evaluated

### 3.1 Splunk Enterprise Security

Splunk is one of the most widely deployed SIEM platforms globally. It is built on Splunk's core log analytics platform and is available in both on-premises and cloud-hosted (Splunk Cloud) configurations.

**Deployment Model:**  
Splunk Enterprise Security can be deployed on-premises on dedicated infrastructure or as a fully managed SaaS service (Splunk Cloud). On-premises deployment requires significant server infrastructure and operational expertise to manage.

**Scalability:**  
Splunk scales well for large environments but requires careful capacity planning. On-premises deployments require hardware scaling as log volumes grow. Splunk Cloud simplifies this but at higher per-GB cost at scale.

**Detection Capabilities:**  
Splunk provides an extensive library of out-of-the-box detection rules through its Security Content Automation Protocol (SCAP) library and Splunk Security Essentials. The Search Processing Language (SPL) is powerful and flexible for custom detection logic. UEBA is available as a separate module (Splunk UBA) at additional cost.

**Integration Capabilities:**  
Splunk has a large ecosystem of technology add-ons (TAs) for integrating diverse log sources. Integration with Microsoft Active Directory, Azure, and Office 365 is well-supported. However, some healthcare-specific integrations may require custom development.

**Cost:**  
Splunk is among the most expensive SIEM platforms. Licensing is typically based on daily data ingestion volume (GB/day), which can become very costly at scale. On-premises infrastructure adds to the total cost of ownership.

**Operational Complexity:**  
On-premises Splunk requires dedicated Splunk administration expertise. SPL, while powerful, has a steep learning curve. Significant investment in tuning and maintenance is required to achieve value.

---

### 3.2 Elastic Security (Elastic SIEM)

Elastic Security is built on the Elastic Stack (Elasticsearch, Logstash, Kibana) and provides SIEM, endpoint security, and threat intelligence capabilities. It is available as open-source (self-hosted) or as a managed cloud service (Elastic Cloud).

**Deployment Model:**  
Elastic Security can be self-hosted using open-source components, providing maximum flexibility but requiring significant operational overhead. Elastic Cloud provides a managed option, though some advanced security features require a paid subscription tier.

**Scalability:**  
The Elastic Stack is architecturally designed for large-scale distributed deployments and scales horizontally. Elasticsearch clusters can handle high log ingest volumes effectively when properly sized.

**Detection Capabilities:**  
Elastic Security provides pre-built detection rules aligned to MITRE ATT&CK and supports custom Elasticsearch Query Language (EQL) and KQL rules. SIEM analytics and machine learning-based anomaly detection are available at paid tiers.

**Integration Capabilities:**  
The Beats data shipper ecosystem provides a wide range of log collection agents (Winlogbeat for Windows, Filebeat for file-based logs, Packetbeat for network). Integration with Microsoft products is supported but may require more configuration than native Microsoft solutions.

**Cost:**  
The open-source tier provides significant capability at lower direct cost, but operational overheads and the cost of running infrastructure should be factored in. Higher-tier subscriptions required for advanced security features add to total cost.

**Operational Complexity:**  
Self-hosted Elastic deployments have high operational complexity. Cluster management, scaling, and maintenance require specialist Elastic expertise. The managed cloud option reduces this burden but at higher cost.

---

### 3.3 Microsoft Sentinel

Microsoft Sentinel is a cloud-native SIEM and SOAR (Security Orchestration, Automation and Response) platform built on Microsoft Azure. It is a fully managed SaaS service with no infrastructure to deploy or manage.

**Deployment Model:**  
Microsoft Sentinel is exclusively cloud-native, deployed within a Microsoft Azure subscription. There is no on-premises infrastructure component required. Log data is stored in an Azure Log Analytics Workspace. This model fully aligns with a cloud-first IT strategy.

**Scalability:**  
As a fully managed cloud service, Sentinel scales automatically without capacity planning. It can ingest logs from thousands of sources at petabyte scale. There are no ingestion limits imposed by infrastructure, making it well-suited to Bedminster Health's multi-site environment.

**Detection Capabilities:**  
Sentinel provides an extensive library of built-in analytics rules mapped to MITRE ATT&CK. User and Entity Behaviour Analytics (UEBA) is built in natively. Microsoft's threat intelligence feed is integrated by default. Custom detection rules are written in Kusto Query Language (KQL), which is modern and well-documented.

**Integration Capabilities:**  
Sentinel has native, first-party connectors for the entire Microsoft ecosystem including Microsoft 365, Azure Active Directory, Microsoft Defender (Endpoint, Identity, Office 365), and Azure services. Community-contributed connectors cover a wide range of third-party sources. The Data Connector gallery includes over 200 connectors including CEF/Syslog for network devices and non-Microsoft systems.

**Cost:**  
Sentinel is priced based on data ingestion volume (Pay-As-You-Go) with optional commitment tiers that reduce the per-GB cost at higher volumes. There is no infrastructure cost. The Microsoft 365 E5 licence benefit provides certain data types at no additional ingestion charge. Overall cost of ownership is generally lower than Splunk at equivalent log volumes.

**Operational Complexity:**  
Sentinel's fully managed model significantly reduces operational overhead compared to on-premises or self-hosted alternatives. Azure Monitor Agent and the legacy MMA agent provide straightforward log collection from Windows and Linux systems. The Sentinel interface is modern and accessible to security analysts with moderate training investment.

---

## 4. Platform Comparison Summary

| Criterion | Splunk | Elastic Security | Microsoft Sentinel |
|---|---|---|---|
| **Deployment Model** | On-premises / Cloud | On-premises / Cloud | Cloud-native only |
| **Scalability** | High (with planning) | High (with planning) | Very High (automatic) |
| **Detection Capabilities** | Very High | High | Very High |
| **Integration Capabilities** | Very High | High | Very High (native Microsoft) |
| **Cost** | High | Low-Medium | Medium |
| **Operational Complexity** | High | Very High | Low-Medium |
| **UEBA** | Additional module (cost) | Available (paid tier) | Built-in |
| **SOAR** | Available (SOAR add-on) | Limited | Built-in (Logic Apps) |
| **Compliance Workbooks** | Available | Limited | Built-in |

---

## 5. Recommendation: Microsoft Sentinel

### 5.1 Rationale

Based on the evaluation against Bedminster Health's requirements, **Microsoft Sentinel is recommended** as the SIEM platform for this organisation.

The recommendation is supported by the following factors:

**Alignment with Microsoft Environment**  
Bedminster Health, in common with most large healthcare organisations in Australia, operates a predominantly Microsoft environment including Windows Server, Active Directory, and Microsoft 365. Microsoft Sentinel's native integration with these technologies provides immediate and deep visibility into authentication, identity, email, and endpoint events without requiring custom integration work.

**Cloud-Native Architecture Reduces Operational Burden**  
Managing on-premises SIEM infrastructure requires dedicated specialist resources for hardware, software, and platform administration. As a fully managed cloud service, Sentinel eliminates this overhead, allowing the security team to focus on detection and response rather than platform management.

**Built-in UEBA and SOAR**  
Sentinel includes User and Entity Behaviour Analytics natively, enabling anomaly-based detection of insider threats and compromised accounts without additional licensing. The built-in SOAR capability (via Azure Logic Apps) enables automated response playbooks that can reduce mean time to respond.

**Scalability Without Capacity Planning**  
Sentinel's automatic scaling means log volume growth across additional sites, new systems, or increased event rates does not require infrastructure changes. This is particularly important for a distributed, multi-site healthcare organisation.

**Compliance Readiness**  
Built-in compliance workbooks and the Azure compliance framework support reporting obligations under ISO/IEC 27001 and the Australian Privacy Act 1988. Retention policies and data sovereignty configurations can be set to meet Australian regulatory requirements.

**Total Cost of Ownership**  
While Sentinel incurs ongoing consumption costs, the elimination of infrastructure investment and reduced operational staffing requirements generally results in a lower total cost of ownership compared to Splunk at equivalent log volumes.

### 5.2 Considerations and Mitigations

| Consideration | Mitigation |
|---|---|
| Cloud dependency | Azure high-availability SLA (99.9%+) and disaster recovery capabilities |
| Data sovereignty | Log Analytics Workspace deployed in Australian region (Australia East) |
| Non-Microsoft log sources | CEF/Syslog connectors and community data connectors cover network devices and clinical systems |
| Cost management | Commitment tier pricing and ingestion filters to manage costs at scale |
