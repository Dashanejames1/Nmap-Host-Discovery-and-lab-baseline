# Nmap-Host-Discovery-and-Lab-Baseline

**Author:** Dashane James  
**Lab Environment:** VMware Workstation | Kali Linux | Metasploitable 2  
**Purpose:** Establish a documented network baseline, the foundation every later assessment in this portfolio is built on.  
**Status:** 🔵 Completed

---

## 📋 Overview

The first step in any security assessment is figuring out what's actually there. Before evaluating vulnerabilities or analyzing traffic, you need to map the network: identify which hosts are live, which ports are open, and save that state as a documented baseline you can refer back to.

This repository covers exactly that: running a ping sweep to locate live hosts on the subnet, performing a fast Nmap scan against the target to identify open ports, and saving the output to a file. It's the starting point for the entire Week 1 reconnaissance workflow. Every subsequent lab (version detection, packet analysis, vulnerability scripting) traces back to the port list and baseline established here.

---

## 🧪 Lab Environment

| Component | Details |
|---|---|
| Hypervisor | VMware Workstation (Host-Only Network) |
| Attacker Machine | Kali Linux 2026.1, `192.168.79.129` |
| Target Machine | Metasploitable 2, `192.168.79.130` |
| Network Type | Host-Only (isolated, no internet exposure) |
| Host OS | Windows 11, ASUS Vivobook 14 |

> ⚠️ **Note:** All activity was performed in a controlled, isolated lab environment against a deliberately vulnerable machine. No unauthorized access to live networks was performed.

---

## 🛠️ Tools Used

- **Nmap**: Used to perform host discovery, port scanning, and save scan output to a file for later reference
- **Metasploitable 2**: Intentionally vulnerable Linux target machine used as the subject of all scanning activity

---

## 🔬 Tasks / Assessments Performed

### 1. Find all live hosts on the subnet using a ping sweep

Before scanning any specific target, I needed to know what was alive on the network. A ping sweep sends ICMP requests across the entire subnet and reports back which IP addresses responded, essentially asking "which devices on this network are on right now?"

```bash
sudo nmap -n --disable-arp-ping -sP 192.168.79.0/24
```

<img width="321" height="105" alt="image" src="https://github.com/user-attachments/assets/7a155223-f0bf-4037-ba05-c3272166fd92" />

**Finding:**

