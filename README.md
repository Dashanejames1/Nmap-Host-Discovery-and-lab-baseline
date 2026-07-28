# Nmap-Host-Discovery-and-lab-baseline

**Author:** Dashane James  
**Lab Environment:** [VMware Workstation | Kali Linux | Metasploitable 2]  
**Purpose:** [To mirror the first phase of real-world penetration testing and develop practical hands-on fluency with the reconnaisance phase ]  
**Status:** 🟢 Active / 🟡 In Progress / 🔵 Completed

---

## 📋 Overview

 Week 1, this repository focuses on network discovery and reconnaisance: mapping a target environment before targeting anything else. I used several tools to practice host discovery, port scanning, and service identification while documenting a network baseline.

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
- **[Metasploitable]** — [Acts as a target machine with many intentional vulnerabilities to practice penetration testing, ethical hacking, and securely auditing safely. ]

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
| [e.g. 21/tcp FTP] | [e.g. Nmap] | 🔴 Critical | [ metasploitable runs vsftpd 2.3.4, which has a wellknown backdoor vulnerability(CVE-2011-2523) which gives an attacker a root shell.] |
| [e.g. 23/tcp Telnet] | [e.g. Nmap] | 🔴 Critical | [Sends credentials and traffic in plain text with no encryption at all.] |
| [e.g. 445/tcp SMB] | [e.g. Nmap] | 🔴 Critical | [MB has a long history of critical exploits (e.g., EternalBlue-class vulnerabilities). Also enables enumeration of shares, users, and sometimes anonymous login.] |
| [e.g. 139/tcp Netbios] | [e.g. Nmap] | 🔴 Critical | [often paired with SMB issues, allows enumeration of system/network info.] |
| [e.g. 5900/tcp VNC] | [e.g. Nmap] | 🔴 Critical | [remote desktop access; Metasploitable's VNC is famously configured with a weak/no password, giving full GUI control.] |
| [e.g. 80/tcp HTTP] | [e.g. Nikto] | 🟠 High | [Notes] |
| [e.g. 3306/tcp MySQL & PostgreSQL] | [e.g. Nmap] | 🟠 High | [databases exposed to the network; Metasploitable's are set up with weak or default credentials, risking data exposure or command execution.] |
| [e.g. 2049/tcp NFS] | [e.g. Nmap] | 🟠 High | [misconfigured NFS shares can allow unauthorized file access.]
| [e.g. 111/rpcbind] | [e.g. Nmap] | 🟠 High | [rpcbind exposes RPC services that assist in enumeration/attacks.]
| [e.g. 25/tcp SMTP] | [e.g. Nmap] | 🟡 Medium | [can allow email relay abuse or reveal system info via banner grabbing.] |
| [e.g. 80/tcp HTTP] | [e.g. Nmap] | 🟡 Medium | Metasploitable's web apps (DVWA, Mutillidae, etc.) are intentionally full of vulnerabilities (SQLi, XSS, command injection.] |

**To enumerate is to systematically query a target system or network to extract detailed, valuable information**
What is SMB, Netbios, VNC, NFS, rpcbind ??

**Good writeup structure: for each port, note the service, the specific known CVE if there is one, and the real-world remediation (patch version, disable service, enforce auth, segment network). That CVE-2011-2523 FTP backdoor is probably your strongest example to lead with — it's a textbook case and easy to explain clearly in an interview.**

**Risk Levels:** 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low

---

## 🗺️ MITRE ATT&CK Mapping

| Action Performed | ATT&CK Tactic | Technique ID | Technique Name |
|---|---|---|---|
| Ping sweep to discover live hosts | Reconnaissance | T1595.001 | Scanning IP Blocks |
| Fast port scan of Metasploitable | Reconnaissance | T1046 | Network Service Discovery |
| Saving scan output for later reference | Collection | T1005 | Data from Local System |

---

## 🛡️ Defensive Recommendations

Based on findings, the following remediations would be recommended in a real environment:

1. **[Metasplotable has many vulnerable ports open.]** — [Recommendation]

1. **vsftpd 2.3.4 backdoor (Critical)** — Upgrade or replace immediately; this version contains a known, publicly documented backdoor (CVE-2011-2523).
2. **Telnet enabled (Critical)** — Disable Telnet and replace with SSH for all remote administration.
3. **SMB/NetBIOS exposed with weak configuration (Critical)** — Disable SMB/NetBIOS if not required, or restrict access via host-based firewall rules; enforce message signing where SMB is necessary.
4. **VNC with weak/no authentication (Critical)** — Disable VNC or enforce strong authentication and tunnel access through SSH/VPN.
5. **Databases (MySQL/PostgreSQL) exposed to the network (High)** — Restrict database access to localhost or an internal-only network segment; enforce strong credentials.
---


## 🔑 Technical Notes

# Ping sweep to find all live hosts on the subnet
sudo nmap -n --disable-arp-ping -sP 192.168.79.0/24

# Fast scan (top 100 ports) against a specific target
sudo nmap -n --disable-arp-ping -F 192.168.79.130

# Save scan output to a file for later reference
sudo nmap -n --disable-arp-ping -F 192.168.79.130 -oN ~/baseline.txt

# View a saved scan output file
cat ~/baseline.txt

# Note: ~/ is a shortcut for the home directory (e.g. /home/kali).
# Omitting it saves the file to whatever directory you're currently in
# instead of a predictable, consistent location.
>
> **"Always add the -n flag to Nmap scans in this VMware environment to prevent DNS resolution hangs."]**
> 
> **-sP and -sT contradict eachother (Can't be used together because -sP means just do a ping/host-discovery sweep, skip ports entirely but -sT tells Nmap to do a full TCP connect port scan. These commands contradict eachother.)**


---

## 📌 About This Project

[This repository is the starting point of my broader cybersecurity portfolio — establishing the foundational reconnaissance and host discovery skills that every later assessment (service detection, vulnerability scanning, packet analysis, and exploitation) builds on.]

**Related repositories:**
- `Nmap-Scan-Types-SYN-vs-TCP-vs-UDP` — Compared SYN, TCP connect, and UDP scan types against the ports identified as open in this baseline
- `Service-Version-Detection-and-OS-Fingerprinting` — Identified exact software versions on the ports found open here, and researched real CVEs tied to them
- `Nmap-Scripting-Engine` — Used NSE scripts to detect and, in one case, actively exploit the vsftpd vulnerability first identified in this repository
- `Wireshark-Capture-and-Analyze-Traffic` — Captured and analyzed live traffic against the same target established here
- `TCPDump-CLI-Packet-Capture` — Captured command-line packet traffic, including a live Telnet session on a port identified as open in this baseline

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
