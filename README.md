# Wazuh SIEM Project — DVWA Threat Detection & SOC Monitoring

A hands-on SIEM/SOC project built from scratch using **Wazuh 4.14.8**, monitoring a deliberately vulnerable web application (DVWA) and turning raw attack traffic into detected, MITRE ATT&CK-mapped, triaged security alerts — the same workflow a SOC Analyst follows day to day:

**Log → Event → Alert → Triage → Response → Report**

This project extends my [DVWA Web Application Security Assessment](../dvwa-web-application-security-assessment) capstone. Instead of only exploiting DVWA's vulnerabilities, this project focuses on detecting and monitoring those same attacks in real time using a complete SIEM stack.

## What this project demonstrates

- Installing and configuring a complete Wazuh SIEM stack (Manager, Indexer, Dashboard, Agent) from a blank Ubuntu Server VM
- Onboarding multiple log sources (Apache web access logs, Linux authentication logs, and File Integrity Monitoring)
- Writing **custom detection rules** for SQL Injection, Blind SQL Injection, Reflected XSS, Stored XSS, CSRF, and brute-force login attempts
- Mapping custom detection rules to the **MITRE ATT&CK framework** using Wazuh's native `<mitre>` tagging
- Validating detections using manually generated traffic, `curl`, and **Burp Suite** where applicable
- Configuring **Active Response** (`firewall-drop`) to automatically block a source IP after repeated brute-force login attempts, with block/unblock evidence
- Building SOC-style dashboards for alert investigation and threat hunting
- Performing **alert triage** — recording rule ID, severity, MITRE technique, source information, analyst action, and incident status
- Writing a full **incident report** following the NIST incident response lifecycle (Preparation → Detection & Analysis → Containment → Eradication & Recovery → Lessons Learned)

## Lab environment

| Component | Details |
|---|---|
| Hypervisor | VMware Workstation |
| Network | Isolated NAT network |
| Wazuh Manager / Indexer / Dashboard | Ubuntu Server — `192.168.20.132` |
| Agent (monitored endpoint) | Kali Linux running Apache, MariaDB, DVWA — `192.168.20.128` |
| SIEM | Wazuh 4.14.8 |
| Target application | DVWA (Damn Vulnerable Web Application) |
| Attack tooling | Burp Suite (Proxy / Intruder / Repeater), curl |

> All IP addresses shown throughout this repository (192.168.x.x, 127.0.0.1) are private/loopback addresses used within the lab environment.

## Custom detection rules

| Rule ID | Detects | MITRE Technique | Level |
|---|---|---|---|
| 100010 | SQL Injection on DVWA | T1190 — Exploit Public-Facing Application | 10 |
| 100011 | Reflected XSS on DVWA | T1189 — Drive-by Compromise | 10 |
| 100012 | Blind SQL Injection on DVWA | T1190 — Exploit Public-Facing Application | 10 |
| 100013 | Brute-force login attempt (building block) | — | 1 |
| 100014 | Brute-force login — 5+ attempts in 60s | T1110 — Brute Force | 10 |
| 100016 | Stored XSS on DVWA | T1190 — Exploit Public-Facing Application | 10 |
| 100017 | CSRF attack on DVWA | T1189 — Drive-by Compromise | 10 |

Active Response (`firewall-drop`) is bound to **Rule 100014**. Repeated brute-force attempts from the same source IP trigger an automatic firewall block on the monitored agent.

## Repository structure

```text
Wazuh-SOC-Threat-Detection-Lab/
│
├── README.md
│
├── 01_Setup_Installation/        # Wazuh stack + DVWA environment setup
├── 02_Log_Collection/            # Apache/DVWA access-log collection
├── 03_Authentication_and_FIM/    # Linux authentication logs + FIM
├── 04_Brute_Force/               # Brute-force detection and testing
├── 05_Active_Response/           # firewall-drop configuration and evidence
├── 06_MITRE_and_Triage/          # MITRE ATT&CK + alert triage
├── 07_SQL_Injection/             # SQL Injection + Blind SQL Injection
├── 08_XSS/                       # Reflected + Stored XSS
├── 09_CSRF/                      # CSRF detection and MITRE mapping
│
├── Wazuh_SOC_Incident_Report.pdf
├── Wazuh_Threat_Hunting_Report.pdf
└── wazuh-module-overview-general.pdf
