# Splunk SOC Lab — Threat Intelligence, IOC/IOA Detection & Threat Hunting

> Built a Splunk SIEM lab to analyze Linux security logs, investigate IOCs and IOAs, detect suspicious SSH activity, and examine network reconnaissance using Nmap and Wireshark.

**Hands-on SOC investigation | Splunk Enterprise · Kali Linux · Ubuntu · Wireshark · Nmap · VMware Workstation**

## Overview

Built an isolated three-VM security operations lab, ingested Linux authentication, system and Apache logs into Splunk, and investigated simulated reconnaissance and SSH access activity. Correlated security events with network packet captures and reviewed supplied threat indicators using external reputation sources.

**Outcome:** Identified one unsuccessful SSH password attempt and a controlled Nmap scan. **None of the five supplied threat indicators appeared in the indexed log snapshots.** The report explicitly distinguishes observed local activity from external reputation scores.

**[View the complete 26-page Splunk investigation report with screenshots](Report.pdf)**

## Lab architecture

```text
                   VMware VMnet1 — Host-only
                         192.168.50.0/24
                                |
               +----------------+----------------+
               |                |                |
          Kali Linux       Ubuntu Victim     Splunk Server
          .50.10           .50.20            .50.30
          Nmap / SSH       Apache / SSH      Splunk Enterprise
          traffic source   auth/syslog       log ingestion/search
               |                |                |
           Wireshark     /var/log/...       index=main
```

| System | Address | Role |
|---|---|---|
| Kali Linux | `192.168.50.10` | Simulated scanning and denied SSH login |
| Ubuntu Victim | `192.168.50.20` | Linux and Apache log source |
| Ubuntu Splunk Server | `192.168.50.30` | Splunk Enterprise SIEM; log analysis |

## Investigation process

1. Configured the VMs on a VMware host-only network and validated connectivity.
2. Installed Splunk Enterprise and imported snapshots of Ubuntu `auth.log`, `syslog` and Apache `access.log`.
3. Searched indexed events for failed SSH authentication and attempted usernames using Splunk SPL.
4. Conducted a controlled Nmap scan and examined the related TCP SYN traffic using Wireshark.
5. Investigated **three supplied IP addresses and two supplied domains** with Splunk searches and reputation lookups.
6. Used IOC/IOA concepts, MITRE ATT&CK, the Pyramid of Pain and STRIDE to document findings and limitations.

## Verified results

| Metric or result | Observation |
|---|---|
| Initial indexed events | **16,809** across auth, syslog and access logs |
| Later indexed dataset | **16,894** events after adding an authentication-log snapshot |
| Wireshark capture | **2,041** packets |
| Supplied IOC candidates | **5** (3 IPs + 2 domains) |
| Local matches for supplied IOCs | **0** in searched indexed snapshots |
| Confirmed SSH activity | One failed password attempt from Kali using `invaliduser` |
| Network reconnaissance | Nmap scan supported by output and SYN packet evidence |
| Confirmed compromise | **None** |

### Example SPL investigations

```spl
index=main "Failed password" "sshd-session"
```

```spl
index=main | top dest_ip
```

The destination-IP search did not produce meaningful results because `dest_ip` was not extracted from the available log sources—not because destination traffic was proven absent. The report includes additional field extraction and IOC hunting examples.

## Detection and analysis

- **ATT&CK T1046 — Network Service Discovery:** Supported by the port scan and packet capture.
- **SSH access attempt:** Verified in authentication logs; a single denied login is **not** evidence of a brute-force campaign.
- **Threat intelligence:** One supplied indicator had strong external reputation warnings, but **no observed local match**. Reputation was not treated as proof of compromise.
- **Data limitations:** Collected log snapshots were indexed; continuous forwarding was **not demonstrated**. DNS resolution for one tested domain failed in Kali.

## Report and screenshot evidence

The **[26-page PDF report](Report.pdf)** includes the investigation summary, Splunk setup, indexed source counts, searches, IOC/IOA analyses, risk assessment, recommendations, the **nine required assignment screenshots**, additional Wireshark evidence and threat-intelligence lookup images.

The original `.pcapng` capture is not embedded in this PDF and would need to be provided separately if requested by an instructor.

## Skills demonstrated

`SIEM` · `Splunk SPL` · `SOC Investigation` · `Threat Hunting` · `Log Ingestion` · `Network Reconnaissance Detection` · `IOC/IOA Analysis` · `Wireshark` · `Threat Intelligence` · `MITRE ATT&CK` · `Technical Reporting`

---
*All attacker actions were controlled lab simulations. The conclusions are limited to collected evidence.*