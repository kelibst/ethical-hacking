# Phase 1: Hacker Methodology & Attack Infrastructure

> **Goal:** Establish a professional attack lab and internalize the PTES methodology used by real pentesters.
> **Prerequisite:** Comfort with the terminal, VMs, and a Linux environment. ✅ You have this.

---

## 📚 What You'll Learn

- How to set up and optimize Kali Linux as an attack machine
- The Penetration Testing Execution Standard (PTES) — the framework professionals follow
- The purpose and basic operation of the 5 core tools in a pentester's arsenal

---

## Module 1.1 — Environment Setup

### Objective
Get a hardened, optimized Kali Linux attack machine running.

### Resources
| Resource | Type | URL | Cost |
|----------|------|-----|------|
| Kali Linux Pre-built VM | Official Download | https://www.kali.org/get-kali/#kali-virtual-machines | Free |
| Kali on WSL2 | Official Docs | https://www.kali.org/docs/wsl/wsl-preparations/ | Free |
| VirtualBox | Hypervisor | https://www.virtualbox.org/wiki/Downloads | Free |
| Kali Linux Revealed (Book) | Free Book | https://kali.training/downloads/Kali-Linux-Revealed-1st-edition.pdf | Free |

### Steps

**Option A: VirtualBox VM (Recommended for isolated lab)**
```bash
# 1. Download the VirtualBox image from kali.org
# 2. Import it: File → Import Appliance → select the .ova file
# 3. Set Network to "Host-Only" for lab isolation
# 4. Boot and update:
sudo apt update && sudo apt full-upgrade -y

# 5. Install extra tools
sudo apt install -y tmux zsh git curl wget gobuster ffuf seclists
```

**Option B: Kali on WSL2 (If you prefer staying on your host OS)**
```powershell
# In Windows PowerShell (Admin):
wsl --install -d kali-linux
# Then launch and run:
sudo apt update && sudo apt install -y kali-linux-default
```

### Terminal Power-Up (Do This Once)
```bash
# Install Oh My Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Install tmux plugin manager
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# Set up a basic tmux config for multi-pane hacking sessions
cat > ~/.tmux.conf << 'EOF'
set -g mouse on
set -g history-limit 10000
bind | split-window -h
bind - split-window -v
set -g default-terminal "screen-256color"
EOF
```

### Key Concept: Why an Isolated VM?
Kali Linux is intentionally built to be used as an offensive platform. Never run it as your primary OS or expose it to the internet without understanding its defaults. The **Host-Only** network mode ensures your vulnerable practice VMs can only talk to your Kali machine — not the real internet.

---

## Module 1.2 — The PTES Methodology

### Objective
Understand the professional framework that separates ethical hackers from script kiddies.

### Resources
| Resource | Type | URL | Cost |
|----------|------|-----|------|
| PTES Technical Guidelines | Official Spec | http://www.pentest-standard.org/index.php/Main_Page | Free |
| OWASP Testing Guide v4.2 | Free PDF | https://owasp.org/www-project-web-security-testing-guide/ | Free |
| TCM Security — Practical Ethical Hacking Course | Course (paid but excellent) | https://academy.tcm-sec.com/p/practical-ethical-hacking-the-complete-course | ~$30 |

### The 7 Phases of PTES

```
┌─────────────────────────────────────────────────────────────────┐
│                    PTES - The Full Lifecycle                     │
├─────────────────────────────────────────────────────────────────┤
│ 1. Pre-Engagement     → Define scope, get written authorization  │
│ 2. Intelligence       → OSINT, passive & active recon           │
│    Gathering                                                     │
│ 3. Threat Modeling    → What are the crown jewels to protect?   │
│ 4. Vulnerability      → Scan, identify, and research weaknesses  │
│    Analysis                                                      │
│ 5. Exploitation       → Gain access by firing exploits          │
│ 6. Post-Exploitation  → PrivEsc, pivot, harvest creds           │
│ 7. Reporting          → Document risk in business language       │
└─────────────────────────────────────────────────────────────────┘
```

