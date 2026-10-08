# Attack-to-Detection SOC Lab

### Adversary Emulation, Detection Engineering & Incident Response

A controlled cybersecurity home lab designed to simulate attacker activity, collect endpoint and network telemetry, develop detections, investigate security events, map activity to MITRE ATT&CK, and document incident response.

---

## 🎯 Project Objective

The goal of this project is to demonstrate a practical **SOC analyst workflow** from attack simulation through detection and investigation.

Instead of focusing only on offensive activity, this lab follows the complete workflow:

**Attack → Telemetry → Detection → Investigation → MITRE ATT&CK → Response → Lessons Learned**

---

## 🏗️ Lab Architecture
![SOC Lab Architecture](architecture/architecture-diagram.png)


| Component    | Role                           | IP Address       |
| ------------ | ------------------------------ | ---------------- |
| Kali Linux   | Attacker / Adversary Emulation | `192.168.56.101` |
| Ubuntu Linux | Monitored Endpoint             | `192.168.56.103` |
| SSH          | Remote Access / Attack Surface | TCP `22`         |
| auditd       | Endpoint Telemetry             | Ubuntu           |
| auth.log     | Authentication Telemetry       | Ubuntu           |
| tcpdump      | Network Telemetry              | Ubuntu           |

### Data Flow


┌──────────────────┐
│   Kali Linux     │
│    Attacker      │
│ 192.168.56.101   │
└────────┬─────────┘
         │
         │ Simulated Attacks
         ▼
┌─────────────────────────┐
│     Ubuntu Endpoint     │
│      192.168.56.103     │
│                         │
│  SSH / TCP 22           │
│  auditd                 │
│  /var/log/auth.log      │
└───────────┬─────────────┘
            │
            │ Telemetry
            ▼
