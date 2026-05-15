# Phase 3: Network Reconnaissance & Enumeration

> **Move beyond the web layer.** Learn to map the infrastructure that hosts web applications.
> This is where you find all the attack surfaces a web scanner misses.

---

## 🎓 Phase 3 Core Resources — Watch / Study Before Starting

### 🆓 Free Full Courses
| Course | Platform | URL | Notes |
|--------|----------|-----|-------|
| **Ethical Hacker** (Modules 3–4) | Cisco Networking Academy | https://skillsforall.com/course/ethical-hacker | Covers network scanning and enumeration in depth |
| **Jr Penetration Tester Path** | TryHackMe | https://tryhackme.com/path/outline/jrpenetrationtester | Structured path with free network rooms |

### 📺 Key YouTube Resources for This Phase
| Video / Channel | Best For | URL |
|---------|----------|-----|
| **[Ethical Hacking Full Course — freeCodeCamp](https://www.youtube.com/watch?v=3Kq1MIfTWCE)** | 15-hr course, covers Nmap, Metasploit, enumeration | https://www.youtube.com/watch?v=3Kq1MIfTWCE |
| **NetworkChuck** | Nmap beginners, network concepts | https://www.youtube.com/@NetworkChuck |
| **David Bombal** | Wireshark, advanced Nmap, protocol analysis | https://www.youtube.com/@davidbombal |
| **IppSec (ippsec.rocks)** | HTB walkthroughs showing real enumeration flow | https://ippsec.rocks |

---

## What You'll Learn
- Systematic host and service discovery with Nmap
- Enumerating high-value services: SMB, FTP, SSH, DNS, HTTP
- Directory brute-forcing to find hidden endpoints
- Automated vulnerability scanning

---

## Practice Lab Setup

| Target VM | Download | Notes |
|-----------|----------|-------|
| **Metasploitable 2** | https://sourceforge.net/projects/metasploitable/ | Classic, safe, intentionally vulnerable |
| **VulnHub** | https://www.vulnhub.com/ | Hundreds of themed VMs |
| **TryHackMe** | https://tryhackme.com/paths | Browser-based rooms, no VM needed |

> ⚠️ Set Metasploitable 2's network adapter to **Host-Only** before booting.

---

## Module 3.1 — Advanced Nmap Scanning

### Resources
| Resource | URL |
|----------|-----|
| Nmap Official Book | https://nmap.org/book/toc.html |
| Nmap NSE Script List | https://nmap.org/nsedoc/ |
| TryHackMe: Nmap Room | https://tryhackme.com/room/furthernmap |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [NMAP Tutorial to find Network Vulnerabilities](https://www.youtube.com/watch?v=4t4kBkMsDbQ) | NetworkChuck | Most beginner-friendly, full breakdown of all key flags |
| [Nmap for Ethical Hackers — Full Tutorial](https://www.youtube.com/watch?v=a1J2KeEy0Zs) | David Bombal | Advanced Nmap including NSE scripts and output parsing |
| [Full Nmap Tutorial](https://www.youtube.com/watch?v=5MTZdN9TEO4) | HackerSploit | Methodical enumeration from a pentester’s perspective |

### Core Nmap Techniques

```bash
# ─── Discovery ──────────────────────────────────────────────────────
# Ping sweep — find live hosts
nmap -sn 192.168.56.0/24

# ─── Port Scanning ──────────────────────────────────────────────────
# Fast scan of top 1000 ports
nmap -sS 192.168.56.101

# Full scan — all 65535 ports (slower but complete)
nmap -sS -p- 192.168.56.101

# UDP scan (catches services ping scans miss: DNS, SNMP, TFTP)
nmap -sU --top-ports 100 192.168.56.101

# ─── Version & OS Detection ─────────────────────────────────────────
# Service version detection
nmap -sV 192.168.56.101

# Aggressive scan (version + OS + scripts)
nmap -A 192.168.56.101

# ─── NSE Scripts ────────────────────────────────────────────────────
# Run default scripts
nmap -sC 192.168.56.101

# Run a specific script (e.g., SMB vulnerability check)
nmap --script smb-vuln-ms17-010 192.168.56.101

# Run all vuln scripts
nmap --script vuln 192.168.56.101

# ─── Output ─────────────────────────────────────────────────────────
# Save all output formats (grepable, XML, normal)
nmap -sV -oA scan_results 192.168.56.101
```

### Reading Nmap Output
```
PORT     STATE  SERVICE  VERSION
22/tcp   open   ssh      OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp   open   http     Apache httpd 2.2.8
445/tcp  open   netbios-ssn Samba smbd 3.X - 4.X
3306/tcp open   mysql    MySQL 5.0.51a-3ubuntu5
```
**What to look for:**
- Old software versions → likely CVEs exist
- Services that shouldn't be publicly exposed (MySQL, Redis, memcached)
- Unusual ports

---

## Module 3.2 — Service Enumeration

### Resources
| Resource | URL |
|----------|-----|
| HackTricks (massive reference) | https://book.hacktricks.xyz |
| TryHackMe: Network Services | https://tryhackme.com/room/networkservices |
| enum4linux-ng | https://github.com/cddmp/enum4linux-ng |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [SMB Enumeration — Metasploitable 2](https://www.youtube.com/watch?v=70sPTJzgDWE) | HackerSploit | Hands-on SMB enum against the same target you’ll use |
| [FTP Exploitation Tutorial](https://www.youtube.com/watch?v=GkSLFd-VfvQ) | HackerSploit | Anonymous FTP, banner grabbing, vsftpd recon |
| [DNS Enumeration for Beginners](https://www.youtube.com/watch?v=Y4qjPEMOGLA) | HackerSploit | Zone transfers and subdomain discovery |

### SMB (Port 445) — Windows File Sharing
```bash
# Enumerate SMB shares (null session)
smbclient -L //192.168.56.101 -N

# Connect to a share
smbclient //192.168.56.101/tmp -N

# Deeper enumeration with enum4linux
enum4linux -a 192.168.56.101

# Nmap SMB scripts
nmap --script smb-enum-shares,smb-enum-users 192.168.56.101
```

### FTP (Port 21) — File Transfer
```bash
# Check for anonymous login
ftp 192.168.56.101
# Username: anonymous | Password: (blank)

# Nmap FTP scripts
nmap --script ftp-anon,ftp-bounce 192.168.56.101
```

### SSH (Port 22)
```bash
# Check supported authentication methods
nmap --script ssh-auth-methods 192.168.56.101

# Check host key algorithms (old = vulnerable)
nmap --script ssh2-enum-algos 192.168.56.101

# Try default credentials (for CTF/lab scenarios)
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.56.101
```

### DNS (Port 53) — Zone Transfers & Subdomain Discovery
```bash
# Zone transfer attempt
dig axfr @192.168.56.101 target.com

# Subdomain brute-force
gobuster dns -d target.com -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt

# dnsrecon
dnsrecon -d target.com -t axfr
```

### HTTP/HTTPS (Ports 80/443)
```bash
# Technology fingerprinting
whatweb http://192.168.56.101

# Nikto vulnerability scanner
nikto -h http://192.168.56.101

# curl to inspect headers
curl -I http://192.168.56.101
```

---

## Module 3.3 — Directory Brute-Forcing

### Resources
| Resource | URL |
|----------|-----|
| SecLists (wordlists) | https://github.com/danielmiessler/SecLists |
| ffuf | https://github.com/ffuf/ffuf |
| gobuster | https://github.com/OJ/gobuster |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [ffuf — Fast Web Fuzzer Tutorial](https://www.youtube.com/watch?v=aN3Nayvd7FU) | HackerSploit | Covers directory, file, and subdomain fuzzing with real examples |
| [Directory Enumeration with gobuster](https://www.youtube.com/watch?v=e83lp6b3NUw) | NetworkChuck | Beginner-friendly walkthrough, explains every flag |

### Install SecLists (Essential Wordlists)
```bash
sudo apt install -y seclists
# Lives at /usr/share/seclists/
```

### ffuf — Fast Web Fuzzer
```bash
# Directory/file brute-force
ffuf -u http://192.168.56.101/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt

# With extension filtering (find .php, .txt, .bak files)
ffuf -u http://192.168.56.101/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt \
     -e .php,.txt,.bak,.html

# Subdomain fuzzing
ffuf -u http://FUZZ.target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -H "Host: FUZZ.target.com"

# Filter out 404 and size-based noise
ffuf -u http://192.168.56.101/FUZZ -w wordlist.txt -fc 404 -fs 1234
```

### gobuster — Classic Directory Buster
```bash
# Directory mode
gobuster dir -u http://192.168.56.101 -w /usr/share/seclists/Discovery/Web-Content/common.txt

# DNS subdomain mode
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

---

## Module 3.4 — Vulnerability Scanning

### Resources
| Resource | URL |
|----------|-----|
| OpenVAS | https://www.openvas.org/ |
| Nessus Essentials (Free) | https://www.tenable.com/products/nessus/nessus-essentials |

### Quick Nmap Vulnerability Scan
```bash
# Runs all vuln NSE scripts against a host
nmap --script vuln 192.168.56.101 -oN vuln_scan.txt
```

### Nessus Essentials (Free for up to 16 IPs)
```bash
# Install Nessus on Kali
# 1. Download .deb from https://www.tenable.com/products/nessus/nessus-essentials
# 2. Install:
sudo dpkg -i Nessus-*.deb
sudo systemctl start nessusd
# 3. Access at: https://localhost:8834
```

---

## 🏆 Verifiable Step: Metasploitable 2 Network Map

**Do this before moving to Phase 4:**

### Setup
```bash
# 1. Boot Metasploitable 2 VM (set to Host-Only network)
# 2. Log in: msfadmin / msfadmin
# 3. Find its IP:
ifconfig
# Note the IP (e.g., 192.168.56.102)
```

### Your Task
Run the following from your Kali machine and save the output:

```bash
# Full port scan
nmap -sV -sC -p- 192.168.56.102 -oA metasploitable_scan

# SMB enumeration
enum4linux -a 192.168.56.102

# Directory brute-force on the web server
ffuf -u http://192.168.56.102/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt
```

### Deliverable
Create a file `./lab-notes/metasploitable-network-map.md` with:
```markdown
# Metasploitable 2 Network Map

## Open Ports & Services
| Port | Protocol | Service | Version | Notes |
|------|----------|---------|---------|-------|
| 21   | TCP      | FTP     | vsftpd 2.3.4 | Anonymous login allowed |
| ...  | ...      | ...     | ...     | ... |

## Interesting Findings
- List any misconfigurations or unusual services

## Attack Surface
- What would you target first and why?
```

### 🤖 Ask Gemma to Help Analyze
```bash
ollama run gemma3
# Prompt:
# "I've scanned Metasploitable 2 and found these open ports: [paste nmap output]
# Acting as a penetration tester, help me prioritize which services to attack first
# and explain why each one is potentially vulnerable."
```

**Mark all Phase 3 tasks ✅ in [PROGRESS.md](../PROGRESS.md)**

---

## Additional Resources
| Resource | URL |
|----------|-----|
| HackTricks — Network Pentesting | https://book.hacktricks.xyz/network-services-pentesting |
| TryHackMe — Jr Penetration Tester Path | https://tryhackme.com/path/outline/jrpenetrationtester |
| IppSec YouTube (HTB machine walkthroughs) | https://www.youtube.com/@ippsec |

---

➡️ **Next Phase:** [Phase 4 — System Hacking & Exploitation](../phase-04-exploitation/README.md)
