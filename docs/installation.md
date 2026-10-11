# Wazuh SIEM SOC Lab — Installation and Deployment Guide

## 1. Project Overview

This document describes the deployment and configuration of a Wazuh-based Security Information and Event Management (SIEM) laboratory.

The project was developed to gain practical experience in SIEM deployment, endpoint monitoring, security event analysis, and SOC operations.

**SIEM Platform:** Wazuh 4.14.8  
**Host Operating System:** Windows 11  
**SIEM Server:** Ubuntu 24.04 running in WSL2  
**Monitored Endpoint:** Ubuntu Server 24.04 running in Oracle VirtualBox  
**Deployment Model:** All-in-one Wazuh installation

## 2. Hardware Requirements and Environment

The laboratory was deployed on a laptop with the following specifications:

| Component | Specification |
|---|---|
| Processor | AMD Ryzen 5 7520U |
| Physical RAM | 16 GB |
| Host OS | Windows 11 |
| Virtualization | WSL2 and Oracle VirtualBox |
| Wazuh Version | 4.14.8 |
| Monitored Endpoints | 1 Ubuntu Server |

The deployment uses WSL2 for the Wazuh central components and VirtualBox for the monitored endpoint.

This architecture separates security monitoring infrastructure from the endpoint generating security events.

## 3. WSL2 Ubuntu Environment

### 3.1 Installing Ubuntu

Windows Subsystem for Linux 2 (WSL2) was used to host the Wazuh SIEM server.

Example installation command in Windows PowerShell:

```powershell
wsl --install -d Ubuntu
```

After installation, the Ubuntu environment can be accessed using:

```powershell
wsl -d Ubuntu
```

### 3.2 Resource Allocation

The WSL2 environment was configured with the following resource limits:

```ini
[wsl2]
memory=9GB
processors=4
swap=4GB
```

These settings are stored in:

`%USERPROFILE%\.wslconfig`

After updating the configuration, WSL2 can be restarted from Windows PowerShell:

```powershell
wsl --shutdown
```

The settings take effect when the Ubuntu distribution starts again.

### 3.3 Enabling systemd

Systemd was enabled to manage Wazuh services inside Ubuntu.

The `/etc/wsl.conf` file contains:

```ini
[boot]
systemd=true
```

This allows Wazuh components to be managed using `systemctl`.

## 4. Wazuh Central Component Installation

Wazuh 4.14.8 was installed inside WSL2 Ubuntu using an all-in-one deployment.

The deployment includes three central components.

### 4.1 Wazuh Manager

The Wazuh Manager receives and analyzes security events collected from monitored endpoints.

Its responsibilities include:

- Receiving security logs from Wazuh Agents.
- Applying detection rules.
- Correlating suspicious events.
- Generating security alerts.

### 4.2 Wazuh Indexer

The Wazuh Indexer stores and indexes security alerts for searching and investigation.

### 4.3 Wazuh Dashboard

The Wazuh Dashboard provides a web interface for:

- Security monitoring.
- Threat Hunting.
- Endpoint management.
- File Integrity Monitoring.
- Security Configuration Assessment.

### 4.4 Installation Method

The official Wazuh installation assistant supports an all-in-one deployment.

For Wazuh 4.14, the standard installation workflow uses:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

**Note:** These commands document the standard installation method for the Wazuh 4.14 release series. The lab uses Wazuh 4.14.8; installation details and available packages should be verified against the official documentation when reproducing the deployment.

Official documentation:

https://documentation.wazuh.com/current/installation-guide/

## 5. Starting and Verifying Wazuh Services

The Wazuh services are managed through systemd.

### Start the services

```bash
sudo systemctl start wazuh-manager
sudo systemctl start wazuh-indexer
sudo systemctl start wazuh-dashboard
```

### Verify service status

```bash
sudo systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard
```

Expected output:

```text
active
active
active
```

All three services were verified as running successfully during the laboratory setup.

## 6. Accessing the Wazuh Dashboard

The Wazuh Dashboard is accessed from the Windows host using:

https://localhost

The administrator account is used to access the web interface.

**Security note:** Administrator passwords, API credentials, and authentication tokens are not included in this repository.

The laboratory uses a self-signed HTTPS certificate. The browser may display a certificate trust warning until the certificate is explicitly trusted.

## 7. VirtualBox Ubuntu Server Deployment

Oracle VirtualBox was used to create a separate Ubuntu Server virtual machine.

### Virtual Machine Configuration

| Component | Configuration |
|---|---|
| VM Name | SOC-SEIM-lab |
| Operating System | Ubuntu Server 24.04 |
| Hostname | soc-siem |
| Virtual CPUs | 1 |
| RAM | 8 GB during initial deployment |
| Disk | Approximately 49 GB |
| Network Adapter 1 | NAT |
| Network Adapter 2 | Host-only |

The VM was configured with one virtual CPU after higher CPU allocations caused stability problems in the virtualized environment.

**Resource consideration:** Running WSL2 and VirtualBox simultaneously on a 16 GB laptop requires careful memory management. Reducing the endpoint VM's RAM may improve overall stability.

## 8. Network Configuration

The Ubuntu VM uses two network interfaces.

### NAT Network

The NAT adapter provides network connectivity.

Observed address:

```text
10.0.2.15
```

### Host-Only Network

The host-only adapter allows the Windows host to communicate directly with the Ubuntu VM.

| Device | IP Address |
|---|---|
| Windows host-only interface | 192.168.56.1 |
| Ubuntu VM host-only interface | 192.168.56.10 |