### The Most Important Rule
**You must have written authorization before testing ANY system.**
Even testing your own employer's systems without a signed Scope of Work is illegal in many jurisdictions. Always get it in writing.

---

## Module 1.3 — Core Tooling Overview

### Objective
Know what each tool does and when to use it.

### Resources
| Resource | Type | URL | Cost |
|----------|------|-----|------|
| Burp Suite Community Edition | Tool Download | https://portswigger.net/burp/communitydownload | Free |
| Nmap Official Documentation | Docs | https://nmap.org/book/toc.html | Free |
| Metasploit Unleashed | Free Course | https://www.offsec.com/metasploit-unleashed/ | Free |
| Wireshark Learn | Official Docs | https://www.wireshark.org/docs/ | Free |

### Tool Primer

#### 🗺️ Nmap — Network Mapper
```bash
# Basic host discovery
nmap -sn 192.168.1.0/24

# Full TCP port scan
nmap -sS -p- 192.168.1.10

# Version detection + NSE scripts
nmap -sV -sC 192.168.1.10
```

#### 🔍 Burp Suite — Web Interception Proxy
- Sits between your browser and the web app
- Captures, modifies, and replays HTTP requests
- **You'll use this for almost all of Phase 2**

#### ⚡ Metasploit Framework
```bash
# Launch msfconsole
msfconsole

# Search for an exploit
search type:exploit name:vsftpd

# Use and configure an exploit
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.1.10
run
```

#### 📡 Wireshark — Packet Analyzer
- Captures raw network traffic
- Useful for seeing exactly what's going over the wire (credentials in plaintext, etc.)

#### 🔗 Netcat — The Swiss Army Knife
```bash
# Listen for a connection on port 4444
nc -lvnp 4444

# Connect to a remote host
nc 192.168.1.10 4444

# Transfer a file
nc -lvnp 9999 > received_file.txt  # Receiver
nc 192.168.1.10 9999 < file.txt    # Sender
```

---

## 🏆 Verifiable Step: Intercept HTTP with Burp Suite

**This is your Phase 1 exit exam. Do not move to Phase 2 until you have completed this.**

### Task
1. Open Burp Suite Community Edition
2. Go to **Proxy → Open Browser** (Burp's built-in Chromium)
3. Ensure "Intercept is on" is toggled ON in the Proxy tab
4. In the Burp browser, navigate to `http://example.com`
5. **Screenshot the intercepted raw HTTP GET request in Burp Suite**
6. Click "Forward" to release the request
7. Go to **Proxy → HTTP History** and verify the request appears

### 🤖 Ask Gemma to Help
```bash
ollama run gemma3
```
Prompt:
```
You are a cybersecurity instructor. I've just intercepted my first HTTP request using Burp Suite.
The raw request looks like this:

GET / HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 ...

Can you explain what each line of this HTTP request means, and what an attacker would look for
when they intercept a request like this?
```

### ✅ Completion Criteria
- [ ] Kali Linux VM is running and updated
- [ ] Burp Suite is configured and intercepting traffic
- [ ] You have captured at least one HTTP request
- [ ] You understand what each field in the raw HTTP request means

**Mark 1.1, 1.2, 1.3, and this Verifiable Step as ✅ in [PROGRESS.md](../PROGRESS.md)**

---

## 📖 Further Reading
- [Kali Linux Documentation](https://www.kali.org/docs/)
- [PTES Standard](http://www.pentest-standard.org/index.php/Main_Page)
- [NetworkChuck — Kali Linux Setup (YouTube)](https://www.youtube.com/watch?v=lZAoFs75_cs)

---

➡️ **Next Phase:** [Phase 2 — Web Application Penetration Testing](../phase-02-web-hacking/README.md)
