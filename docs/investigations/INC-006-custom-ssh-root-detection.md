# INC-006 — Custom SSH Root Authentication Detection

## 1. Investigation Overview

| Field | Details |
|---|---|
| Investigation ID | INC-006 |
| Category | Custom SIEM Detection Engineering |
| Platform | Wazuh SIEM 4.14.8 |
| Detection Rule | 100002 |
| Severity | Level 10 — High |
| Monitored Endpoint | ubuntu-soc-agent |
| Log Source | systemd journald |
| Status | Successfully Tested and Verified |
| Date | 15 October 2026 |

## 2. Objective

Develop, test, and deploy a custom Wazuh detection rule to identify failed SSH authentication attempts targeting the root account on a monitored Ubuntu server.

## 3. Detection Rule Configuration

The custom detection rule was added to:

`/var/ossec/etc/rules/local_rules.xml`

```xml
<rule id="100002" level="10">
  <if_sid>5716</if_sid>
  <match>for root from</match>
  <description>Custom SOC Alert: Failed SSH authentication attempt for root account.</description>
  <group>authentication_failed,ssh_root_attempt,</group>
</rule>
```

The rule extends Wazuh's SSH authentication-failure detection and matches log messages containing `for root from`.

## 4. Implementation and Testing

1. Backed up the existing `local_rules.xml` file.
2. Created custom detection Rule 100002.
3. Validated the rule configuration using `wazuh-analysisd -t`.
4. Tested a simulated failed root SSH authentication event using `wazuh-logtest`.
5. Restarted Wazuh Manager to activate the rule.
6. Generated a controlled failed root SSH authentication attempt from the Windows host.
7. Verified the resulting alert in Wazuh Manager logs and the Wazuh Dashboard.

### Test Command

Executed from Windows PowerShell:

```powershell
ssh -o PubkeyAuthentication=no -o PreferredAuthentications=password -o NumberOfPasswordPrompts=1 root@192.168.56.10
```

The authentication attempt was rejected.

### Observed SSH Log

```text
Oct 15 03:22:14 soc-siem sshd[2175]: Failed password for root from 192.168.56.1 port 63863 ssh2
```

## 5. Detection Results

| Field | Observed Value |
|---|---|
| Rule ID | 100002 |
| Rule Level | 10 |
| Source IP | 192.168.56.1 |
| Source Port | 63863 |
| Target Username | root |
| Agent ID | 001 |
| Agent Name | ubuntu-soc-agent |
| Event Source | journald |
| Result | Alert Generated Successfully |

The Wazuh agent collected the SSH authentication failure from the systemd journal. The Wazuh Manager processed the event and generated a Level 10 alert using the custom detection rule.

### Dashboard Evidence

![Custom SSH Root Authentication Detection](https://raw.githubusercontent.com/Piranavi-Sasikaran/wazuh-siem-soc-lab/main/screenshots/ssh-detection/100002.png)

## 6. Security Analysis

Direct SSH authentication attempts against the root account are security-relevant because root provides privileged access to Linux systems.

The test demonstrated that the custom Wazuh rule can detect a failed root password authentication attempt and extract useful investigation fields, including the source IP address, source port, and targeted username.

The monitored Ubuntu server was configured with `PermitRootLogin no`, and the attempted login was rejected.

**No successful root login or system compromise was observed.**

The source address `192.168.56.1` belongs to the controlled lab environment. This event was intentionally generated for detection testing.

## 7. Limitations and Future Improvements

- The rule was verified against a failed-password SSH log pattern.
- Other root authentication failure formats may require additional detection logic.
- Future improvements could include repeated-attempt correlation, false-positive tuning, and MITRE ATT&CK mapping.
- High-severity alerts should be reviewed in context rather than automatically treated as confirmed compromise.

## 8. Conclusion

Successfully implemented and validated a custom Wazuh detection rule for failed SSH authentication attempts targeting the root account.

The investigation demonstrated practical experience in SIEM detection engineering, log analysis, rule validation, controlled security testing, and alert investigation.
