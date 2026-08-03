# Microsoft Sentinel Home Lab Guide

**Cloud-Native SIEM Practice for SOC Analysts**  
Version 1.0 | August 2026

---

## 1. Overview & Objectives

This guide helps you build a practical **Microsoft Sentinel** laboratory. Sentinel is Microsoft’s cloud-native SIEM and SOAR platform. It is widely used in enterprise environments (including many Australian organisations) and is a high-value skill for Junior SOC Analyst roles.

**Primary Goals:**
- Create an Azure Log Analytics workspace and enable Microsoft Sentinel
- Ingest sample or lab data at minimal/no cost
- Explore the Sentinel interface (Incidents, Analytics, Hunting, Workbooks)
- Write and run basic KQL (Kusto Query Language) queries
- Create or enable analytics rules and investigate resulting incidents
- Map activity to MITRE ATT&CK
- Produce portfolio-ready documentation

**Why this matters for SOC roles:**
- Sentinel is one of the leading cloud SIEMs
- KQL skills transfer to other Microsoft security tools
- Incident investigation, hunting, and rule tuning are core SOC tasks
- Complements on-prem labs (Wazuh, Sysmon, Event Logs) with a cloud SIEM perspective

---

## 2. Cost-Aware Lab Options

| Option | Cost | Best For | Notes |
|--------|------|----------|-------|
| **Official Microsoft Sentinel Training Lab** | Very low / free | Fastest start, realistic multi-source data | Pre-recorded data (~10–20 MB), 31-day free trial |
| **New workspace + free trial** | Free for 31 days (up to 10 GB/day) | Full control | Enable Sentinel on a new Log Analytics workspace |
| **Azure free account + $200 credit** | Effectively free for short labs | Broader Azure practice | Credit covers most small usage |
| **Connect a lab VM** | Trial + small ingestion | Real endpoint data | Use Azure Monitor Agent; keep volume low |

**Recommendation for portfolio:** Start with the official Training Lab or a clean workspace under the 31-day free trial, then optionally add a small amount of real lab data.

---

## 3. Prerequisites

- Microsoft account
- Azure subscription (Free account is sufficient): https://azure.microsoft.com/free
- Owner or Contributor rights on a resource group
- Modern browser

Optional but useful:
- Existing Windows lab machine (for later agent connection)
- Basic familiarity with Azure Portal

---

## 4. Phase 1 – Create Workspace and Enable Sentinel

