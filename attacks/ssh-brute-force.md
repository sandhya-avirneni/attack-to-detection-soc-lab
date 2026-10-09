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

A manually defined threshold was used to evaluate the authentication logs. Five or more failed or invalid SSH authentication attempts from the same source IP were treated as potential brute-force activity. No automated detection rule or alert was implemented.

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

No successful SSH authentication was identified in the reviewed evidence.

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

**Primary Evidence Source:**

```text
/var/log/auth.log
```

**Authentication Log Screenshot:**

![SSH Authentication Failures](../evidence/02-ssh-authentication-failures.png)

The screenshot documents failed or invalid SSH authentication activity observed during the controlled simulation.

**Related Lab Telemetry:**

Linux auditd and tcpdump were used for additional endpoint and network visibility in the broader SOC lab. They are not presented here as independent proof of the 12 SSH authentication attempts.

**Detailed Investigation:**

[Incident 001 — SSH Brute Force](../investigations/incident-001.md)

**Detection Documentation:**

[SSH Brute-Force Detection](../detections/ssh-bruteforce.md)
---

## Result

**Status: Manually Detected and Investigated**

The controlled simulation generated repeated failed or invalid SSH authentication events in Ubuntu authentication logs.

The investigation documented 12 failed or invalid events and evaluated them against a manually defined five-attempt threshold.

No successful SSH authentication was identified in the reviewed evidence.

No automated SIEM alert was generated.
