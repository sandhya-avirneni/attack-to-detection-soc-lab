# Incident 001 — SSH Brute Force Investigation

## Incident Summary

A simulated SSH brute-force attack was detected against the Ubuntu endpoint after repeated failed authentication attempts from the Kali Linux attacker system.

The activity exceeded the manually defined detection threshold and was investigated to determine whether the attack resulted in successful authentication.

---

## Incident Details

| Field                     | Value                     |
| ------------------------- | ------------------------- |
| Incident ID               | INC-001                   |
| Severity                  | High                      |
| Status                    | Detected and Investigated |
| Source IP                 | `192.168.56.101`          |
| Destination IP            | `192.168.56.103`          |
| Service                   | SSH                       |
| Port                      | `22`                      |
| Target Account            | `fakeuser`                |
| Failed/Invalid Attempts   | `12`                      |
| Detection Threshold       | `5`                       |
| Successful Authentication | None identified           |

---

## Attack Timeline

| Time / Stage          | Activity                                                             |
| --------------------- | -------------------------------------------------------------------- |
| Initial               | Kali initiated SSH connection to Ubuntu                              |
| Authentication        | Invalid credentials were repeatedly submitted                        |
| Detection             | Multiple failed/invalid authentication events appeared in `auth.log` |
| Threshold             | 12 attempts exceeded the 5-attempt detection threshold               |
| Investigation         | Source, target, account, and authentication outcome were reviewed    |
| Validation            | `fakeuser` was confirmed as a non-existent account                   |
| Authentication Review | No successful SSH authentication was identified                      |
| Response              | Activity documented and monitoring continued                         |

---

## Detection

Repeated failed and invalid SSH authentication attempts were manually identified through analysis of Ubuntu authentication logs.

The following analytical threshold was used:

```text
≥5 failed/invalid SSH authentication attempts
from the same source IP
```

**Observed Activity:** 12 documented failed or invalid authentication events.

The documented activity exceeded the analytical threshold and was classified as a potential SSH brute-force attempt.

**Detection Method:** Manual authentication-log analysis.

**Automation Status:** No automated SIEM alert or scheduled detection rule was implemented.

---

## Evidence

Primary evidence source:

```text id="r8mtqk"
/var/log/auth.log
```

The log contained events including:

* `Invalid user fakeuser`
* `Failed password`
* PAM authentication failures
* SSH connection closure

These events confirmed repeated unsuccessful authentication attempts.

---

## Investigation Process

### 1. Source Identification

The authentication events originated from:

```text id="9t6l9f"
192.168.56.101
```

This address corresponded to the Kali Linux attacker VM.

---

### 2. Target Identification

The targeted Ubuntu endpoint was:

```text id="qj1e5q"
192.168.56.103
```

The targeted service was SSH on TCP port 22.

---

### 3. Account Validation

The attacker attempted to authenticate as:

```text id="0x0t4h"
fakeuser
```

The account was checked against the local system account database.

The investigation confirmed that `fakeuser` did not exist.

---

### 4. Authentication Outcome

Authentication logs were reviewed for successful login indicators, including:

```text id="6gl1h4"
Accepted password
Accepted publickey
session opened
```

No successful authentication event was identified.

Therefore:

**No successful SSH authentication was identified in the reviewed evidence..**

---

## MITRE ATT&CK Mapping

**T1110 — Brute Force**

The activity involved repeated authentication attempts against an SSH service.

---

## Impact Assessment

### Confirmed Impact

* Repeated authentication attempts were generated.
* SSH authentication telemetry was successfully collected.
* No successful authentication was identified.
* No evidence of account compromise was observed.

### Risk

If the targeted account had existed and valid credentials had been discovered, the activity could have resulted in unauthorized access.

---

## Response Actions

Because the targeted account did not exist and no successful authentication was identified:

* No account reset was required.
* No credential compromise was identified.
* No host isolation was required.
* The activity was documented.
* Continued monitoring was recommended.

In a production environment, additional controls could include:

* Source IP blocking or rate limiting
* SSH hardening
* MFA where supported
* Key-based authentication
* Review of exposed SSH services
* Monitoring for repeated attacks from related sources

---

## Analyst Conclusion

The manually investigated authentication activity was consistent with a simulated SSH brute-force pattern.

The investigation established:

**12 failed/invalid attempts → threshold exceeded → account did not exist → no successful authentication → no evidence of compromise.**

The incident was therefore classified as **detected and investigated without confirmed compromise**.

---

## Related Documentation

* [SSH Brute-Force Attack](../attacks/ssh-brute-force.md)
* [SSH Brute-Force Detection](../detections/ssh-bruteforce.md)
* [Detection Matrix](../detections/detection-matrix.md)
* [MITRE ATT&CK Mapping](../mitre/attack-mapping.md)

