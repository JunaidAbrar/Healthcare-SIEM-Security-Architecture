# Cost-Benefit Analysis

**Document:** SIEM Cost-Benefit Analysis — Bedminster Health  
**Version:** 1.0  
**Classification:** Confidential

---

## 1. Purpose

This document provides a financial and operational justification for implementing Microsoft Sentinel as Bedminster Health's SIEM platform. It analyses the anticipated costs of implementation and ongoing operations against the security, operational, and compliance benefits the platform delivers.

---

## 2. Benefits of SIEM Implementation

### 2.1 Improved Security Visibility

Without a SIEM, Bedminster Health's security team has no unified view of security events across its infrastructure. Logs from over 2,000 workstations, 150 servers, and multiple network devices are collected in silos — if reviewed at all — in isolation from one another.

Microsoft Sentinel provides a single pane of glass for all security events across the organisation:

- Consolidated view of all authentication, network, endpoint, and cloud events.
- Interactive dashboards and workbooks providing real-time operational insight.
- Threat hunting capability enabling proactive investigation across the full log dataset.
- Cross-source correlation exposing attack chains that are invisible when reviewing individual log sources.

This capability materially improves the organisation's ability to understand and manage its security posture on an ongoing basis.

---

### 2.2 Faster Incident Detection

The cost of a cyber security incident increases significantly with dwell time — the period between initial compromise and detection. Industry research consistently shows that undetected breaches cause substantially greater harm than those detected and contained quickly.

**Current state:** Incidents at Bedminster Health are detected reactively, often days or weeks after initial compromise, when operational impact makes the breach impossible to ignore.

**Post-SIEM state:** Real-time detection rules and UEBA enable detection of indicators of compromise within minutes to hours, dramatically reducing dwell time.

| Metric | Current State | Post-SIEM Target |
|---|---|---|
| Mean Time to Detect (MTTD) | Days–Weeks | Hours |
| Mean Time to Respond (MTTR) | Days | Hours |
| Incidents detected proactively | ~0% | >70% |

Reducing MTTD and MTTR reduces the scope and cost of incidents — including data recovery, regulatory response, legal costs, and reputational damage.

---

### 2.3 Compliance Support

Healthcare organisations in Australia face compliance obligations under multiple regulatory frameworks:

- **Australian Privacy Act 1988** — Requires reasonable security measures to protect personal information, including health records.
- **ISO/IEC 27001** — International standard for information security management systems.
- **Mandatory Data Breach Notification Scheme** — Requires notification of eligible data breaches to the Office of the Australian Information Commissioner (OAIC) and affected individuals.

Microsoft Sentinel supports compliance through:

- **Audit log retention:** All security events retained for defined periods to support audit requirements.
- **Compliance workbooks:** Pre-built reporting aligned to ISO 27001 controls.
- **Evidence for auditors:** Demonstrable security monitoring capability evidences reasonable security measures.
- **Breach detection:** SIEM-enabled rapid detection reduces breach scope, limiting notification obligations.
- **Access auditing:** Comprehensive audit trails of who accessed what data, supporting Privacy Act compliance.

Failure to meet these obligations can result in regulatory penalties, mandatory public notification of breaches, and reputational damage.

---

### 2.4 Reduced Breach Risk

Healthcare data breaches are among the most costly across all industry sectors. Factors include:

- High regulatory penalties.
- Cost of mandatory breach notification (postal and media notification requirements).
- Patient notification and credit monitoring obligations.
- Legal liability from affected patients.
- Reputational damage affecting patient trust and staff confidence.
- Operational disruption during incident response and recovery.

The Ponemon Institute's annual Cost of a Data Breach report consistently shows healthcare as the highest-cost sector for data breaches, with average costs exceeding USD $10 million per incident globally. Australian incidents, while typically smaller in scale, involve significant regulatory and reputational consequences.

A functional SIEM capability that reduces the probability and scope of breaches provides a direct financial return through risk reduction.

---

### 2.5 Operational Efficiency

Beyond security benefits, SIEM implementation delivers operational efficiency gains:

- **Reduced manual log review:** Analysts no longer need to manually query multiple disparate log systems; all investigation is conducted through a single platform.
- **Automated response:** Playbooks automate time-consuming triage tasks (account suspension, endpoint isolation, notification) that currently require manual intervention.
- **Standardised processes:** Documented SOC workflows reduce reliance on individual knowledge and improve consistency of response.
- **Evidence quality:** Comprehensive, centralised logs improve forensic investigation quality when incidents do occur.

---

## 3. Estimated Costs

### 3.1 Microsoft Sentinel Licensing

Microsoft Sentinel pricing is based on data ingestion volume into the Log Analytics Workspace, with two pricing models:

**Pay-As-You-Go:** Per GB ingested, no commitment.  
**Commitment Tiers:** Fixed daily rate for committed ingestion volumes, typically 10–65% lower per-GB cost than Pay-As-You-Go.

