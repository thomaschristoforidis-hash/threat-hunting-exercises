# Threat Hunting Exercises Lab Report

**Hypothesis-Driven Hunting Practice Using Lab Telemetry**

| Field | Details |
|-------|---------|
| **Author** | Thomas Christoforidis |
| **Role Target** | Junior SOC Analyst / Cyber Security Analyst |
| **Date** | August 2026 |
| **Focus** | Structured threat hunting across endpoint and SIEM data |
| **Supporting Labs** | Sysmon, Windows Event Logs, PowerShell, Wazuh, Sentinel, Atomic Red Team, ATT&CK Mapping |

---

## 1. Executive Summary

This report documents a series of **threat hunting exercises** performed in a controlled home-lab environment. Using a simple hypothesis-driven methodology, hunts were conducted against telemetry from Sysmon, Windows Security Event Logs, PowerShell Script Block Logging, and SIEM platforms (Wazuh / Microsoft Sentinel). Atomic Red Team was used to generate known activity for validation hunts.

The work demonstrates the ability to form hypotheses, select appropriate data sources, query and analyse results, reach evidence-based conclusions, and link findings to MITRE ATT&CK — core skills for proactive SOC work.

**Key outcomes achieved:**
- Application of a repeatable hunting methodology
- Completion of multiple foundational hunts (process execution, failed logons, PowerShell abuse, network/DNS, account changes)
- Correlation of Sysmon, Security, and PowerShell telemetry
- Validation hunting against Atomic Red Team activity
- Documentation of findings, conclusions, and detection opportunities
- Reinforcement of ATT&CK mapping through hunting results

---

## 2. Lab Objectives

| # | Objective | Status |
|---|-----------|--------|
| 1 | Apply a structured hypothesis-driven hunting process | Achieved |
| 2 | Hunt across Sysmon, Security Event Logs, and PowerShell logs | Achieved |
| 3 | Perform hunts for unusual processes, failed logons, and PowerShell abuse | Achieved |
| 4 | Correlate host telemetry with SIEM views where available | Achieved |
| 5 | Validate visibility using Atomic Red Team generated activity | Achieved |
| 6 | Document hunts with clear conclusions and ATT&CK mapping | Achieved |
| 7 | Identify detection improvement opportunities | Achieved |

---

## 3. Hunting Methodology Used

Each exercise followed the same lightweight cycle:

1. **Hypothesis** – Specific, testable statement about possible suspicious activity
2. **Data Sources** – Selection of Sysmon, Security, PowerShell, or SIEM data
3. **Query / Filter** – Targeted search using Event Viewer filters, KQL, or SIEM queries
4. **Analysis** – Interpretation of results in context
5. **Conclusion** – Benign / Suspicious / Malicious / Inconclusive, with supporting evidence
6. **Next Steps** – Detection rule ideas, coverage gaps, or further investigation

This approach keeps hunts focused and produces consistent, reviewable documentation.

---

## 4. Exercises Performed (Summary)

| Exercise | Hypothesis Focus | Primary Data Sources | Outcome Type |
|----------|------------------|----------------------|--------------|
| Unusual Process Execution | Suspicious LOLBins or command lines | Sysmon ID 1, Security 4688 | Visibility confirmed; detection opportunity noted |
| Failed Logon Patterns | Brute force / password spraying | Security 4625 / 4624 | Patterns identifiable; timeline possible |
| PowerShell Abuse | Encoded or suspicious PowerShell | Sysmon ID 1 + Event 4104 | High-fidelity visibility when logging enabled |
| Network from Unusual Processes | Unexpected outbound connections | Sysmon ID 3 | Process-to-network linkage demonstrated |
| DNS Queries | Suspicious or rare domains | Sysmon ID 22 | Process + domain correlation possible |
| Account & Group Changes | Unauthorised account or privilege changes | Security 4720, 4732, etc. | Clear audit trail available |
| Persistence Indicators | Run keys / tasks / services | Sysmon + Security | Baseline hunting feasible |
| Atomic Red Team Validation | Reconstruct known technique activity | Multi-source | Successful reconstruction of selected tests |

---

## 5. Detailed Case Study – PowerShell Encoded Command Hunt

**Hypothesis:** Encoded PowerShell commands (`-enc`) have been executed and can be fully investigated using available telemetry.

**Data Sources:**  
- Sysmon Event ID 1 (process creation)  
- PowerShell Operational Event ID 4104 (Script Block Logging)  
- Optional: Wazuh / Sentinel for centralised view

