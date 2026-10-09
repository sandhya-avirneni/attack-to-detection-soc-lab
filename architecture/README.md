# SOC Lab Architecture

## Overview

The Attack-to-Detection SOC Lab consists of a controlled VirtualBox environment used for adversary emulation, Linux security monitoring, and incident investigation.

A separate Splunk Cloud exercise was used to investigate manually uploaded SSH-related security events using Search Processing Language (SPL).

The architecture was designed to demonstrate how attack activity generates security telemetry and how SOC analysts can investigate that activity.

## Architecture Diagram

![SOC Lab Architecture](architecture-diagram.png)

## 1. Lab Components

| Component | Purpose | Configuration |
|---|---|---|
| Windows 11 | Virtualization host | Physical computer |
| Oracle VirtualBox | Virtual machine management | Local virtualization |
| Kali Linux | Attacker machine | 192.168.56.101 |
| Ubuntu Linux | Monitored endpoint | 192.168.56.103 |
| OpenSSH | Remote access service | TCP port 22 |
| Linux auditd | Endpoint activity monitoring | Ubuntu |
| /var/log/auth.log | SSH authentication logs | Ubuntu |
| tcpdump | Network traffic capture | Lab environment |
| Splunk Cloud | Separate SIEM log analysis | Manual log upload |

## 2. Network Configuration

The Kali Linux and Ubuntu virtual machines communicate through a VirtualBox host-only network.

| System | IP Address | Role |
|---|---|---|
| Kali Linux | 192.168.56.101 | Attacker |
| Ubuntu Linux | 192.168.56.103 | Monitored endpoint |

The host-only network provides communication between the virtual machines without directly exposing their simulated attack traffic to external networks.

## 3. Attack Simulation Workflow

The Kali Linux virtual machine was used to simulate controlled security activity against Ubuntu.

### Network Reconnaissance

Nmap was used to identify available services on the monitored endpoint.

```bash
nmap -sV 192.168.56.103
```

The scan identified SSH on TCP port 22.

### SSH Authentication Testing

Repeated SSH authentication attempts were simulated using the non-existent username `fakeuser`.

The Ubuntu endpoint recorded failed and invalid authentication events in `/var/log/auth.log`.

### Local Account Discovery

Access to `/etc/passwd` and `/etc/group` was monitored using Linux auditd.

These activities generated endpoint telemetry for investigation.

## 4. Security Telemetry

### SSH Authentication Logs

**Source:** `/var/log/auth.log`

Used to investigate:

- Failed SSH login attempts
- Invalid usernames
- Source IP addresses
- Authentication outcomes

### Linux auditd

Used to monitor:

- Command execution through `execve`
- Access to `/etc/passwd`
- Access to `/etc/group`
- Access to `/etc/shadow`

### Network Traffic

`tcpdump` was used for network traffic capture during the lab exercises.

## 5. Splunk Cloud Integration

Splunk Cloud was used as a separate SIEM log-analysis exercise.

### Implementation

1. SSH-related log data was manually uploaded into Splunk Cloud.
2. Indexed events were searched using SPL.
3. Raw event data and timestamps were reviewed.
4. SSH-related events containing the attacker IP address were identified.

### SPL Query

```spl
index=main "libssh" | table _time, _raw
```

The search returned two SSH-related events containing `192.168.56.101`.

**Important:** Automated log forwarding from Ubuntu to Splunk Cloud was not configured. Splunk was used for manual ingestion and investigation rather than automated alert generation.

## 6. Detection and Investigation Workflow

The lab followed this process:

1. Simulate controlled attacker activity.
2. Generate authentication, endpoint, and network telemetry.
3. Collect and review security events.
4. Evaluate detection logic.
5. Investigate suspicious activity.
6. Map observed behavior to MITRE ATT&CK.
7. Document findings and response considerations.

## 7. Architecture Limitations

The environment was designed for learning and detection validation rather than production security monitoring.

Current limitations include:

- Manual log analysis
- Manual Splunk Cloud ingestion
- No automated SIEM alert generation
- Limited monitored endpoints
- No automated containment or response orchestration

## 8. Future Improvements

- Implement automated log forwarding to Splunk.
- Create scheduled SPL detection searches.
- Configure SIEM alerts.
- Add Windows endpoint monitoring using Sysmon.
- Expand network telemetry using Zeek or Suricata.
- Introduce additional attack simulations and detection rules.

---

[Return to Main Project README](../README.md)
