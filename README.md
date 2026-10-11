# Wazuh SIEM SOC Lab

**A hands-on Security Operations Center (SOC) laboratory built using Wazuh SIEM, WSL2, and VirtualBox.**

## Project Overview

This project demonstrates the deployment and operation of a Wazuh-based SIEM environment for centralized security monitoring, threat detection, and incident investigation.

The lab simulates suspicious SSH authentication activity and file integrity changes on an Ubuntu endpoint. Security events are collected, analyzed, and investigated through Wazuh.

The project is being developed to strengthen practical skills in SOC operations, Blue Team security, and incident response.

## Technologies Used

| Technology | Purpose |
|---|---|
| Wazuh 4.14.8 | SIEM and security monitoring |
| Ubuntu Server 24.04 | Monitored endpoint |
| WSL2 Ubuntu | Wazuh central server |
| Oracle VirtualBox | Endpoint virtualization |
| Windows 11 | Host operating system |
| Linux SSH | Authentication monitoring |
| Wazuh Syscheck | File Integrity Monitoring |
| MITRE ATT&CK | Detection technique mapping |

## System Architecture

The lab consists of three main environments:

- **Windows 11 Host:** Runs VirtualBox and WSL2 and provides browser access to the dashboard.
- **WSL2 Ubuntu:** Hosts the Wazuh Manager, Indexer, and Dashboard.
- **VirtualBox Ubuntu Server:** Runs the Wazuh Agent and generates monitored security events.

The Ubuntu endpoint sends security events to the Wazuh Manager for analysis and alert generation.

[View Architecture Documentation](docs/architecture.md)

## Implemented Security Detection Scenarios

| Detection Scenario | Wazuh Rule | Severity | Status |
|---|---|---|---|
| SSH invalid-user detection | 5710 | Level 5 | Completed |
| Repeated SSH activity correlation | 5712 | Level 10 | Completed |
| File creation detection | 554 | Level 5 | Completed |
| File modification detection | 550 | Level 7 | Completed |
| File deletion detection | 553 | Level 7 | Completed |

### SSH Authentication Monitoring

Simulated SSH connections using a nonexistent username to evaluate Wazuh's authentication monitoring and event correlation capabilities.

The lab generated individual authentication alerts and a higher-severity alert for repeated suspicious SSH activity.

**Investigations:**

- [INC-001 — SSH Invalid User Detection](docs/investigations/INC-001-ssh-invalid-user.md)
- [INC-002 — SSH Brute-Force Detection](docs/investigations/INC-002-ssh-brute-force.md)

### File Integrity Monitoring

Configured real-time monitoring for `/opt/soc-fim-lab` using Wazuh Syscheck.

Successfully detected:

- File creation
- File modification
- File deletion

**Investigation:**

[INC-003 — File Integrity Monitoring](docs/investigations/INC-003-file-integrity-monitoring.md)

## SOC Investigation Reports

| Incident ID | Investigation | Status |
|---|---|---|
| INC-001 | SSH Invalid User Detection | Closed — Authorized Test |
| INC-002 | Repeated SSH Activity Detection | Closed — Authorized Test |
| INC-003 | File Integrity Monitoring | Closed — Authorized Test |

Each report documents the test scenario, observed alerts, analysis, and investigation outcome.

## Documentation

- [System Architecture](docs/architecture.md)
- [Installation and Deployment Guide](docs/installation.md)
- [INC-001: SSH Invalid User Detection](docs/investigations/INC-001-ssh-invalid-user.md)
- [INC-002: SSH Brute-Force Detection](docs/investigations/INC-002-ssh-brute-force.md)
- [INC-003: File Integrity Monitoring](docs/investigations/INC-003-file-integrity-monitoring.md)

## Skills Demonstrated

- SIEM deployment and configuration
- Wazuh Agent installation and management
- Centralized security log collection
- SSH authentication monitoring
- Security event correlation
- Real-time File Integrity Monitoring
- Security alert investigation
- MITRE ATT&CK mapping
- SOC incident documentation
- Linux administration and troubleshooting

## Planned Enhancements

The following features are planned for future development:

- [ ] Security Configuration Assessment (SCA)
- [ ] Vulnerability detection and analysis
- [ ] Custom Wazuh detection rules
- [ ] Additional security attack simulations
- [ ] Alert tuning and false-positive analysis
- [ ] Active Response testing
- [ ] Final SOC monitoring dashboard review

## Project Status

**In Progress — Core SIEM deployment, SSH monitoring, and File Integrity Monitoring completed.**

This project is an educational security laboratory. All simulations are performed within an authorized testing environment.