The scan found 3 live hosts on the subnet:
- `192.168.79.1`: the gateway/virtual DHCP server (VMware's virtual router for the host-only network)
- `192.168.79.129`: Kali Linux (the attacker machine)
- `192.168.79.130`: Metasploitable 2 (the target)

This kind of host inventory matters in a real engagement because any unrecognized live host needs to be investigated. In this lab, all three were expected, but the habit of documenting what's on the network before you start touching anything is foundational to a clean assessment.

---

### 2. Run a fast Nmap scan against Metasploitable

With the target confirmed at 192.168.79.130, I ran a fast scan to get a quick view of which ports were open. The `-F` flag limits the scan to the 100 most commonly used ports rather than all 65,535, which makes it useful for rapid triage.

```bash
sudo nmap -n --disable-arp-ping -F 192.168.79.130
```

<img width="307" height="227" alt="Screenshot 2026-07-14 214935" src="https://github.com/user-attachments/assets/27126024-60cd-4c86-a06c-a2dbeb0be8e3" />

**Finding:**

The scan identified 18 open TCP ports on Metasploitable. 82 ports came back closed. That alone tells a story, a legitimate server typically has a handful of ports open for specific services. Eighteen open ports on a single host is a significant attack surface.

At this stage the scan only tells me which ports are open and what service name Nmap guesses from the port number. What's actually running on each port, and whether it's vulnerable, is what the rest of the week's labs work through.

---

### 3. Save scan output to a file for reference

Rather than just printing the results to the screen and moving on, I saved the scan output to a file. This is standard practice in any real assessment, you need a record of what the network looked like before you started making changes, running exploits, or generating alerts.

```bash
sudo nmap -n --disable-arp-ping -F 192.168.79.130 -oN ~/baseline.txt
```

<img width="384" height="243" alt="Screenshot 2026-07-14 225433" src="https://github.com/user-attachments/assets/d5793cb6-11d2-4d44-bff5-3e2e8952b201" />

<img width="319" height="229" alt="Screenshot 2026-07-14 225148" src="https://github.com/user-attachments/assets/7c1fda63-f0bc-4457-98de-ac3815e59271" />

**Finding:**

The `-oN` flag saves the output in normal (human-readable) format. The `~/` prefix saves it to the home directory, which keeps it in a predictable location rather than wherever the terminal happens to be open. To view it later: `cat ~/baseline.txt`.

This file became the reference point for all subsequent scans. When later labs uncovered additional ports or services, I was able to compare them against this baseline to see what had and hadn't been captured in the initial fast scan.

---

## 📊 Key Findings Summary

| Port/Service | Tool Used | Risk Level | Notes |
|---|---|---|---|
| 21/tcp FTP | Nmap | 🔴 Critical | Metasploitable runs vsftpd 2.3.4, a version with a documented backdoor vulnerability (CVE-2011-2523) that allows a remote attacker to gain a root shell |
| 23/tcp Telnet | Nmap | 🔴 Critical | Transmits all data including credentials in plaintext with no encryption |
| 445/tcp SMB | Nmap | 🔴 Critical | SMB has a long history of critical vulnerabilities; also allows enumeration of shares, users, and sometimes anonymous login |
| 139/tcp NetBIOS | Nmap | 🔴 Critical | Often paired with SMB issues; allows enumeration of system and network information |
| 5900/tcp VNC | Nmap | 🔴 Critical | Remote desktop protocol; Metasploitable's VNC instance uses a weak or absent password, giving full GUI-level access |
| 80/tcp HTTP | Nmap | 🟡 Medium | Metasploitable hosts several intentionally vulnerable web applications (DVWA, Mutillidae, etc.), further analyzed in the Wireshark lab |
| 3306/tcp MySQL | Nmap | 🟠 High | Database exposed directly on the network; Metasploitable's MySQL is configured with weak default credentials |
| 2049/tcp NFS | Nmap | 🟠 High | Misconfigured NFS shares can allow unauthorized file access |
| 111/tcp rpcbind | Nmap | 🟠 High | Exposes RPC service information, assisting attacker enumeration |
| 25/tcp SMTP | Nmap | 🟡 Medium | Can allow email relay abuse or reveal system information via banner grabbing |

**Risk Levels:** 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low

---

## 🗺️ MITRE ATT&CK Mapping

| Action Performed | ATT&CK Tactic | Technique ID | Technique Name |
|---|---|---|---|
| Ping sweep to identify live hosts | Reconnaissance | T1595.001 | Scanning IP Blocks |
| Fast port scan of Metasploitable | Reconnaissance | T1046 | Network Service Discovery |

---

## 🛡️ Defensive Recommendations

Based on findings, the following remediations would be recommended in a real environment:

1. **vsftpd 2.3.4 backdoor (Critical)**: Upgrade or replace immediately. This version contains a known, publicly documented backdoor (CVE-2011-2523) that should never run in any environment.
2. **Telnet enabled (Critical)**: Disable Telnet and replace with SSH for all remote administration. Telnet transmits credentials and session data in plaintext.
3. **SMB/NetBIOS exposed (Critical)**: Disable SMB and NetBIOS if not required. Where SMB is needed, restrict access via firewall rules and enforce message signing.
4. **VNC with weak or no authentication (Critical)**: Disable VNC or enforce strong authentication. Tunnel VNC access through SSH or VPN.
5. **Databases exposed to the network (High)**: MySQL and other database services should never be directly accessible on a network interface. Bind to localhost only and enforce strong credentials.

---

## 🔑 Technical Notes

```bash
# Find all live hosts on the subnet (ping sweep)
sudo nmap -n --disable-arp-ping -sP 192.168.79.0/24

# Fast scan, top 100 TCP ports against a specific target
sudo nmap -n --disable-arp-ping -F 192.168.79.130

# Fast scan with output saved to a file for later reference
sudo nmap -n --disable-arp-ping -F 192.168.79.130 -oN ~/baseline.txt

# View a saved scan output file
cat ~/baseline.txt

# Note: ~/ is a shortcut for the home directory (e.g. /home/kali).
# Saving output here keeps it in a consistent, predictable location
# rather than wherever the terminal happens to be open.

# VMware lab note: always add -n to Nmap scans in this environment.
# DNS resolution hangs indefinitely on the isolated host-only network
# since there is no real DNS server to answer queries.
```

---

## 📌 About This Project

This repository is the starting point of a broader reconnaissance portfolio. The baseline established here (3 live hosts, 18 open ports, scan output saved to file) is the foundation every later lab builds on.

The six repositories that follow this one pick up where the port list ends: comparing how different scan types find (or miss) those ports, identifying the exact software versions running on them, capturing what's moving across the wire, and ultimately confirming that one of those services had a critical backdoor that could be exploited for root access.

**Related repositories (in order):**

- `Nmap-Scan-Types-SYN-vs-TCP-vs-UDP`: Compared SYN, TCP connect, and UDP scan types against the 18 ports identified here, examining speed, stealth, and completeness tradeoffs
- `Service-Version-Detection-and-OS-Fingerprinting`: Identified exact software versions running on the open ports, then researched real CVEs tied to those specific versions
- `Wireshark-Capture-and-Analyze-Traffic`: Captured and analyzed live traffic against the same target, including plaintext credential exposure via HTTP
- `TCPDump-CLI-Packet-Capture`: Demonstrated command-line packet capture, including a live Telnet session revealing plaintext data in ASCII
- `Nmap-Scripting-Engine`: Used NSE scripts to confirm and actively exploit the vsftpd vulnerability first identified in this baseline, resulting in a confirmed root shell

---

## 👤 Author

**Dashane James**  
Senior Field Service Technician → Cybersecurity Analyst  
📍 Yonkers, NY  
🎓 B.S. Information Technology, SUNY Canton  
🏆 CompTIA Security+  
🔗 [GitHub](https://github.com/Dashanejames1) | [Zero Trust Cyber Security Brand](https://www.instagram.com/zerotrust_cybersecurity)

---

*This repository is part of an active portfolio demonstrating hands-on cybersecurity skills. All lab work performed in isolated environments for educational purposes.*