1. Sign in to the [Azure Portal](https://portal.azure.com).
2. Search for **Log Analytics workspaces** → **Create**.
3. Choose subscription, create a new resource group (e.g. `rg-sentinel-lab`), give the workspace a name (e.g. `law-sentinel-lab`), select a region close to you, and create.
4. Once deployed, search for **Microsoft Sentinel** → **Create**.
5. Select the Log Analytics workspace you just created and add Microsoft Sentinel.
6. You are automatically enrolled in the **31-day free trial** (up to 10 GB/day free ingestion and analysis on new workspaces).

---

## 5. Phase 2 – Get Data Quickly (Recommended Paths)

### Path A – Official Microsoft Sentinel Training Lab (Easiest)

Microsoft provides a ready-made training lab that deploys sample data and artefacts:

- Repository / guidance: Azure-Sentinel Training Lab on GitHub
- Deploy via Azure Cloud Shell or the marketplace solution
- Ingests small pre-recorded datasets from multiple sources
- Creates detection rules, incidents, workbooks, etc.
- Extremely low cost because data volume is tiny

Follow the official onboarding script or “Microsoft Sentinel Training Lab Solution” in the marketplace.

### Path B – Content Hub & Sample Data

1. In Sentinel, go to **Content management → Content hub**.
2. Install useful solutions (e.g. Windows Security Events, Azure Activity, training content).
3. Use **Data connectors** to enable free or low-volume sources (Azure Activity is free).
4. Optionally upload or generate small test datasets.

### Path C – Connect a Windows Lab Machine (Advanced)

1. Install the **Azure Monitor Agent** on a Windows VM.
2. Configure the agent to send Security Events / Sysmon (via custom logs or Data Collection Rules) to your workspace.
3. Keep the machine powered on only when testing and monitor ingestion volume.

---

## 6. Core Skills to Practise

### 6.1 Kusto Query Language (KQL)

Essential basic patterns:

```kusto
// Recent Security events
SecurityEvent
| where TimeGenerated > ago(24h)
| take 10

// Failed logons
SecurityEvent
| where EventID == 4625
| summarize count() by Account, Computer, bin(TimeGenerated, 1h)

// Process creation (if Sysmon or 4688 data present)
SecurityEvent
| where EventID == 4688
| project TimeGenerated, Computer, SubjectUserName, NewProcessName, CommandLine
```

Practise in **Logs** (or the Hunting blade).

### 6.2 Analytics Rules

- Browse **Analytics → Rule templates**
- Enable a few high-value scheduled rules
- Create a simple custom rule based on a KQL query
- Observe the resulting **Incidents**

### 6.3 Incident Investigation

- Open an incident
- Review the incident graph / entities
- Examine related alerts and raw events
- Add comments, change status, assign severity
- Practise the investigation timeline mindset

### 6.4 Hunting & Workbooks

- Use **Hunting** queries
- Explore or create a simple **Workbook** for visualisation
- Bookmark interesting findings

### 6.5 MITRE ATT&CK

- Many analytics rules and incidents show ATT&CK tactics/techniques
- Map the techniques you see back to your previous ATT&CK Detection Mapping lab

---

## 7. Suggested Lab Exercises

1. Deploy workspace + enable Sentinel under the free trial.
2. Deploy the official Training Lab (or enable a few connectors + sample data).
3. Run 5–10 basic KQL queries and save useful ones.
4. Enable 3–5 analytics rule templates and wait for / trigger incidents.
5. Fully investigate one incident (entities, timeline, related alerts, conclusion).
6. Create one simple custom analytics rule.
7. Map at least three observed techniques to MITRE ATT&CK.
8. Take screenshots of: workspace overview, a KQL query result, an incident page, and a rule.

---

## 8. Linking to Previous Labs

| Previous Lab | How it connects |
|--------------|-----------------|
| Sysmon / Windows Event Logs | Potential data source via Azure Monitor Agent |
| Wazuh SIEM | Complementary on-prem SIEM – compare workflows |
| Atomic Red Team | Generate activity that can be detected in Sentinel if logs are forwarded |
| MITRE ATT&CK Mapping | Re-use and extend your coverage matrix with Sentinel detections |
| PowerShell Monitoring | Script Block / process events become rich Sentinel data |

Even if you keep Sentinel as a pure cloud / sample-data lab, the investigation skills transfer directly.

---

## 9. Cost Control Tips

- Stay inside the 31-day free trial window when possible
- Prefer the official Training Lab (tiny data volume)
- Avoid continuous high-volume agents unless you monitor costs
- Set a budget alert in Azure Cost Management
- Delete the resource group when the lab is finished if you no longer need it

---

## 10. Portfolio Documentation Tips

Capture:
- Azure / Sentinel workspace overview
- Example KQL query and results
- Analytics rule configuration
- Incident investigation screenshots (entities, timeline, status)
- Short write-up of one full investigation
- MITRE techniques observed

---

## 11. References

- Microsoft Sentinel documentation: https://learn.microsoft.com/en-us/azure/sentinel/
- Microsoft Sentinel Training Lab (GitHub / official)
- KQL quick reference
- Azure free account: https://azure.microsoft.com/free
- Your existing Sysmon, Wazuh, Event Log, Atomic Red Team, and ATT&CK mapping labs

---

**Document prepared for practical SOC and cloud SIEM skill development.**  
Always monitor costs and stay within free trial / credit limits.
