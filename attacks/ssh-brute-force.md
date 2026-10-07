# SSH Brute-Force Simulation

## Objective

Simulate repeated SSH authentication attempts against an Ubuntu endpoint and validate whether the activity can be detected through authentication logs.

This scenario demonstrates a basic SOC workflow:

**Attack Simulation → Log Collection → Detection → Investigation → Response**

---

## Lab Environment

| System       | Role            | IP Address       |
| ------------ | --------------- | ---------------- |
| Kali Linux   | Attacker        | `192.168.56.101` |
| Ubuntu Linux | Target Endpoint | `192.168.56.103` |
| SSH          | Target Service  | TCP `22`         |

---

## Attack Simulation

From the Kali Linux attacker machine, an SSH connection was initiated against the Ubuntu endpoint using a non-existent account:

```bash
ssh fakeuser@192.168.56.103
```

Multiple incorrect authentication attempts were intentionally entered.

The simulation was stopped after generating sufficient failed authentication events.

---

## Observed Activity

Ubuntu recorded authentication events in:

```
/var/log/auth.log
```

Relevant events included:

* Invalid user attempts
* Failed password attempts
* PAM authentication failures
* SSH connection closures

The targeted account, `fakeuser`, did not exist on the Ubuntu system.

---

## Detection Logic

A simple threshold-based detection was used:

```
5 or more failed/invalid SSH authentication attempts
from the same source IP
→ Potential SSH brute-force activity
```

The investigation identified:

| Field                     | Value            |
| ------------------------- | ---------------- |
| Source IP                 | `192.168.56.101` |
| Target IP                 | `192.168.56.103` |
| Target Service            | SSH / TCP 22     |
| Target Account            | `fakeuser`       |
| Failed/Invalid Attempts   | `12`             |
| Detection Threshold       | `5`              |
| Successful Authentication | None identified  |

---

## Investigation

Authentication logs were reviewed to determine:

1. Where the activity originated
2. Which account was targeted
3. How many failed attempts occurred
4. Whether the targeted account existed
5. Whether authentication was ultimately successful

The logs showed repeated authentication failures originating from the Kali system.

A review for successful authentication events found no evidence of:

* Successful password authentication
* Successful public-key authentication
* A successful SSH session

Therefore, the simulated attack did **not** result in a successful login.

---

## MITRE ATT&CK

**T1110 — Brute Force**

The activity represents repeated authentication attempts against an SSH service.

---

## Response

Because the targeted account did not exist and no successful authentication was identified, no account containment action was required.

The event was documented and monitoring was continued.

In a production environment, additional response actions could include:

* Blocking or rate-limiting the source
* Investigating the source host
* Reviewing other authentication attempts
* Checking for successful logins
* Evaluating account exposure
* Implementing SSH hardening or lockout controls

---

## Evidence

Primary evidence source:

```
/var/log/auth.log
```

Supporting telemetry:

```
auditd
tcpdump
```

Detailed investigation:

[`investigations/incident-001.md`](../investigations/incident-001.md)

Detection documentation:

[`detections/ssh-bruteforce.md`](../detections/ssh-bruteforce.md)

---

## Result

**Status: Detected and Investigated**

The lab successfully generated and identified repeated SSH authentication failures. The investigation confirmed **12 failed/invalid attempts** with **no evidence of successful authentication**.

