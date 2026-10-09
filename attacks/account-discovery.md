# Linux Account Discovery Simulation

## 1. Objective

Simulate local account discovery activity on an Ubuntu Linux endpoint and investigate the resulting security telemetry using Linux auditd.

This exercise demonstrates how SOC analysts can monitor access to sensitive system information, examine audit records, and identify behavior associated with account discovery.

## 2. Lab Environment

| Component | Details |
|---|---|
| Monitored Endpoint | Ubuntu Linux 22.04 |
| Endpoint IP | 192.168.56.103 |
| Monitoring Tool | Linux auditd |
| Investigation Tool | ausearch |
| Monitored Files | /etc/passwd, /etc/group |
| MITRE ATT&CK | T1087.001 — Account Discovery: Local Account |

## 3. Attack Scenario

Linux stores local account and group information in system files.

Two relevant files are:

- `/etc/passwd` — Contains local user account information.
- `/etc/group` — Contains local group information.

Attackers may attempt to access these files to identify existing accounts and groups before performing additional actions.

In this controlled exercise, access to local account information was monitored to generate and investigate audit telemetry.

## 4. Audit Monitoring Configuration

Linux auditd was configured with the following monitoring rules:

```bash
-w /etc/passwd -p r -k account_discovery
-w /etc/group -p r -k account_discovery
```

### Rule Explanation

| Parameter | Purpose |
|---|---|
| `-w` | Specifies the file to monitor |
| `-p r` | Monitors read access |
| `-k account_discovery` | Assigns a searchable audit key |

An additional rule was configured for `/etc/shadow` under the separate key `credential_access`.

## 5. Account Discovery Simulation

The exercise involved accessing local account-information files in the controlled Ubuntu environment.

Representative commands for this activity include:

```bash
cat /etc/passwd
cat /etc/group
```

These commands illustrate the type of file access monitored by the configured audit rules.

## 6. Security Event Investigation

Audit events were investigated using:

```bash
sudo ausearch -k account_discovery -i
```

The `-k` option filters events by audit key, while `-i` interprets supported fields into a more readable format.

### Observed Telemetry

The reviewed audit output included:

- File paths associated with account-information access
- Audit event timestamps
- System call records
- Process-related information
- The `account_discovery` audit key

These records demonstrated that the endpoint was collecting file-access telemetry relevant to local account discovery.

## 7. MITRE ATT&CK Mapping

**Technique:** T1087.001 — Account Discovery: Local Account

**Tactic:** Discovery

Local account discovery involves obtaining information about user accounts present on a system.

Monitoring access to `/etc/passwd` and `/etc/group` can provide useful supporting telemetry during an investigation.

However, access to these files can also occur during legitimate administrative activity. An audit event alone does not establish malicious intent.

## 8. Detection Considerations

Potential indicators worth investigating include:

- Unexpected processes accessing account-information files
- Account discovery activity following suspicious authentication attempts
- Unusual sequences of discovery commands
- Access by unexpected user accounts
- Repeated access occurring alongside other suspicious activity

These are analytical considerations, not automated detection rules implemented in this exercise.

## 9. Results and Limitations

| Test | Result |
|---|---|
| auditd monitoring rules configured | Completed |
| Account-information file access monitored | Completed |
| Audit events retrieved with ausearch | Completed |
| Manual security investigation | Completed |
| Automated alert generation | Not implemented |
| Malicious intent confirmed | No — controlled lab activity |

Some audit records may also reflect legitimate system or investigation processes accessing monitored files. Therefore, individual records require process-level review before attribution to a specific simulated action.

## 10. Evidence

![Account Discovery Audit Evidence 1](../evidence/03-account-discovery-auditd-1.png)

![Account Discovery Audit Evidence 2](../evidence/03-account-discovery-auditd-2.png)

The screenshot demonstrates auditd records associated with monitored account-information files.

## 11. Key Takeaways

- Linux auditd provides visibility into file-access activity.
- Account discovery telemetry can support security investigations.
- Audit keys simplify searching for relevant events.
- Legitimate system activity can generate similar audit records.
- Contextual analysis is necessary before classifying an event as malicious.

## 12. Conclusion

This exercise demonstrated how Linux auditd can monitor access to local account-information files and how the resulting telemetry can be investigated.

It strengthened practical skills in Linux security monitoring, audit-log analysis, MITRE ATT&CK mapping, and evidence-based incident investigation.

---

[Return to Main Project README](../README.md)
