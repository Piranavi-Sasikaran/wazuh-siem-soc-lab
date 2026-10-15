# SSH Root Authentication Alert Tuning

## 1. Objective

Improve the accuracy of a custom Wazuh detection rule that identifies failed SSH authentication attempts targeting the root account.

The goal was to reduce false-positive alerts while preserving detection of genuine authentication failures.

## 2. Environment

- SIEM Platform: Wazuh 4.14.8
- Wazuh Manager: Ubuntu 24.04 on WSL2
- Monitored Endpoint: Ubuntu Server 24.04
- Agent: ubuntu-soc-agent (ID: 001)
- Log Source: systemd journald
- Detection Rule: 100002
- Alert Severity: Level 10

## 3. Original Detection Rule

The initial custom rule used text matching to identify failed root authentication attempts.

```xml
<rule id="100002" level="10">
  <if_sid>5716</if_sid>
  <match>for root from</match>
  <description>Custom SOC Alert: Failed SSH authentication attempt for root account.</description>
  <group>authentication_failed,ssh_root_attempt,</group>
</rule>
```

## 4. False-Positive Identification

During testing, the following synthetic SSH log incorrectly triggered Rule 100002:

```text
Oct 15 18:10:12 soc-siem sshd[12345]: Failed password for root frombackup from 192.168.56.1 port 54325 ssh2
```

Although Wazuh did not extract a destination username, the rule matched the substring `for root from`.

This demonstrated that relying on a text substring could produce misleading high-severity alerts.

## 5. Rule Tuning

The original text-matching condition was replaced with an exact username match.

```xml
<rule id="100002" level="10">
  <if_sid>5716</if_sid>
  <user type="pcre2">^root$</user>
  <description>Custom SOC Alert: Failed SSH authentication attempt for root account.</description>
  <group>authentication_failed,ssh_root_attempt,</group>
</rule>
```

The regular expression `^root$` requires an exact match against the username field evaluated by Wazuh.

## 6. Validation and Testing

The updated rule configuration was validated using:

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

The configuration validation completed successfully with exit code 0.

The rule was then tested using `wazuh-logtest`.

| Test Scenario | Result |
|---|---|
| Failed root password authentication | Rule 100002, Level 10 |
| Failed root public-key authentication | Rule 100002, Level 10 |
| Synthetic false-positive event | Rule 5760, Level 5 |
| Real failed root SSH authentication | Rule 100002, Level 10 |

## 7. Live Detection Verification

Following validation, the Wazuh Manager was restarted to activate the updated rule.

A controlled failed SSH password authentication attempt was generated from Windows against the Ubuntu Server VM.

**Observed alert details:**

- Timestamp: 2026-10-15 18:17:45 (+05:30)
- Rule ID: 100002
- Severity: Level 10
- Agent: ubuntu-soc-agent
- Source IP: 192.168.56.1
- Target Username: root
- Authentication Result: Failed password
- Log Source: journald

The live alert confirmed that the tuned rule continued detecting failed root authentication attempts.

## 8. Outcome

The tuning exercise demonstrated that exact username matching was more accurate than the original substring-based condition for the tested SSH events.

The synthetic false-positive event no longer triggered the custom high-severity alert, while genuine failed root password and public-key authentication events remained detectable.

## 9. Skills Demonstrated

- SIEM detection engineering
- Custom Wazuh rule development
- False-positive identification and analysis
- Regular-expression-based rule tuning
- Log decoding and analysis
- Detection validation using Wazuh Logtest
- Live security event verification

**Note:** These results demonstrate improved accuracy for the tested scenarios, not the elimination of all possible false positives.
