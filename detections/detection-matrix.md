# Detection Matrix

## Overview

This matrix summarizes the security activities simulated in the lab, the telemetry collected, detection logic, and corresponding MITRE ATT&CK techniques.

The goal is to demonstrate how raw security telemetry can be transformed into actionable detections.

---

## Detection Coverage

| ID  | Activity                | Data Source         | Detection Logic                                | MITRE ATT&CK | Result                |
| --- | ----------------------- | ------------------- | ---------------------------------------------- | ------------ | --------------------- |
| D01 | SSH Brute Force         | `/var/log/auth.log` | ≥5 failed/invalid attempts from same source IP | T1110        | ✅ Detected            |
| D02 | Local Account Discovery | auditd              | Access to `/etc/passwd` or `/etc/group`        | T1087.001    | ✅ Detected            |
| D03 | Command Execution       | auditd `execve`     | Monitor process/command execution events       | T1059        | ✅ Telemetry validated |
| D04 | Network Reconnaissance  | tcpdump / Nmap      | Network traffic and service discovery activity | T1046        | ✅ Observed            |

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

**Outcome:** Detection threshold exceeded.

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

**Outcome:** Activity detected and investigated.

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

**T1046 — Network Service Scanning**

**Outcome:** Reconnaissance activity observed.

---

## Detection Engineering Principles

The lab demonstrates several detection engineering principles:

### Threshold-Based Detection

Repeated events can be grouped into a meaningful security signal.

Example:

```text id="v0i4x3"
12 failed SSH attempts
>
5-attempt threshold
=
Potential brute-force alert
```

### Multiple Telemetry Sources

Different activities require different data sources:

| Telemetry  | Primary Use             |
| ---------- | ----------------------- |
| `auth.log` | Authentication activity |
| auditd     | Endpoint activity       |
| tcpdump    | Network traffic         |
| Nmap       | Service discovery       |

### Contextual Investigation

A detection alone does not prove compromise.

For example, the SSH detection required additional investigation to determine:

* Whether the account existed
* Whether authentication succeeded
* Whether a session was established
* Whether additional suspicious activity occurred

---

## Detection Limitations

The current lab uses relatively simple detection logic.

Potential limitations include:

* Thresholds are static
* No centralized SIEM correlation
* No automated alerting
* Limited historical baselining
* Limited network protocol analysis
* No Windows endpoint telemetry

These limitations provide opportunities for future improvements.

---

## Future Detection Improvements

Planned enhancements include:

* Sigma detection rules
* Centralized SIEM ingestion
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

