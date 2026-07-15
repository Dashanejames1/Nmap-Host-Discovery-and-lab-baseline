# Nmap-Host-Discovery-and-lab-baseline

**Author:** Dashane James  
**Lab Environment:** [VMware Workstation | Kali Linux | Metasploitable 2]  
**Purpose:** [To mirror the first phase of real-world penetration testing and develop practical hands-on fluency with the reconnaisance phase ]  
**Status:** 🟢 Active / 🟡 In Progress / 🔵 Completed

---

## 📋 Overview

[Write 2-3 sentences describing what this project is, what you did, and why. Example: "This repository documents a series of ______ assessments performed in an isolated VMware home lab. The goal was to build hands-on proficiency with ______ aligned with CySA+ exam objectives."] Week 1 focuses on network discovery and reconnaisance: mapping a target environment before targeting anything else. I used several tools to practice host discovery, port scanning, and service identification while documenting a network baseline.

---

## 🧪 Lab Environment

| Component | Details |
|---|---|
| Hypervisor | VMware Workstation (Host-Only Network) |
| Attacker Machine | Kali Linux 2026.1 — `192.168.79.129` |
| Target Machine | [Metasploitable 2] — `192.168.79.130` |
| Network Type | Host-Only (isolated, no internet exposure) |
| Host OS | Windows 11 — ASUS Vivobook 14 |

> ⚠️ **Note:** All activity was performed in a controlled, isolated lab environment against deliberately vulnerable machines. No unauthorized access to live networks was performed.

---

## 🛠️ Tools Used

- **[Nmap]** — [Scans a target to discover hosts, services, and open or closed ports.]
- **[Wireshark]** — [What it does]
- **[TCPDump]** — [What it does]
- **[Netcat]** — [What it does]

---

## 📁 Repository Structure

```
[Repo-Name]/
├── README.md
├── [folder-1]/
│   ├── [file1.txt]        # Description of file
│   └── [file2.md]         # Description of file
├── [folder-2]/
│   ├── [file3.md]         # Description of file
│   └── [file4.txt]        # Description of file
└── reports/
    └── [final-report.md]  # Full assessment report
```

---

## 🔬 Tasks / Assessments Performed

### 1. [Find all live hosts on the subnet using Ping Sweep]
[Brief description of what you did and why]
ping sweep to find all live hosts on my subnet to document which hosts are live so we can identify later any unidentifiable live hosts that I must look into.)

```bash
# Command used
[sudo nmap -n --disable-arp-ping -sP 192.168.79.0/24]
```
<img width="321" height="105" alt="image" src="https://github.com/user-attachments/assets/7a155223-f0bf-4037-ba05-c3272166fd92" />

*-sP is what makes it considered a ping sweep*

# Output
Command identified 3 live hosts on the subnet:
192.168.79.1 - Gateway/Router for the host-only network(VMwares virtual DHCP server).
192.168.79.130 - The metasploitable 2 target (Target)
192.168.79.129 - Kali (Attacker)

**Finding:** [What did you discover?]
I discovered there are 3 live hosts on this network the Gateway, Metasplotable (target), and Kali VM (ATTACKER).
A ping sweep is essentially asking devices on the network "Which devices on this network are alive?"
A live host is any active device with an IP address that is powered on, connected and capable of responding to network requests.
---

### 2. [Running a fast Nmap scan against Metasploitable]
[Used Nmap to scan the IP for metasploitable and displays the open ports.]

```bash
# Command used
[sudo nmap -n --disable-arp-ping -F 192.168.79.130]
<img width="307" height="227" alt="Screenshot 2026-07-14 214935" src="https://github.com/user-attachments/assets/27126024-60cd-4c86-a06c-a2dbeb0be8e3" />

```

