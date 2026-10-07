# SSH Brute-Force Detection

## Detection Objective

Detect repeated failed or invalid SSH authentication attempts originating from the same source IP.

The detection was designed to identify potential brute-force activity while providing enough context for a SOC analyst to investigate the event.

---

## Data Source

**Log Source:**

```
/var/log/auth.log
```

**Service:**

```
OpenSSH
```

**Protocol:**

```
TCP/22
```

---

## Detection Logic

The lab uses a simple threshold-based detection:

```
5 or more failed/invalid SSH authentication attempts
from the same source IP
→ Potential SSH brute-force activity
```

This threshold was selected for the lab to demonstrate how repeated authentication failures can be converted into a security alert.

---

## Relevant Events

The following authentication events were monitored:

* `Failed password`
* `Invalid user`
* PAM authentication failures
* SSH connection failures

These events provide evidence of repeated authentication attempts against the endpoint.

---

## Detection Results

During the simulated attack:

| Detection Field           | Result           |
| ------------------------- | ---------------- |
| Source IP                 | `192.168.56.101` |
| Destination IP            | `192.168.56.103` |
| Service                   | SSH              |
| Destination Port          | `22`             |
| Target Account            | `fakeuser`       |
| Failed/Invalid Attempts   | `12`             |
| Detection Threshold       | `5`              |
| Alert Triggered           | Yes              |
| Successful Authentication | Not identified   |

The threshold was exceeded by **7 attempts**.

---

## Investigation Workflow

When the threshold is exceeded, the analyst should investigate:

### 1. Identify the Source

Determine the IP address generating the authentication failures.

```
192.168.56.101
```

### 2. Identify the Target

Determine which host and service are being targeted.

```
192.168.56.103
SSH / TCP 22
```

### 3. Identify the Account

Determine which username is being targeted.

```
fakeuser
```

### 4. Validate the Account

Check whether the targeted account exists on the endpoint.

The investigation confirmed that `fakeuser` was not a valid local account.

### 5. Check for Successful Authentication

Review authentication logs for successful login events such as:

```
Accepted password
Accepted publickey
session opened
```

No successful authentication was identified during this simulation.

---

## Alert Severity

**Severity: High**

The threshold was exceeded and the activity represented repeated authentication attempts against an exposed SSH service.

However, severity should be adjusted in a production SOC based on additional context such as:

* Whether the targeted account exists
* Whether authentication succeeded
* Source reputation
* Number and frequency of attempts
* Internet exposure
* Privilege level of the targeted account
* Other suspicious activity from the same source

---

## False-Positive Considerations

Repeated failed SSH authentication does not always indicate malicious activity.

Possible legitimate causes include:

* User entering an incorrect password
* Misconfigured automation
* Expired credentials
* Incorrect SSH configuration
* Monitoring or backup systems using outdated credentials

Detection thresholds should therefore be tuned using environment-specific baselines.

---

## MITRE ATT&CK

**T1110 — Brute Force**

The simulated behavior involved repeated authentication attempts against an SSH service.

---

## Recommended Response

If this alert occurred in a production environment, an analyst could:

1. Validate the source and target
2. Check whether authentication succeeded
3. Determine whether the account exists
4. Review related authentication activity
5. Investigate the source host
6. Apply blocking or rate-limiting controls if appropriate
7. Harden SSH authentication
8. Continue monitoring for related activity

---

## Detection Outcome

**Detection Status: Successful**

The detection successfully identified the simulated SSH brute-force activity after the configured threshold was exceeded.

**Observed:** 12 failed/invalid attempts
**Threshold:** 5 attempts
**Successful authentication:** None identified

Related attack simulation:

[`attacks/ssh-brute-force.md`](../attacks/ssh-brute-force.md)

Related investigation:

[`investigations/incident-001.md`](../investigations/incident-001.md)

