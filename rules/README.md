
# Wazuh Custom Detection Rules

## Overview

This directory contains the XML detection rules used in my
Wazuh SIEM-Based SOC Monitoring & Threat Detection Lab.

The rules extend Wazuh's built-in SSH detection capabilities
and support controlled security testing and SOC investigations.

## Custom Rules

### Rule 100001 - SSH Authentication Failure (Example)

- Severity: Level 5
- Parent Rule: 5716
- Purpose: Matches SSH authentication failures originating
  from the specific IP address 1.1.1.1.
- Status: Default Wazuh example rule retained in the configuration.

### Rule 100002 - Failed Root SSH Authentication

- Severity: Level 10
- Parent Rule: 5716
- Purpose: Detects failed SSH authentication attempts
  targeting the root account.
- Detection: Uses PCRE2 exact username matching (`^root$`)
  to prevent false positives involving other usernames.
- Validation: Tested using SSH authentication events
  and verified in Wazuh alerts.

### Rule 100003 - Active Response Lab Detection

- Severity: Level 5
- Parent Rule: 5760
- Purpose: Detects controlled SSH authentication failures
  using the test account `labtest`.
- Source IP: 192.0.2.10 (documentation/test address).
- Validation: Used to trigger Wazuh Active Response and
  verify temporary firewall blocking and automatic removal.
- Safety: The test-only Active Response action was disabled
  after successful validation.

## Detection Engineering Skills Demonstrated

- Custom SIEM rule development
- SSH authentication monitoring
- Rule inheritance and event matching
- PCRE2 regular expression matching
- Alert tuning and false-positive reduction
- Controlled automated response testing
- Security event validation

## Rule Configuration

The XML file is available here:

[local_rules.xml](./local_rules.xml)

In the lab, the Wazuh Manager loads these rules from:

`/var/ossec/etc/rules/local_rules.xml`

## Related Documentation

- [SSH Root Alert Tuning](../docs/alert-tuning/SSH-Root-Alert-Tuning.md)
- [Wazuh Active Response](../docs/active-response/Wazuh-Active-Response.md)

## Important Note

These rules were developed and tested in an isolated
cybersecurity lab environment.

The Active Response blocking action is currently disabled
after controlled testing.
