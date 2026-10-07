# Incident 002 — Local Account Discovery Investigation

## Incident Summary

Local account discovery activity was simulated on the Ubuntu endpoint by accessing system files containing account and group information.

Auditd file-watch rules generated telemetry for the activity, allowing the event to be detected and investigated.

---

## Incident Details

| Field          | Value                       |
| -------------- | --------------------------- |
| Incident ID    | INC-002                     |
| Activity       | Local Account Discovery     |
| Host           | Ubuntu Linux                |
| Host IP        | `192.168.56.103`            |
| Data Source    | auditd                      |
| Files Accessed | `/etc/passwd`, `/etc/group` |
| Status         | Detected and Investigated   |
| MITRE ATT&CK   | T1087.001                   |

---

## Activity Simulation

The following commands were used to access local account and group information:

```bash
cat /etc/passwd
cat /etc/group
```

These files contain information about local users and groups configured on the Linux system.

---

## Detection

Auditd file-watch rules were configured to monitor access to the account-related files.

Configured monitoring included:

```text id="6q3z9j"
/etc/passwd
/etc/group
```

The corresponding audit key was:

```text id="x9k2lm"
account_discovery
```

The generated audit events provided evidence that the monitored files had been accessed.

---

## Investigation Process

### 1. Identify the Activity

Auditd telemetry was searched using the detection key:

```bash id="k4w7px"
sudo ausearch -k account_discovery
```

The resulting events confirmed access to the monitored account and group files.

---

### 2. Identify the Target Files

The investigation identified access to:

```text id="3v1q9b"
/etc/passwd
/etc/group
```

These files are commonly queried during local account discovery.

---

### 3. Determine the Security Context

Account discovery can be a legitimate administrative activity.

Examples include:

* System administration
* Troubleshooting
* User management
* Software installation or configuration

However, attackers may also enumerate local accounts to identify potential targets or privileged users.

Therefore, the event requires contextual analysis rather than automatically being classified as malicious.

---

## MITRE ATT&CK Mapping

**T1087.001 — Account Discovery: Local Account**

The activity corresponds to local account discovery because the system's local account and group information was accessed.

---

## Impact Assessment

### Confirmed Activity

* `/etc/passwd` was accessed.
* `/etc/group` was accessed.
* Auditd successfully generated telemetry.
* The activity was detected and investigated.

### Confirmed Impact

No system modification or account compromise was identified from this activity.

The simulation was limited to reading account and group information.

---

## Response

Because this was a controlled lab simulation with no evidence of malicious modification or compromise:

* No containment action was required.
* The activity was documented.
* Audit telemetry was retained for investigation.
* The detection was validated.

In a production environment, an analyst should correlate this activity with additional telemetry such as:

* Process execution
* User identity
* Source process
* SSH authentication activity
* Privilege escalation events
* Other account-related activity

This context can help distinguish legitimate administration from malicious enumeration.

---

## Analyst Conclusion

The lab successfully detected local account discovery activity through auditd file monitoring.

The investigation confirmed access to:

```text id="1xj0cf"
/etc/passwd
/etc/group
```

The activity was classified as **detected and investigated** with no confirmed compromise.

This scenario demonstrates why detection alerts should be investigated in context rather than automatically treated as malicious.

---

## Related Documentation

* [Account Discovery Attack](../attacks/account-discovery.md)
* [Detection Matrix](../detections/detection-matrix.md)
* [MITRE ATT&CK Mapping](../mitre/attack-mapping.md)
* [Lessons Learned](../lessons-learned.md)

