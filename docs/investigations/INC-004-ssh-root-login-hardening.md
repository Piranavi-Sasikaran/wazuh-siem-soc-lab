# INC-004 — SSH Root Login Security Hardening

## 1. Investigation Overview

| Field | Details |
|---|---|
| Investigation ID | INC-004 |
| Category | Security Configuration Assessment (SCA) |
| Tool | Wazuh SIEM 4.14.8 |
| Monitored Endpoint | ubuntu-soc-agent |
| Operating System | Ubuntu Server 24.04 LTS |
| Security Benchmark | CIS Ubuntu Linux 24.04 LTS Benchmark v1.0.0 |
| SCA Check ID | 35659 |
| Severity | Configuration Hardening Finding |
| Investigation Status | Closed — Remediation Verified |
| Date | October 13, 2026 |

## 2. Objective

Identify and remediate an SSH security configuration weakness using Wazuh Security Configuration Assessment (SCA).

The investigation focuses on disabling direct SSH root login to reduce the risk of unauthorized privileged access and align the monitored Ubuntu endpoint with CIS security recommendations.

## 3. Initial Security Finding

During the initial SCA assessment, Wazuh identified the following failed security check:

- **Check ID:** 35659
- **Description:** Ensure sshd PermitRootLogin is disabled.
- **Initial Result:** Failed
- **Benchmark:** CIS Ubuntu Linux 24.04 LTS

The initial effective SSH configuration returned:

```bash
permitrootlogin without-password
```

This setting disabled password-based root authentication but could still permit direct root access through SSH keys.

## 4. Investigation and Analysis

The SSH server configuration was inspected using:

```bash
sudo sshd -T | grep '^permitrootlogin '
```

The Wazuh SCA policy was also reviewed to understand the failed compliance conditions.

The assessment required:

1. The effective SSH configuration to return `permitrootlogin no`.
2. The main `/etc/ssh/sshd_config` file to explicitly contain `PermitRootLogin no`.

The investigation identified that both requirements needed to be satisfied for the Wazuh SCA check to pass.

## 5. Security Remediation

Before applying the configuration changes, administrative privileges for the `socadmin` account were verified.

A dedicated SSH hardening configuration file was created:

**File:** `/etc/ssh/sshd_config.d/00-soc-hardening.conf`

```text
PermitRootLogin no
```

The main SSH configuration file was also updated:

**File:** `/etc/ssh/sshd_config`

```text
PermitRootLogin no
```

The SSH configuration was validated using:

```bash
sudo sshd -t
```

The effective configuration was verified using:

```bash
sudo sshd -T | grep '^permitrootlogin '
```

**Verified output:**

```text
permitrootlogin no
```

The SSH service was reloaded, and successful remote access through the regular `socadmin` account was verified.

## 6. Wazuh SCA Validation

After remediation, the Wazuh Agent was restarted to trigger a new Security Configuration Assessment scan.

```bash
sudo systemctl restart wazuh-agent
```

The updated assessment confirmed:

| Check | Before | After |
|---|---|---|
| SCA 35659 — Disable SSH Root Login | Failed | Passed |

### Overall SCA Assessment Results

| Metric | Initial Assessment | Latest Assessment |
|---|---:|---:|
| Compliance Score | 47% | 50% |
| Passed Checks | 112 | 120 |
| Failed Checks | 125 | 117 |
| Not Applicable | 42 | 42 |

The overall compliance score increased by three percentage points between assessments. Multiple checks changed status during this period, so the full improvement cannot be attributed solely to this SSH remediation.

## 7. Evidence

### Successful SCA Validation

![Wazuh SCA Check 35659 Passed](../../screenshots/security-configuration-assessment/35659.png)

The screenshot confirms that Wazuh SCA Check 35659 was marked **Passed** following remediation.

## 8. Security Impact

Disabling direct SSH root login improves the endpoint's security posture by:

- Preventing direct remote authentication as the root account.
- Encouraging administrators to authenticate using individual user accounts.
- Supporting accountability through individual authentication and sudo activity.
- Reducing exposure associated with direct privileged SSH access.
- Improving alignment with CIS Ubuntu Linux security recommendations.

## 9. Investigation Outcome

**Status: Closed — Remediation Verified**

The SSH root login configuration weakness was successfully remediated.

The effective SSH configuration was validated, regular administrative access remained available, and Wazuh confirmed that SCA Check 35659 changed from **Failed** to **Passed**.

This investigation demonstrates practical experience in security configuration assessment, Linux system hardening, compliance verification, and SOC documentation.

---

**Environment:** Authorized educational SOC laboratory.

**Note:** This investigation documents a security configuration finding, not an actual security breach.
