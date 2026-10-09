# SOC Lab — Evidence Collection

This directory contains screenshots collected during controlled security testing in the Attack-to-Detection SOC Lab.

The evidence supports network reconnaissance, SSH authentication monitoring, Linux account discovery investigation, and Splunk Cloud SIEM log analysis.

---

## 1. Network Reconnaissance — Nmap

![Nmap Reconnaissance](01-nmap-reconnaissance.png)

**Activity:** Network service enumeration from Kali Linux (`192.168.56.101`) against Ubuntu (`192.168.56.103`).

**Tool:** Nmap

**Objective:** Identify exposed network services, including SSH on TCP port 22.

**MITRE ATT&CK:** T1046 — Network Service Discovery

---

## 2. SSH Authentication Failure Analysis

![SSH Authentication Failures](02-ssh-authentication-failures.png)

**Activity:** Analysis of failed SSH authentication attempts against the Ubuntu endpoint.

**Log Source:** `/var/log/auth.log`

**Observed Indicators:**
- Invalid username: `fakeuser`
- Source IP: `192.168.56.101`
- Repeated failed authentication events

**Detection Logic:** Five or more failed or invalid authentication attempts from the same source IP within the reviewed dataset.

**MITRE ATT&CK:** T1110 — Brute Force

**Investigation Finding:** The documented simulation generated 12 failed or invalid authentication events. The detection threshold was evaluated manually; no automated SIEM alert was generated.

---

## 3. Linux Account Discovery — auditd

### Evidence 1

![Account Discovery Audit Evidence 1](03-account-discovery-auditd-1.png)

### Evidence 2

![Account Discovery Audit Evidence 2](03-account-discovery-auditd-2.png)

**Activity:** Monitoring access to local Linux account information.

**Telemetry Source:** Linux Audit Framework (`auditd`)

**Monitored Files:**
- `/etc/passwd`
- `/etc/group`

**Detection Key:** `account_discovery`

**MITRE ATT&CK:** T1087.001 — Account Discovery: Local Account

**Investigation Finding:** Audit events recorded access to monitored account-information files, providing evidence for endpoint investigation. Individual events require process-level review to distinguish simulated discovery activity from legitimate system access.

---

## 4. Splunk Cloud — SSH Event Investigation

![Splunk Cloud SSH Analysis](04-splunk-cloud-ssh-analysis.png)

**Activity:** Investigation of SSH-related security telemetry using Splunk Cloud.

**Platform:** Splunk Cloud — Search & Reporting

**Log Ingestion Method:** Manual upload

**SPL Query:**

```spl
index=main "libssh" | table _time, _raw
```

**Observed Indicators:**
- Source IP address: `192.168.56.101`
- SSH protocol identification strings
- Event timestamps
- Raw connection-event data

**Investigation Finding:** The SPL query returned two SSH-related events. The exercise demonstrated manual SIEM log ingestion, event searching, and security telemetry analysis.

**Limitation:** Automated log forwarding, scheduled detection searches, and SIEM alert generation were not implemented.

---

## Evidence Handling

All attack simulations were performed in an authorized VirtualBox home lab.

Screenshots demonstrate the collection and interpretation of security telemetry.

Detection findings were validated through manual log analysis. Splunk Cloud was used for manually uploaded log investigation rather than automated alerting.

---

[Return to Main Project README](../README.md)
