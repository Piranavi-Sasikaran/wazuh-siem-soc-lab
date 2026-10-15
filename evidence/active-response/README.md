# Wazuh Active Response Evidence

This directory contains evidence collected during a controlled Wazuh Active Response experiment.

## Experiment Overview

- **Detection Rule:** 100003
- **Wazuh Agent:** ubuntu-soc-agent (001)
- **Test Event:** Simulated SSH authentication failure
- **Test Source IP:** 192.0.2.10 (documentation-only address)
- **Response:** firewall-drop
- **Block Duration:** 60 seconds

## Verified Results

1. Wazuh successfully detected the simulated SSH authentication failure.
2. Custom Rule 100003 generated a security alert.
3. Active Response automatically added a firewall DROP rule for the test IP.
4. The DROP rule was automatically removed after approximately 60 seconds.
5. The Active Response execution log recorded the response operations.

## Evidence

The experiment was verified using Wazuh alerts, Linux iptables output, and Active Response execution logs.

**Note:** This experiment used synthetic authentication logs in an isolated SOC lab. It did not involve a real SSH attack.
