# Wazuh SIEM SOC Lab

**A hands-on Security Operations Center (SOC) laboratory built using Wazuh SIEM, WSL2, and VirtualBox.**

## Project Overview

This project demonstrates the deployment and operation of a Wazuh-based SIEM environment for centralized security monitoring, threat detection, event correlation, and SOC incident investigation.

The laboratory simulates SSH authentication anomalies, repeated SSH activity triggering brute-force alerts, and file integrity changes on an Ubuntu endpoint. Security events are collected, analyzed, and investigated through the Wazuh Dashboard.

The project focuses on developing practical skills in SOC operations, Blue Team security, threat detection, and incident response.

## Technologies Used

| Technology | Purpose |
|---|---|
| Wazuh 4.14.8 | SIEM and security monitoring |
| Ubuntu Server 24.04 | Monitored endpoint |
| WSL2 Ubuntu | Wazuh central server |
| Oracle VirtualBox | Endpoint virtualization |
| Windows 11 | Host operating system |
| Linux SSH | Authentication and brute-force alert detection |
| Wazuh Syscheck | Real-time File Integrity Monitoring |
| MITRE ATT&CK | Threat detection technique mapping |

## System Architecture

The laboratory consists of three main environments:

- **Windows 11 Host:** Runs VirtualBox and WSL2 and provides browser access to the Wazuh Dashboard.
- **WSL2 Ubuntu:** Hosts the Wazuh Manager, Indexer, and Dashboard.
- **VirtualBox Ubuntu Server:** Runs the Wazuh Agent and generates monitored security events.

The Wazuh Agent collects security events from the Ubuntu endpoint and forwards them to the Wazuh Manager for analysis and alert generation.

[View Architecture Documentation](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/architecture.md)

## Implemented Security Detection Scenarios

| Detection Scenario | Wazuh Rule | Severity | Status |
|---|---|---|---|
| SSH Invalid-User Detection | 5710 | Level 5 | Completed |
| SSH Brute-Force Alert Detection | 5712 | Level 10 | Completed |
| File Creation Detection | 554 | Level 5 | Completed |
| File Modification Detection | 550 | Level 7 | Completed |
| File Deletion Detection | 553 | Level 7 | Completed |

### SSH Authentication & Brute-Force Detection

Conducted controlled SSH authentication simulations using a nonexistent username to evaluate Wazuh's authentication monitoring and brute-force event correlation capabilities.

**Detection Results:**

- **Rule 5710 (Level 5):** Detected SSH authentication attempts involving a nonexistent username.
- **Rule 5712 (Level 10):** Triggered a correlated SSH brute-force alert after repeated invalid-user connection events from the same source IP.
- Analyzed SSH logs, source IP addresses, authentication-related events, and Wazuh rule metadata to investigate suspicious activity.

**Technical Note:** The brute-force alert was generated from repeated invalid-user and pre-authentication connection events rather than confirmed password-guessing attempts.

**SOC Investigations:**

- [INC-001 — SSH Invalid User Detection](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/investigations/INC-001-ssh-invalid-user.md)
- [INC-002 — SSH Brute-Force Detection](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/investigations/INC-002-ssh-brute-force.md)

### File Integrity Monitoring (FIM)

Configured real-time File Integrity Monitoring using Wazuh Syscheck to monitor changes within `/opt/soc-fim-lab` on the Ubuntu endpoint.

**Detection Results:**

- **Rule 554 (Level 5):** Successfully detected file creation.
- **Rule 550 (Level 7):** Successfully detected file modification through integrity checksum changes.
- **Rule 553 (Level 7):** Successfully detected file deletion.

Investigated FIM alerts and analyzed file paths, event types, detection rules, and MITRE ATT&CK mappings.

**SOC Investigation:**

[INC-003 — File Integrity Monitoring](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/investigations/INC-003-file-integrity-monitoring.md)

## SOC Investigation Reports

| Incident ID | Investigation | Status |
|---|---|---|
| INC-001 | SSH Invalid User Detection | Closed — Authorized Test |
| INC-002 | SSH Brute-Force Detection | Closed — Authorized Test |
| INC-003 | File Integrity Monitoring | Closed — Authorized Test |

Each investigation report includes:

- Security simulation objectives and execution steps.
- Relevant Wazuh detection rules and alert details.
- Security event analysis and MITRE ATT&CK mapping.
- Investigation findings and incident classification.
- Supporting screenshots and detection evidence.

## Documentation

- [System Architecture](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/architecture.md)
- [Installation and Deployment Guide](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/installation.md)
- [INC-001: SSH Invalid User Detection](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/investigations/INC-001-ssh-invalid-user.md)
- [INC-002: SSH Brute-Force Detection](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/investigations/INC-002-ssh-brute-force.md)
- [INC-003: File Integrity Monitoring](https://github.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/blob/main/docs/investigations/INC-003-file-integrity-monitoring.md)

## Skills Demonstrated

- SIEM deployment and configuration
- Wazuh Agent installation and management
- Centralized security log collection and analysis
- SSH authentication and brute-force alert detection
- Security event correlation
- Real-time File Integrity Monitoring
- Threat detection and security alert investigation
- MITRE ATT&CK mapping
- SOC incident analysis and documentation
- Linux system administration and troubleshooting

## Planned Enhancements

The following capabilities are planned for future development:

- [ ] Security Configuration Assessment (SCA)
- [ ] Vulnerability detection and analysis
- [ ] Custom Wazuh detection rules
- [ ] Additional security attack simulations
- [ ] Alert tuning and false-positive analysis
- [ ] Active Response testing
- [ ] Final SOC monitoring dashboard review

## Project Status

**In Progress — Core SIEM deployment, SSH authentication and brute-force alert detection, File Integrity Monitoring, and initial SOC investigations completed.**

This project is an educational cybersecurity laboratory. All simulations are performed within an authorized testing environment.