Based on the estimated log volume of approximately 34 GB/day (as documented in the architecture design), the recommended tier is the **100 GB/day commitment tier** (providing headroom for growth).

> **Note:** Actual pricing varies by Azure region and is subject to Microsoft's published pricing. The below figures are illustrative estimates based on publicly available Azure pricing as a planning guide. **These figures must be validated against current Microsoft Azure pricing at the time of procurement.** Azure pricing is subject to change; consult the [Azure Pricing Calculator](https://azure.microsoft.com/en-au/pricing/calculator/) and engage a Microsoft licensing specialist before finalising budget commitments.

| Component | Estimated Annual Cost (AUD) |
|---|---|
| Microsoft Sentinel (100 GB/day commitment tier) | ~$200,000–$280,000 |
| Log Analytics Workspace storage (archive tier, 1 year) | ~$15,000–$25,000 |
| Azure infrastructure (log forwarder VMs, 6 sites) | ~$15,000–$20,000 |
| **Estimated Total Licensing and Infrastructure** | **~$230,000–$325,000/year** |

> Microsoft 365 E5 customers receive a daily data grant for certain log types (Azure AD, Office 365, Defender) at no additional charge, which may substantially reduce ingestion costs if Bedminster Health holds appropriate licences.

---

### 3.2 Implementation Costs

One-time implementation costs include professional services for architecture, deployment, and configuration.

| Component | Estimated Cost (AUD) |
|---|---|
| Security architecture and design | ~$40,000–$60,000 |
| Sentinel deployment and configuration | ~$50,000–$80,000 |
| Detection rule development | ~$30,000–$50,000 |
| SOC workflow and playbook development | ~$20,000–$35,000 |
| Training — SOC team | ~$10,000–$20,000 |
| **Estimated Total Implementation Cost** | **~$150,000–$245,000** |

---

### 3.3 Ongoing Operational Costs

| Component | Estimated Annual Cost (AUD) |
|---|---|
| SOC analyst staffing (1–2 FTE, partly offset by existing team) | ~$120,000–$180,000 |
| Security engineer (platform maintenance, 0.5 FTE) | ~$60,000–$90,000 |
| Annual rule review and threat model update | ~$15,000–$25,000 |
| Training and certifications | ~$5,000–$10,000 |
| **Estimated Total Ongoing Operational Cost** | **~$200,000–$305,000/year** |

---

### 3.4 Total Cost of Ownership Summary (3-Year)

| Cost Category | Year 1 | Year 2 | Year 3 |
|---|---|---|---|
| Licensing and Infrastructure | ~$230,000–$325,000 | ~$230,000–$325,000 | ~$230,000–$325,000 |
| Implementation (one-time) | ~$150,000–$245,000 | — | — |
| Operations (staffing, maintenance) | ~$200,000–$305,000 | ~$200,000–$305,000 | ~$200,000–$305,000 |
| **Annual Total** | **~$580,000–$875,000** | **~$430,000–$630,000** | **~$430,000–$630,000** |
| **3-Year Total** | | | **~$1,440,000–$2,135,000** |

---

## 4. Return on Investment

### 4.1 Risk Reduction Value

The primary financial return from SIEM investment is risk reduction. A reasonable estimate of annual risk exposure for Bedminster Health without a SIEM includes:

| Risk Scenario | Estimated Probability (Annual) | Estimated Impact (AUD) | Expected Loss |
|---|---|---|---|
| Significant ransomware incident | 15% | $3,000,000–$8,000,000 | $450,000–$1,200,000 |
| Data breach (patient records) | 20% | $1,500,000–$5,000,000 | $300,000–$1,000,000 |
| Regulatory penalty | 10% | $500,000–$2,000,000 | $50,000–$200,000 |
| **Total Annualised Risk Exposure** | | | **~$800,000–$2,400,000/year** |

A SIEM implementation is expected to reduce the probability of a significant incident by 40–60% through improved detection and response capability, and to reduce the cost of incidents that do occur by limiting breach scope through faster containment.

**Estimated annual risk reduction value:** $320,000–$1,440,000

---

### 4.2 ROI Assessment

| Metric | Value |
|---|---|
| 3-Year Total Cost of Ownership | ~$1,440,000–$2,135,000 |
| 3-Year Risk Reduction Value (conservative) | ~$960,000–$4,320,000 |
| Net 3-Year Benefit (conservative estimate) | Positive in Year 1–2 |

Even under conservative assumptions, the SIEM investment delivers positive return over a three-year period when risk reduction value is factored in.

---

## 5. Recommendation

The cost-benefit analysis supports proceeding with Microsoft Sentinel implementation. The combination of risk reduction value, compliance enablement, operational efficiency gains, and the relatively moderate total cost of ownership compared to alternative platforms presents a compelling business case.

The CISO should present this analysis to executive leadership with a recommendation to approve the SIEM programme budget, including Phase 1 implementation funding in the current financial year.
