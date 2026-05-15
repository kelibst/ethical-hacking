# Lab Machines Reference

> Notes and setup instructions for all practice VMs and platforms used in this course.

---

## 🖥️ Metasploitable 2

**Purpose:** Primary Phase 3 & 4 practice target  
**Difficulty:** Beginner  
**Type:** Intentionally vulnerable Linux VM

### Setup
```bash
# 1. Download from:
# https://sourceforge.net/projects/metasploitable/

# 2. Import into VirtualBox:
# File → Import Appliance → select .vmdk or .ova

# ⚠️  CRITICAL: Set Network to "Host-Only Adapter"
# Adapter 1 → Host-Only Adapter → vboxnet0

# 3. Boot and login:
# Username: msfadmin
# Password: msfadmin

# 4. Find the IP:
ifconfig
```

### Services & Vulnerabilities (Cheat Reference)
| Port | Service | Known Vulnerability |
|------|---------|-------------------|
| 21 | vsftpd 2.3.4 | Backdoor (CVE-2011-2523) |
| 22 | OpenSSH 4.7 | Brute-force, old version |
| 23 | Telnet | Cleartext credentials |
| 25 | Sendmail | Various |
| 80 | Apache 2.2 + PHP | DVWA, TWiki, phpMyAdmin |
| 139/445 | Samba 3.X | Username enumeration, various |
| 3306 | MySQL 5.0 | No root password by default |
| 5432 | PostgreSQL | Weak credentials |
| 6667 | UnrealIRCd | Backdoor |
| 8180 | Apache Tomcat | Default credentials |

> **Rule:** Always scan first. Don't use this reference until you've discovered services yourself.

---

## 🌐 DVWA (Damn Vulnerable Web App)

**Purpose:** Phase 2 web application practice  
**Difficulty:** Configurable (Low → High)  
**Type:** Docker or local PHP app

### Setup via Docker
```bash
docker run -d -p 8080:80 --name dvwa vulnerables/web-dvwa
# Access: http://localhost:8080
# Username: admin | Password: password
# Click "Create / Reset Database" on first login
```

### Security Levels
- **Low:** No defenses at all — easiest to exploit
- **Medium:** Basic defenses (blacklists, basic sanitization) — can be bypassed
- **High:** Strong defenses — requires more advanced techniques
- **Impossible:** Properly secured — useful to see how code SHOULD look

### Practice Modules
| Module | What to Learn |
|--------|-------------|
| SQL Injection | Basic → UNION-based → Blind |
| XSS (Reflected) | Basic payload → filter bypass |
| XSS (Stored) | Persistent payload storage |
| XSS (DOM) | JavaScript sink exploitation |
| Command Injection | OS command chaining |
| File Upload | Bypass extension checks → web shell |
| CSRF | Token bypass techniques |
| File Inclusion (LFI) | Path traversal, log poisoning |

---

## 🐋 DVNA (Damn Vulnerable Node Application)

**Purpose:** Phase 2 — Node.js/Express specific vulnerabilities  
**Difficulty:** Intermediate  
**Type:** Docker

```bash
docker run -d -p 3000:3000 --name dvna appsecco/dvna:sqlite
# Access: http://localhost:3000
# Register a new account to get started
```

**Why useful for you:** This is Node.js + Express — very close to your Next.js background. It demonstrates:
- SQLI in SQLite queries
- NoSQL injection
- Server-side JS injection
- Sensitive data exposure

---

## 🎯 VulnHub Machines

**Platform:** https://www.vulnhub.com  
**Purpose:** Phase 3, 4, 5 — Full attack chain practice

### Recommended by Phase

#### Phase 3-4 (Initial Access)
| Machine | URL | Key Skills |
|---------|-----|-----------|
| Kioptrix Level 1 | https://www.vulnhub.com/entry/kioptrix-level-1-1,22/ | Samba exploit, Apache |
| Mr. Robot: 1 | https://www.vulnhub.com/entry/mr-robot-1,151/ | WordPress, custom wordlists |

#### Phase 5 (PrivEsc Focus)
| Machine | URL | Key Skills |
|---------|-----|-----------|
| Basic Pentesting: 1 | https://www.vulnhub.com/entry/basic-pentesting-1,216/ | SSH, cron PrivEsc |
| DC-1 | https://www.vulnhub.com/entry/dc-1,292/ | Drupal, SUID PrivEsc |

### Import into VirtualBox
```bash
# 1. Download the .ova or .vmdk from VulnHub
# 2. VirtualBox: File → Import Appliance
# 3. Set Network: Host-Only Adapter
# 4. Boot and find the IP:
nmap -sn 192.168.56.0/24
```

---

## 🔐 Hack The Box (HTB)

**Platform:** https://app.hackthebox.com  
**Purpose:** Phase 4, 5, 6 — Realistic, realistic machines

### Getting Started
```bash
# 1. Create account at hackthebox.com
# 2. Go to Labs → Starting Point
# 3. Download your VPN config file
# 4. Connect:
sudo openvpn starting_point_username.ovpn
```

### Recommended Machines by Phase

#### Tier 0 (Phase 3-4)
| Machine | OS | Skills |
|---------|-----|--------|
| Meow | Linux | Telnet, basic enumeration |
| Fawn | Linux | FTP anonymous login |
| Dancing | Windows | SMB enumeration |
| Explosion | Windows | RDP, weak credentials |

#### Tier 1-2 (Phase 4-5)
| Machine | OS | Skills |
|---------|-----|--------|
| Archetype | Windows | MSSQL, SMB, WinRM |
| Oopsie | Linux | Web + SUID PrivEsc |
| Vaccine | Linux | SQLi, sudo PrivEsc |
| Responder | Windows | File inclusion, NTLM relay |

### Finding Machine IPs After VPN Connect
```bash
# HTB gives you the target IP when you "spawn" the machine
# It's shown on the machine's page after clicking "Start Machine"
# Typically in the 10.10.x.x range
```

---

## 🌐 TryHackMe

**Platform:** https://tryhackme.com  
**Purpose:** All phases — guided, beginner-friendly

### Recommended Rooms (Free)

| Room | Phase | URL |
|------|-------|-----|
| Intro to Offensive Security | Phase 1 | https://tryhackme.com/room/introtooffensivesecurity |
| Nmap | Phase 3 | https://tryhackme.com/room/furthernmap |
| Metasploit: Introduction | Phase 4 | https://tryhackme.com/room/metasploitintro |
| Linux PrivEsc | Phase 5 | https://tryhackme.com/room/linprivesc |
| Windows PrivEsc | Phase 5 | https://tryhackme.com/room/windows10privesc |
| OWASP Top 10 - 2021 | Phase 2 | https://tryhackme.com/room/owasptop102021 |

### Connection Options
```bash
# Option A: Use AttackBox (browser-based Kali) — 1 hr free per day

# Option B: Connect your own Kali via OpenVPN
sudo openvpn tryhackme_username.ovpn
# Download config from: https://tryhackme.com/access
```

---

## ⚠️ Lab Safety Rules

1. **Always use Host-Only networking** for intentionally vulnerable VMs
2. **Never expose practice VMs** to the internet or untrusted networks
3. **Take snapshots** before major changes so you can revert
4. **Only attack machines you have permission to test** — all machines in this guide are intentionally vulnerable for learning
5. **Keep notes** of every finding for your Phase 6 report
