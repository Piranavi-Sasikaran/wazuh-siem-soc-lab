# SOC Monitoring Dashboard — Wazuh SIEM

## 1. Objective

Review and validate centralized security monitoring using the Wazuh Threat Hunting Dashboard.

The objective was to analyze security alert trends, investigate high-severity detections, and review MITRE ATT&CK mappings from a monitored Ubuntu endpoint.

## 2. Lab Environment

| Component | Configuration |
|---|---|
| SIEM Platform | Wazuh 4.14.8 |
| Wazuh Manager | Ubuntu on WSL2 |
| Monitored Endpoint | Ubuntu Server 24.04 |
| Agent Name | ubuntu-soc-agent |
| Agent ID | 001 |
| Monitoring Period | September 17–October 17, 2026 |
| Interface | Wazuh Threat Hunting Dashboard |

## 3. Monitoring Results

The dashboard was filtered using:

`agent.name:"ubuntu-soc-agent"`

The following results were observed over the 30-day monitoring period.

| Metric | Result |
|---|---|
| Total Security Alerts | 800 |
| High-Severity Alerts (Level 10+) | 3 |
| SSH Brute-Force Alerts (Rule 5712) | 1 |
| Custom Root SSH Alerts (Rule 100002) | 2 |

These values represent the alerts returned for the selected agent and monitoring period.

## 4. High-Severity Alert Investigation

The following query was used to identify high-severity security events:

`agent.name:"ubuntu-soc-agent" and rule.level >= 10`

Three alerts were identified.

| Date | Rule ID | Severity | Description |
|---|---|---|---|
| October 8, 2026 | 5712 | Level 10 | SSH brute-force detection |
| October 15, 2026 | 100002 | Level 10 | Failed root SSH authentication |
| October 15, 2026 | 100002 | Level 10 | Failed root SSH authentication |

The results demonstrated successful historical retrieval of both built-in and custom Wazuh detection alerts.

## 5. Alert Severity Analysis

The dashboard visualized alerts across multiple severity levels, including Levels 3, 4, 5, 7, 9, and 10.

This enabled the identification of routine system activity, configuration assessment findings, and higher-severity security detections.

## 6. MITRE ATT&CK Analysis

The dashboard displayed alert mappings associated with several MITRE ATT&CK techniques, including:

- Valid Accounts
- Password Guessing
- Brute Force
- Remote Services
- SSH
- File Deletion

These mappings represent classifications associated with Wazuh alerts and do not establish that real attacks occurred.

## 7. Monitoring Dashboard Evidence

The following screenshot demonstrates security alert monitoring, severity distribution, MITRE ATT&CK mappings, and monitored endpoint activity using Wazuh Threat Hunting.

**Supporting Evidence:**

- [View SOC Monitoring Dashboard Screenshot](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/screenshots/soc-monitoring/soc-monitoring-dashboard.png)
- [View SOC Monitoring Evidence README](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/screenshots/soc-monitoring/README.md)
## 8. Key Learnings

- Monitoring endpoint security alerts through Wazuh
- Filtering events using Dashboard Query Language (DQL)
- Investigating high-severity security alerts
- Analyzing alert trends over time
- Reviewing MITRE ATT&CK mappings
- Validating historical security event retention
- Documenting SOC monitoring evidence

## 9. Conclusion

The monitoring review confirmed that Wazuh successfully collected, indexed, and visualized security alerts from the Ubuntu endpoint.

Historical detection events remained available for investigation, including SSH brute-force alerts and custom failed-root-authentication alerts.

The dashboard provides a practical foundation for security monitoring, alert triage, and SOC investigation workflows.

**Note:** All security simulations were conducted within an authorized laboratory environment.