**Finding:** [I found that 18 Ports are open in Metasploitable VM.I could also see  that 82 ports are closed ]
-F indicates user wants a fast scan (scan of the 1oo most common ports instead of a full 65,535 range of ports.)
-sT scans and displays only TCP ports.
-sU scans and displays only UDP ports. (UDP is much slower.)
TCP Is connection oriented(Uses handshake, confirms, delivery, and resends any lost data. (More reliable but slower than TCP.Used with Web browsing, SSH, email and other services that prioritize security and relaibilty over speed.)
UDP is connectionless and fires packets out without handshake or data being confirmed.(Faster but less reliable than TCP. Used with DNS lookups, streaming, and VoIP, where speed matters more than guaranteed delivery.)
---

### 3. [Save output to a file for refernce]
[Saved Nmap Scan output to a file for reference.]

```bash
# Command used
[sudo nmap -n --disable-arp-ping -F 192.168.79.130 -oN ~/baseline.txt]

<img width="384" height="243" alt="Screenshot 2026-07-14 225433" src="https://github.com/user-attachments/assets/d5793cb6-11d2-4d44-bff5-3e2e8952b201" />

<img width="319" height="229" alt="Screenshot 2026-07-14 225148" src="https://github.com/user-attachments/assets/7c1fda63-f0bc-4457-98de-ac3815e59271" />

```

**Finding:** [What did you discover?]
-oN indicates Normal output format, saves to disk instead of just printing to the termianal.
The command to display the file that was created: cat ~/baseline.txt
~/ : indicates a shortcut to save to the home directory.

---

## 📊 Key Findings Summary

| Port/Service | Tool Used | Risk Level | Notes |
|---|---|---|---|
| [e.g. 21/tcp FTP] | [e.g. Nmap] | 🔴 Critical | [Notes] |
| [e.g. 23/tcp Telnet] | [e.g. Wireshark] | 🔴 Critical | [Notes] |
| [e.g. 80/tcp HTTP] | [e.g. Nikto] | 🟠 High | [Notes] |
| [e.g. 3306/tcp MySQL] | [e.g. OpenVAS] | 🟠 High | [Notes] |
| [e.g. 22/tcp SSH] | [e.g. Nmap] | 🟡 Medium | [Notes] |

**Risk Levels:** 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low

---

## 🗺️ MITRE ATT&CK Mapping

| Action Performed | ATT&CK Tactic | Technique ID | Technique Name |
|---|---|---|---|
| [e.g. Port scanning] | [e.g. Reconnaissance] | [e.g. T1595] | [e.g. Active Scanning] |
| [Action] | [Tactic] | [ID] | [Technique] |
| [Action] | [Tactic] | [ID] | [Technique] |
| [Action] | [Tactic] | [ID] | [Technique] |

---

## 🛡️ Defensive Recommendations

Based on findings, the following remediations would be recommended in a real environment:

1. **[Metasplotable has many vulnerable ports open.]** — [Recommendation]
2. **[Finding 2]** — [Recommendation]
3. **[Finding 3]** — [Recommendation]
4. **[Finding 4]** — [Recommendation]
5. **[Finding 5]** — [Recommendation]

---

## 📚 CySA+ Exam Relevance

This lab directly maps to the following CompTIA CySA+ (CS0-003) exam domains:

| Domain | Coverage |
|---|---|
| Security Operations (33%) | [What this lab covers in this domain] |
| Vulnerability Management (30%) | [What this lab covers in this domain] |
| Incident Response (20%) | [What this lab covers in this domain] |
| Reporting & Communication (17%) | [What this lab covers in this domain] |

---

## 🔑 Technical Notes

> [Any important notes about your lab setup, workarounds, or lessons learned. Example:
>
> "Always add the -n flag to Nmap scans in this VMware environment to prevent DNS resolution hangs."]
> 
> -sP and -sT contradict eachother (Can't be used together because -sP means just do a ping/host-discovery sweep, skip ports entirely but -sT tells Nmap to do a full TCP connect port scan. These commands contradict eachother.)
Critical well known exploitable services that are open:
> *include these in the write up section*
> 21- FTP - metasploitable runs vsftpd 2.3.4, which has a wellknown backdoor vulnerability(CVE-2011-2523) which gives an attacker a root shell.
> 23 - Telnet - Sends credentials and traffic in plain text with no encryption at all.
> 445 - SMB - Has a long history 
> 139 - netbios-ssn -
> 5900 - VNC
High risk exploitable services:
> 3306 - MySQL
> 5432 - PostgreSQL
> 2049 - NFS
> 111 - rpcbind
> 6000 - X11
> 513/5144 - rlogin/ssh
Moderate risks:
> 25 - SMTP
> 80 - HTTP
> 8009 - ajp13
> 2121 - ccproxy-ftp

Ports with no modern use that should be disabled completely:
Telnet
513- rlogin
514- rsh
111- rpcbind
6000- X11
2121- ccproxy
21 - FTP
Should be disabled unless there is a documented buisness need:
139 - SMB
445 - NetBios
2049 - NFS
5900 - VNC
8009 - Ajp13
```bash
# Any important commands or workarounds
[command here]
```

---

## 📌 About This Project

[1-2 sentences about how this fits into your overall portfolio and career goals.]

**Related repositories:**
- `[Repo Name]` — [Brief description]
- `[Repo Name]` — [Brief description]
- `[Repo Name]` — Coming soon

---

## 👤 Author

**Dashane James**  
Senior Field Service Technician → Cybersecurity Analyst  
📍 Yonkers, NY  
🎓 B.S. Information Technology — SUNY Canton  
🏆 CompTIA Security+ | CySA+ (In Progress)  
🔗 [GitHub](https://github.com/Dashanejames1) | [Zero Trust Cyber Security Brand](https://www.instagram.com/zerotrust_cybersecurity)

---

*This repository is part 
