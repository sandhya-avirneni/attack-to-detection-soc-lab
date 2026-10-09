# False-Positive Testing and Detection Validation

## Objective

Evaluate how legitimate Linux administrative activity appears in security telemetry and understand how SOC analysts distinguish benign behavior from potentially malicious activity.

The goal was to validate Linux audit logging and examine the importance of context when developing security detections.

## Lab Environment

| Component | Configuration |
|---|---|
| Monitored Endpoint | Ubuntu Linux |
| Endpoint IP | 192.168.56.103 |
| Monitoring Tool | Linux auditd |
| Audit Rule | execve system call monitoring |
| Detection Key | command_execution |
| Investigation Method | Manual audit-log analysis |

## 1. Benign Activity Simulation

The following commands were executed on the Ubuntu endpoint:

```bash
whoami
uname -a
cat /etc/os-release
```

These commands were selected because they are commonly used for legitimate system administration and troubleshooting.

### Expected Behavior

- `whoami` displays the current username.
- `uname -a` displays system and kernel information.
- `cat /etc/os-release` displays operating system information.

Although these commands may also appear during attacker reconnaissance, their execution alone does not establish malicious intent.

## 2. Audit Logging Validation

The following audit rule was configured to monitor command execution:

```bash
-a always,exit -F arch=b64 -S execve -F key=command_execution
```

Recorded events were reviewed using:

```bash
sudo ausearch -k command_execution -i
```

### Observations

The benign commands generated command-execution telemetry.

The audit records provided visibility into process execution, allowing the activity to be reviewed during an investigation.

## 3. False-Positive Analysis

| Activity | Potential Security Interpretation | Assessment |
|---|---|---|
| `whoami` | User-context discovery | Benign validation activity |
| `uname -a` | System information discovery | Benign validation activity |
| `cat /etc/os-release` | Operating system discovery | Benign validation activity |

The commands were intentionally executed as part of authorized lab testing.

Therefore, they were not classified as malicious based solely on the resulting audit events.

## 4. Detection Tuning Considerations

The exercise highlighted several considerations for improving detection quality:

- **Process context:** Determine which process executed the command.
- **User context:** Review the account associated with the activity.
- **Execution frequency:** Determine whether the activity is isolated or repeated.
- **Event correlation:** Investigate whether related suspicious activity occurred.
- **Expected behavior:** Compare the activity with normal administrative operations.

These are recommended tuning considerations rather than separately implemented automated detection rules.

## 5. Results

| Test | Result |
|---|---|
| Benign commands executed | Completed |
| Command-execution audit events generated | Validated |
| Manual event review | Completed |
| Benign activity classification | Completed |
| Automated false-positive suppression | Not implemented |
| Automated SIEM alert testing | Not implemented |

## 6. Key Lessons Learned

1. Security telemetry provides visibility but does not automatically establish malicious behavior.
2. Legitimate administrative commands can resemble attacker discovery techniques.
3. Detection engineering requires investigation context and appropriate tuning.
4. Analysts should avoid treating every command-execution event as an incident.
5. Manual validation is a useful foundation for future automated detection development.

## 7. Future Improvements

- Develop Sigma-style detection rules for suspicious Linux activity.
- Create SPL searches to investigate command-execution patterns.
- Compare benign and suspicious command sequences.
- Implement and test automated alert thresholds.
- Evaluate false-positive rates using a larger event dataset.

## Conclusion

This exercise demonstrated how Linux auditd records legitimate command-execution activity and why contextual analysis is essential for accurate security detection.

The results reinforced the importance of distinguishing benign system administration from potentially suspicious behavior before escalating security events.
