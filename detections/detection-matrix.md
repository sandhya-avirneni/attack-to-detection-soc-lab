# Detection Matrix

## Overview

This matrix summarizes the security activities simulated in the lab, the telemetry collected, detection logic, and corresponding MITRE ATT&CK techniques.

The goal is to demonstrate how raw security telemetry can be transformed into actionable detections.

---

## Detection Coverage

| ID | Activity | Data Source | Detection / Investigation Method | MITRE ATT&CK | Result |
|---|---|---|---|---|---|
| D01 | SSH Brute-Force Simulation | `/var/log/auth.log` | Manual evaluation of ≥5 failed/invalid events from one source IP | T1110 | Threshold exceeded |
| D02 | Local Account Discovery | Linux auditd | Monitoring access to `/etc/passwd` and `/etc/group` | T1087.001 | Audit events investigated |
| D03 | Command Execution Monitoring | Linux auditd (`execve`) | Manual validation of command-execution telemetry | T1059 (contextual) | Benign telemetry validated |
| D04 | Network Reconnaissance | Nmap / tcpdump | Service enumeration and network traffic observation | T1046 | Reconnaissance documented |
| D05 | SSH Event Investigation | Splunk Cloud | Manual log ingestion and SPL search | Not independently established | Two SSH-related events analyzed |

---

## D01 — SSH Brute Force

**Data Source:**

```text id="e1b6h7"
/var/log/auth.log
```

**Detection Logic:**

```text id="9h7a0s"
≥5 failed/invalid SSH authentication attempts
from the same source IP
```

**Observed Result:**

```text id="w0z8qf"
12 failed/invalid attempts
Source: 192.168.56.101
Target: 192.168.56.103
```

**Outcome:** The documented events exceeded the manually defined analytical threshold. No automated alert was configured or generated.

---

## D02 — Local Account Discovery

**Data Source:**

```text id="7s1jqp"
auditd
```

**Monitored Files:**

```text id="7a4m1p"
/etc/passwd
/etc/group
```

**Detection Mechanism:**

Auditd file-watch rules were used to generate telemetry when these account-related files were accessed.

**MITRE ATT&CK:**

**T1087.001 — Local Account Discovery**

**Outcome:** File-access audit events were collected and manually investigated. The events provide telemetry relevant to local account discovery but do not independently establish malicious activity.

---

## D03 — Command Execution

**Data Source:**

```text id="5zrx8h"
auditd
```

**Audit Rule:**

```text id="bd4f4s"
-a always,exit -F arch=b64 -S execve -F key=command_execution
```

This rule captures command execution activity through the Linux `execve` system call.

### Validation Activity

The following benign commands were executed:

```bash
whoami
uname -a
cat /etc/os-release
```

The resulting audit events confirmed that command execution telemetry was being collected.

**Outcome:** Telemetry successfully validated.

This activity was intentionally classified as **benign** rather than malicious.

---

## D04 — Network Reconnaissance

**Tools:**

* Nmap
* tcpdump

**Target:**

```text id="04umwu"
192.168.56.103
```

Nmap was used to identify exposed services on the Ubuntu endpoint.

Network traffic was captured with tcpdump to validate network-level visibility.

**MITRE ATT&CK:**

**T1046 — Network Service Discovery**

**Outcome:** Reconnaissance activity observed.

---

## D05 — Splunk Cloud SSH Event Investigation

**Platform:** Splunk Cloud — Search & Reporting

**Log Ingestion Method:** Manual upload of SSH-related event data.

### Investigation Objective

Investigate indexed SSH-related events using Splunk Search Processing Language (SPL).

### SPL Query

```spl id="c8c6l3"
index=main "libssh" | table _time, _raw
```

### Observed Results

The search returned two SSH-related events containing:

- Source IP address: `192.168.56.101`
- SSH protocol identification information
- Event timestamps
- Raw connection-event data

### Investigation Outcome

Successfully retrieved and analyzed SSH-related security telemetry using Splunk Cloud.

This exercise demonstrated manual SIEM log ingestion, SPL querying, and security event investigation.

**Limitation:** Automated log forwarding, scheduled detection searches, and SIEM alert generation were not implemented.

**Evidence:**

![Splunk Cloud SSH Analysis](../evidence/04-splunk-cloud-ssh-analysis.png)

---

## Detection Engineering Principles

The lab demonstrates several detection engineering principles:

### Threshold-Based Detection

Repeated authentication events can be evaluated against a manually defined threshold to identify potentially suspicious activity.

The SSH simulation was evaluated using the following workflow:

```text
12 documented failed/invalid SSH authentication events
                |
                v
Manually evaluated against 5-attempt threshold
                |
                v
Threshold exceeded
                |
                v
Potential SSH brute-force activity
                |
                v
Manual investigation
```

No automated alert was generated.
```

### Multiple Telemetry Sources

Different activities require different data sources:

| Telemetry  | Primary Use             |
| ---------- | ----------------------- |
| `auth.log` | Authentication activity |
| auditd     | Endpoint activity       |
| tcpdump    | Network traffic         |
| Nmap       | Service discovery       |
| splunk cloud| Indexed log searching and SIEM investigation|

### Contextual Investigation

A detection alone does not prove compromise.

For example, the SSH detection required additional investigation to determine:

* Whether the account existed
* Whether authentication succeeded
* Whether a session was established
* Whether additional suspicious activity occurred

---

## Detection Limitations

The current lab demonstrates foundational detection engineering and manual security investigation.

Limitations include:

- Static analytical thresholds.
- Manual SSH brute-force detection validation.
- Manual log ingestion into Splunk Cloud.
- No automated SIEM alert generation.
- No automated correlation between authentication, endpoint, and network telemetry.
- Limited historical baselining.
- Limited network protocol analysis.
- No Windows endpoint telemetry.

These limitations provide opportunities for future improvements.
---

## Future Detection Improvements

Planned enhancements include:

* Sigma detection rules
* Automated log forwarding and continuous ingestion into Splunk Cloud
* Zeek network telemetry
* Suricata IDS/IPS
* Windows Sysmon telemetry
* Automated alert generation
* Behavioral baselining
* Cross-source event correlation

---

## Summary

The detection matrix demonstrates the relationship between:

**Attack Activity → Telemetry → Detection Logic → MITRE ATT&CK → Investigation**

The lab successfully validated endpoint, authentication, and network telemetry across multiple simulated security scenarios.