### SSH Connectivity Test

The Ubuntu endpoint was accessed from Windows PowerShell using:

```powershell
ssh socadmin@192.168.56.10
```

This connection was also used as part of controlled SSH security monitoring exercises.

## 9. Wazuh Agent Installation

Wazuh Agent 4.14.8 was installed on the Ubuntu Server virtual machine.

The agent was configured to communicate with the Wazuh Manager running inside WSL2.

### Agent Configuration

| Field | Value |
|---|---|
| Agent Name | ubuntu-soc-agent |
| Agent ID | 001 |
| Endpoint OS | Ubuntu Server 24.04 |
| Wazuh Manager | WSL2 Ubuntu |
| Agent Version | 4.14.8 |

The installation used the Wazuh Debian package and supplied the manager address and agent name.

Example configuration parameters:

```bash
WAZUH_MANAGER="<WSL2_MANAGER_IP>"
WAZUH_AGENT_NAME="ubuntu-soc-agent"
```

The manager address must be replaced with the actual reachable WSL2 IP address.

### Start the Wazuh Agent

```bash
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

### Verify Agent Status

```bash
sudo systemctl is-active wazuh-agent
```

Expected output:

```text
active
```

The endpoint was successfully enrolled and displayed as **Active** in the Wazuh Dashboard.

## 10. Wazuh Network Communication

The Wazuh Agent communicates with the Manager using the following ports.

| Port | Protocol | Purpose |
|---|---|---|
| 1514 | TCP | Agent event communication |
| 1515 | TCP | Agent enrollment |
| 443 | HTTPS | Dashboard access |

Connectivity between the Ubuntu VM and WSL2 Manager was tested during deployment.

**Important:** WSL2 IP addresses may change after restarting Windows or WSL2. If the agent becomes disconnected, its configured manager address and network connectivity should be checked.

## 11. SSH Security Monitoring

SSH authentication logs were collected from the Ubuntu endpoint through journald.

Two detection scenarios were tested.

### Test 1 — Invalid SSH Username

An SSH connection using a nonexistent username generated Wazuh rule 5710.

**Detection:** `sshd: Attempt to login using a non-existent user`

### Test 2 — Repeated Suspicious SSH Activity

Repeated SSH connections using a nonexistent username generated Wazuh rule 5712.

**Detection:** `sshd: brute force trying to get access to the system. Non existent user.`

The second test involved repeated pre-authentication events, not confirmed password submissions.

Investigation reports:

- [INC-001 — SSH Invalid User Detection](investigations/INC-001-ssh-invalid-user.md)
- [INC-002 — SSH Brute-Force Detection](investigations/INC-002-ssh-brute-force.md)

## 12. File Integrity Monitoring Configuration

Real-time File Integrity Monitoring was enabled on the Ubuntu endpoint.

### Create the testing directory

```bash
sudo mkdir -p /opt/soc-fim-lab
```

### Configure Wazuh Syscheck

The following entry was added inside the existing `<syscheck>` section of:

`/var/ossec/etc/ossec.conf`

```xml
<directories check_all="yes" realtime="yes">/opt/soc-fim-lab</directories>
```

### Restart the Agent

```bash
sudo systemctl restart wazuh-agent
```

### Verify Monitoring

```bash
sudo grep -i 'real-time' /var/ossec/logs/ossec.log | tail -10
```

The agent logs confirmed monitoring of the configured directory.

### FIM Detection Results

| Test | Rule ID | Result |
|---|---|---|
| File creation | 554 | Detected |
| File modification | 550 | Detected |
| File deletion | 553 | Detected |

Full investigation:

[INC-003 — File Integrity Monitoring](investigations/INC-003-file-integrity-monitoring.md)

## 13. Deployment Verification

The following checks were completed successfully:

- Wazuh Manager service running.
- Wazuh Indexer service running.
- Wazuh Dashboard accessible through HTTPS.
- Ubuntu endpoint enrolled and active.
- SSH authentication alerts generated.
- Repeated SSH activity correlated.
- File creation detected.
- File modification detected.
- File deletion detected.

## 14. Challenges and Troubleshooting

### Limited Hardware Resources

Running WSL2 and VirtualBox simultaneously placed significant demands on the host's 16 GB RAM.

Resource allocation was adjusted to improve stability.

### Virtual Machine CPU Stability

The VirtualBox Ubuntu VM experienced stability issues with two virtual CPUs. Using one virtual CPU allowed the environment to operate more reliably.

### Dynamic WSL2 IP Address

The WSL2 Manager IP address can change after a restart, potentially interrupting agent connectivity.

The Manager address and network connectivity should be checked when troubleshooting a disconnected agent.

### Self-Signed HTTPS Certificate

The Wazuh Dashboard uses a self-signed certificate in the laboratory environment.

A browser certificate warning may appear when accessing the dashboard.

## 15. Security Considerations

This laboratory is intended for authorized security testing and educational use.

- No production systems were targeted.
- SSH simulations were performed against the owned Ubuntu VM.
- FIM tests used a dedicated laboratory directory.
- Administrative credentials are excluded from the repository.
- The environment is not configured for production availability or high availability.

## 16. Conclusion

The Wazuh SIEM laboratory was successfully deployed using WSL2 Ubuntu and a VirtualBox Ubuntu endpoint.

The environment supports centralized security monitoring, SSH authentication detection, event correlation, File Integrity Monitoring, and SOC investigation workflows.

The deployment provides a foundation for future enhancements, including Security Configuration Assessment, vulnerability detection, custom rules, and additional security monitoring scenarios.
