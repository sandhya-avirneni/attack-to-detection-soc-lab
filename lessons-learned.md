# Lessons Learned — Attack-to-Detection SOC Lab

## Overview

This project provided hands-on experience with adversary emulation, Linux security monitoring, detection engineering, and incident investigation.

The primary objective was to understand how attacker activity produces security telemetry and how a SOC analyst can use that evidence to identify, investigate, and document suspicious behavior.

## 1. Authentication Failures Require Context

During the SSH brute-force simulation, 12 failed or invalid authentication attempts were observed from the Kali Linux attacker (`192.168.56.101`) against the Ubuntu endpoint (`192.168.56.103`).

**Key lessons:**
- Repeated authentication failures can indicate brute-force activity.
- Source IP addresses, targeted usernames, timestamps, and authentication outcomes are important investigation details.
- A detection threshold of five failed attempts was exceeded during the simulation.
- Failed authentication attempts do not automatically mean an attacker gained access.

**Takeaway:** Detection should be followed by investigation to determine whether suspicious activity resulted in successful authentication.

## 2. Endpoint Telemetry Improves Visibility

Linux `auditd` was configured to monitor command execution and access to account-related files.

Monitoring `/etc/passwd` and `/etc/group` provided visibility into local account discovery activity.

**Key lessons:**
- Audit rules must be configured and loaded correctly.
- File-access events can provide useful investigation evidence.
- Security events require interpretation because legitimate administrative activity may generate similar telemetry.

**Takeaway:** Collecting logs is only the first step; analysts must understand what the events represent.

## 3. Detection Engineering Requires False-Positive Testing

Benign administrative commands were executed to validate command-execution telemetry.

Examples included:
- `whoami`
- `uname -a`
- `cat /etc/os-release`

These commands generated telemetry but were not classified as malicious.

**Key lessons:**
- Not every recorded event represents an attack.
- Detection rules require context and tuning.
- Analysts should distinguish suspicious behavior from normal system administration.

**Takeaway:** Effective detection engineering balances security visibility with false-positive reduction.

## 4. MITRE ATT&CK Supports Investigation

The lab activities were mapped to relevant MITRE ATT&CK techniques.

| Activity | Technique |
|---|---|
| Network reconnaissance | T1046 — Network Service Discovery |
| SSH brute-force simulation | T1110 — Brute Force |
| Local account discovery | T1087.001 — Account Discovery: Local Account |

Command-execution monitoring also provided telemetry relevant to investigating command-interpreter activity, although benign validation commands alone did not establish malicious T1059 behavior.

**Takeaway:** MITRE ATT&CK provides a consistent framework for describing observed techniques and identifying detection coverage.

## 5. Evidence-Based Investigation Is Essential

The SSH and account discovery scenarios were documented using authentication logs, Linux audit events, and supporting screenshots.

**Key lessons:**
- Investigation conclusions should be supported by observable evidence.
- Analysts should document both what was observed and what could not be confirmed.
- A repeatable investigation process improves consistency.

**Takeaway:** Strong incident reports clearly distinguish evidence, analysis, and conclusions.

## 6. Limitations Identified

This lab demonstrated manual detection validation and incident investigation rather than a fully automated SOC environment.

Current limitations include:
- No centralized SIEM platform.
- No automated alert generation.
- Limited endpoint coverage.
- Detection logic validated through manual analysis.
- A small number of controlled attack scenarios.

Recognizing these limitations helps define realistic future improvements.

## 7. Future Improvements

Planned improvements include:

1. Implement centralized log collection using a SIEM.
2. Create automated detection rules for repeated SSH failures.
3. Develop Sigma-style detection rules.
4. Expand network monitoring with Zeek or Suricata.
5. Add Windows endpoint telemetry using Sysmon.
6. Develop additional attack simulations and incident investigations.

## Final Reflection

The most valuable lesson from this project was understanding the relationship between attacker behavior, security telemetry, detection logic, and incident investigation.

Rather than treating every security event as malicious, the lab emphasized validating suspicious activity, reviewing supporting evidence, and documenting accurate findings.

This experience strengthened foundational skills relevant to entry-level SOC Analyst and cybersecurity operations roles.