┌─────────────────────────┐
│   Detection & Analysis  │
│                         │
│  Detection Rules        │
│  Alert Investigation    │
│  MITRE ATT&CK Mapping   │
│  Incident Response      │
└─────────────────────────┘
```

Detailed architecture documentation is available in [`architecture/`](./architecture/).

---

## 🔴 Attack Scenarios

### 1. Network Reconnaissance

Performed service discovery against the Ubuntu endpoint using Nmap.

**Objective:**
Identify exposed services and understand the attack surface.

**Tool:**

* Nmap

---

### 2. SSH Brute-Force Simulation

Simulated repeated SSH authentication attempts against the Ubuntu endpoint using a non-existent account.

**Source:** `192.168.56.101`
**Target:** `192.168.56.103`
**Account:** `fakeuser`
**Observed attempts:** `12`

A detection threshold of **5 failed/invalid authentication attempts from a single source** was used to identify potential brute-force activity.

**Result:**

* 12 failed/invalid authentication events detected
* No successful authentication identified
* Activity investigated and documented

See [`attacks/ssh-brute-force.md`](./attacks/ssh-brute-force.md).

---

### 3. Local Account Discovery

Simulated local account discovery by accessing:

```
/etc/passwd
/etc/group
```

Auditd file-watch rules were used to generate telemetry for this activity.

See [`attacks/account-discovery.md`](./attacks/account-discovery.md).

---

## 🛡️ Detection Engineering

The lab uses multiple telemetry sources to identify and investigate activity.

### SSH Authentication Detection

Source:

```
/var/log/auth.log
```

Detection logic:

```
≥5 failed or invalid SSH authentication attempts
from the same source IP
→ Potential SSH brute-force activity
```

### Auditd Command Execution Monitoring

An `execve` audit rule was configured to capture command execution activity.

```
-a always,exit -F arch=b64 -S execve -F key=command_execution
```

### Account Discovery Monitoring

Auditd file watches were configured for:

```
/etc/passwd
/etc/group
/etc/shadow
```

These provide telemetry for account discovery and credential-related file access.

See [`detections/`](./detections/) for the detection documentation and matrix.

---

## 🔎 Incident Investigations

### Incident 001 — SSH Brute Force

**Severity:** High
**Source:** `192.168.56.101`
**Target:** `192.168.56.103`
**Service:** SSH
**Result:** Detected and investigated

Investigation confirmed multiple failed authentication attempts against a non-existent account.

No evidence of successful SSH authentication was identified.

See [`investigations/incident-001.md`](./investigations/incident-001.md).

---

### Incident 002 — Local Account Discovery

**Host:** Ubuntu endpoint
**Activity:** Access to `/etc/passwd` and `/etc/group`
**Telemetry:** auditd
**Result:** Detected and investigated

See [`investigations/incident-002.md`](./investigations/incident-002.md).

---

## 🧭 MITRE ATT&CK Mapping

The simulated activities were mapped to relevant MITRE ATT&CK techniques.

| Activity                    | MITRE ATT&CK                              |
| --------------------------- | ----------------------------------------- |
| SSH brute-force simulation  | T1110 — Brute Force                       |
| Local account discovery     | T1087.001 — Local Account Discovery       |
| Command execution telemetry | T1059 — Command and Scripting Interpreter |

The MITRE mapping is documented in [`mitre/attack-mapping.md`](./mitre/attack-mapping.md).

---

## 🧪 False-Positive Testing

Detection quality was tested using benign administrative commands such as:

```
whoami
uname -a
cat /etc/os-release
```

These activities generated telemetry but were intentionally classified as **benign**.

This demonstrates the importance of distinguishing normal administrative behavior from potentially malicious activity and tuning detections accordingly.

See [`detections/false-positive-testing.md`](./detections/false-positive-testing.md).

---

## 🚨 Incident Response

The investigation workflow included:

1. Identify the alert
2. Validate the source and target
3. Review authentication and audit logs
4. Determine whether authentication succeeded
5. Identify the affected account and activity
6. Map the behavior to MITRE ATT&CK
7. Document findings
8. Determine appropriate response actions
9. Validate whether further monitoring was required

For the SSH simulation, investigation confirmed that the targeted account did not exist and that no successful authentication occurred.

---

## 📊 Detection Coverage

| Detection                 | Telemetry       | Status                |
| ------------------------- | --------------- | --------------------- |
| SSH brute force           | `auth.log`      | ✅ Detected            |
| Local account discovery   | auditd          | ✅ Detected            |
| Command execution         | auditd `execve` | ✅ Telemetry validated |
| False-positive validation | auditd          | ✅ Tested              |

---

## 🧰 Tools & Technologies

**Operating Systems**

* Kali Linux
* Ubuntu Linux

**Security Tools**

* Nmap
* tcpdump
* Wireshark
* auditd
* OpenSSH

**Frameworks**

* MITRE ATT&CK

**Security Concepts**

* Network reconnaissance
* Authentication monitoring
* Brute-force detection
* Endpoint telemetry
* Detection engineering
* Incident investigation
* False-positive analysis
* Incident response

---

## 📁 Repository Structure

```
attack-to-detection-soc-lab/
│
├── architecture/
│   └── README.md
│
├── attacks/
│   ├── ssh-brute-force.md
│   └── account-discovery.md
│
├── detections/
│   ├── ssh-bruteforce.md
│   ├── detection-matrix.md
│   └── false-positive-testing.md
│
├── investigations/
│   ├── incident-001.md
│   └── incident-002.md
│
├── mitre/
│   └── attack-mapping.md
│
├── evidence/
│   └── README.md
│
└── lessons-learned.md
```

---

## 📚 Key Lessons

This project reinforced several practical SOC concepts:

* Endpoint and network telemetry provide different perspectives of the same event.
* Authentication failures should be correlated by source, target, account, and time.
* A detection should distinguish malicious behavior from legitimate administrative activity.
* Investigation must verify whether an attack actually succeeded.
* Detection thresholds require tuning to reduce false positives.
* Security events should be documented in a repeatable investigation workflow.

More details are available in [`lessons-learned.md`](./lessons-learned.md).

---

## 🚀 Future Improvements

Planned enhancements include:

* Add centralized log management / SIEM functionality
* Add Sigma-style detection rules
* Add Zeek or Suricata network monitoring
* Expand MITRE ATT&CK coverage
* Add automated alert generation
* Add Windows telemetry with Sysmon
* Develop additional attack-and-detection scenarios
* Introduce detection-as-code practices

---

## ⚠️ Disclaimer

This project was conducted in an isolated home lab using systems controlled by the author.

All attack simulations were performed for educational and defensive security purposes.
