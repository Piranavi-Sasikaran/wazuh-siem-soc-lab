# Wazuh Active Response — Automated SSH Threat Containment

## Objective

Implement and validate automated threat containment using Wazuh Active Response in a controlled SOC lab environment.

The experiment demonstrates how Wazuh can detect a simulated SSH authentication failure, trigger a firewall response, and automatically remove the temporary block.

## Lab Environment

| Component | Configuration |
|---|---|
| SIEM Platform | Wazuh |
| Wazuh Manager | Ubuntu on WSL2 |
| Monitored Endpoint | Ubuntu Server VM |
| Agent ID | 001 |
| Log Source | systemd journald |
| Response Command | firewall-drop |
| Response Duration | 60 seconds |

## 1. Custom Detection Rule

Created Rule `100003` in `/var/ossec/etc/rules/local_rules.xml`.

```xml
<rule id="100003" level="5">
  <if_sid>5760</if_sid>
  <user type="pcre2">^labtest$</user>
  <srcip>192.0.2.10</srcip>
  <description>Active Response Lab: Controlled SSH authentication failure.</description>
  <group>active_response_lab,authentication_failed,</group>
</rule>
```

The rule matches SSH authentication failures for the test username `labtest` originating from the documentation-only IP address `192.0.2.10`.

## 2. Active Response Configuration

Configured Wazuh Manager to execute `firewall-drop` on Agent `001` when Rule `100003` generates an alert.

```xml
<active-response>
  <disabled>no</disabled>
  <command>firewall-drop</command>
  <location>defined-agent</location>
  <agent_id>001</agent_id>
  <rules_id>100003</rules_id>
  <timeout>60</timeout>
</active-response>
```

## 3. Test Procedure

Generated a simulated SSH authentication failure on the monitored Ubuntu VM:

```bash
logger -t 'sshd[12345]' 'Failed password for labtest from 192.0.2.10 port 54321 ssh2'
```

The Wazuh Agent collected the event through journald and forwarded it to the Manager.

Rule `100003` generated an alert, triggering the configured Active Response.

## 4. Firewall Verification

Checked the Ubuntu VM's INPUT chain:

```bash
sudo iptables -L INPUT -n --line-numbers
```

During the response, the firewall contained a DROP rule for `192.0.2.10`.

After the configured timeout, the DROP rule disappeared.

## 5. Results

| Test | Result |
|---|---|
| Custom rule detection | Passed |
| Agent-to-Manager log forwarding | Passed |
| Active Response execution | Passed |
| Automatic IP blocking | Passed |
| Automatic removal | Passed |
| Response timeout | Approximately 60 seconds |

## 6. Evidence

See [Active Response Evidence](../../evidence/active-response/README.md) for the experiment summary and sanitized execution records.

## 7. Key Learnings

- Configuring custom Wazuh detection rules
- Parsing SSH authentication logs
- Connecting detection rules to Active Response
- Executing automated firewall containment
- Verifying response execution and timeout behavior
- Collecting and documenting SOC investigation evidence

## Security Considerations

This experiment used a synthetic SSH authentication event and a documentation-only source IP. No real attack was performed.

The custom rule was intentionally restricted to the lab username and source IP to avoid triggering the response for unrelated authentication failures.

This is a controlled proof of concept, not a production-ready automated blocking policy.