**Process:**
1. Filtered Sysmon for `powershell.exe` process creation events containing `-enc` or `-EncodedCommand` in the CommandLine.
2. Recorded timestamp, user, parent process, and full command line.
3. Pivoted to PowerShell Event ID 4104 in the same time window to retrieve script block content.
4. Assessed whether existing detections would have alerted.
5. Documented findings and ATT&CK mapping (T1059.001).

**Conclusion:** When Script Block Logging and Sysmon are both present, encoded PowerShell activity is highly investigable. The combination of process context (Sysmon) and actual code content (4104) provides strong evidence for both detection and response.

**Detection Opportunity:** Command-line based analytics rules looking for `-enc`, `-w hidden`, `-ep bypass`, and common download cradles, enriched with parent process and user context.

---

## 6. Results and Evidence

### Skills Demonstrated

| Skill | Evidence |
|-------|----------|
| Hypothesis formation | Clear, testable hypotheses for each exercise |
| Data source selection | Appropriate use of Sysmon, Security, PowerShell, SIEM |
| Querying & filtering | Targeted Event Viewer / KQL / SIEM searches |
| Multi-source correlation | Pivoting between process, script block, and authentication data |
| Evidence-based conclusions | Documented benign vs suspicious outcomes |
| ATT&CK linkage | Techniques recorded for hunted activity |
| Detection engineering feedback | Rule ideas and coverage notes generated from hunts |

### Key Observations

- Sysmon Event ID 1 is the single most useful host data source for process-focused hunts.
- PowerShell Script Block Logging transforms encoded command investigations from difficult to straightforward.
- Authentication hunts (4624/4625) pair naturally with process and network data for stronger narratives.
- Atomic Red Team provides an excellent “known-good bad activity” source for validating both visibility and detections.
- Many hunts surface “Partial” coverage — telemetry exists, but formal alerting can still be improved.

---

## 7. Challenges Encountered and Resolutions

| Challenge | Resolution |
|-----------|------------|
| Broad hypotheses produced too much noise | Narrowed scope to one technique or pattern per hunt |
| Timezone / timestamp alignment across tools | Standardised on UTC and noted exact event times for pivoting |
| Distinguishing lab noise from interesting activity | Used Atomic Red Team for controlled positive examples and baselines of normal activity |
| Documentation consistency | Applied the same hunt report template to every exercise |

---

## 8. Lessons Learned

1. **Good hypotheses make good hunts** — Vague goals lead to unfocused querying; specific hypotheses produce clear outcomes.
2. **Telemetry quality determines hunt quality** — Sysmon + Script Block Logging dramatically increase what can be discovered.
3. **Correlation is the real skill** — The value is rarely in a single event; it is in linking process, script, authentication, and network data.
4. **Negative results are still useful** — Confirming that expected activity is *not* present is a valid hunting outcome.
5. **Hunting feeds detection engineering** — Almost every hunt suggests a rule idea or a coverage gap to close.

---

## 9. Recommendations and Next Steps

- Fully document at least three hunts using the standard template for the portfolio.
- Convert one hunt finding into a new Wazuh or Sentinel analytics rule and re-test.
- Expand the ATT&CK Detection Mapping matrix with results from these exercises.
- Practise presenting a hunt end-to-end (hypothesis → query → finding → conclusion) as interview preparation.
- Progress to multi-stage hunts that reconstruct an Atomic Red Team technique across several data sources.

---

## 10. Conclusion

The Threat Hunting Exercises lab successfully applied a structured, hypothesis-driven approach to real telemetry generated by the existing home-lab stack. Through focused hunts covering process execution, authentication, PowerShell, network, DNS, and account activity, practical proactive investigation skills were developed and documented.

This work ties together Sysmon, Windows Event Logs, PowerShell monitoring, SIEM platforms, Atomic Red Team, and ATT&CK mapping into an active defence capability — a strong addition to a Junior SOC Analyst portfolio.

---

## Appendix – Hunt Report Template

```
Hunt Title: 
Date: 
Hypothesis: 
Data Sources Used: 
Queries / Filters Applied: 
Findings: 
Conclusion: 
MITRE ATT&CK Techniques: 
Detection Opportunity: 
Evidence / Screenshots: 
```

---

**End of Report**

*This document is intended for professional portfolio and interview use. Completed individual hunt write-ups and screenshots can be added as exercises are finished.*
