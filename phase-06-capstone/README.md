# Phase 6: Capstone Project — The Full Engagement

> **This is your graduation exam.**
> Execute a complete black-box penetration test and deliver a professional report.
> No tutorials. No hand-holding. This is what real engagements feel like.

---

## 🎓 Phase 6 Core Resources — Study Before Your Capstone

### 🆓 Free Full Courses
| Course | Platform | URL | Notes |
|--------|----------|-----|-------|
| **Jr Penetration Tester Path** | TryHackMe | https://tryhackme.com/path/outline/jrpenetrationtester | Complete this path if not already — bridges everything |
| **Pentest+ Study Guide** | Professor Messer (Free) | https://www.professormesser.com | Free video series for report methodology and scoping |

### 📺 Key YouTube Resources for This Phase
| Video | Channel | Why Watch |
|-------|---------|----------|
| [How to Write a Pentest Report](https://www.youtube.com/watch?v=EOoBAXeABfg) | TCM Security | The industry standard for report writing \u2014 watch before writing your own |
| [Hack The Box Starting Point Tier 2 — Archetype](https://www.youtube.com/watch?v=UGkFE-Nq4HM) | IppSec | Full walkthrough of an HTB machine with web + PrivEsc chain |
| [Mr. Robot Walkthrough \u2014 VulnHub](https://www.youtube.com/watch?v=t6RuSq1_bM4) | HackerSploit | Walthrough of the Mr. Robot VM \u2014 your Option B capstone target |
| [How to document your hacking \u2014 note taking tips](https://www.youtube.com/watch?v=MQGozZzHUwQ) | TCM Security | Note-taking during an engagement \u2014 how to capture everything you need |
| [Bug Bounty vs Pentest Report Writing](https://www.youtube.com/watch?v=Z3AuHuMrMWk) | Rana Khalil | How reports differ between bug bounty and professional pentesting |

---

## Objective

Perform a complete penetration test against a realistic target that requires:
1. Reconnaissance & enumeration
2. Web application exploitation to gain initial access
3. Privilege escalation to root/SYSTEM
4. Documentation of the full attack chain in a professional report

---

## Target Selection

Choose **one** of the following based on your readiness:

### Option A — Guided HTB Machine (Beginner)
| Machine | HTB Platform | Notes |
|---------|-------------|-------|
| Archetype | https://app.hackthebox.com/starting-point | Windows, Tier 2 |
| Oopsie | https://app.hackthebox.com/starting-point | Linux, web + PrivEsc |
| Vaccine | https://app.hackthebox.com/starting-point | Linux, SQLi + sudo |

### Option B — Community VulnHub VMs (Intermediate)
| Machine | Download | Difficulty |
|---------|----------|-----------|
| DC-1 | https://www.vulnhub.com/entry/dc-1,292/ | Medium |
| Kioptrix Level 3 | https://www.vulnhub.com/entry/kioptrix-level-12-3,24/ | Medium |
| Mr. Robot | https://www.vulnhub.com/entry/mr-robot-1,151/ | Medium-Hard |

### Option C — Build Your Own Lab (Advanced)
```bash
# Set up a deliberately vulnerable web app using Docker:
# Next.js with intentional vulnerabilities (mimics your dev background)

docker run -d -p 3000:3000 --name dvna appsecco/dvna:sqlite
# Access at http://localhost:3000
# Damn Vulnerable Node Application — Node.js/Express based
```

---

## Phase A — Reconnaissance & Enumeration

### Checklist
- [ ] Find the target IP
- [ ] Run a full Nmap scan (all ports, service versions, default scripts)
- [ ] Enumerate all discovered services
- [ ] Run directory brute-forcing on any web services
- [ ] Identify the technology stack
- [ ] Document all findings in your report

### Commands Template
```bash
# 1. Host Discovery
nmap -sn 192.168.56.0/24

# 2. Full Port Scan
nmap -sV -sC -p- -oA capstone_scan [TARGET_IP]

# 3. Web Enumeration
whatweb http://[TARGET_IP]
ffuf -u http://[TARGET_IP]/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -o web_enum.json
nikto -h http://[TARGET_IP]

# 4. Specific Service Enumeration
# (based on what nmap finds)
```

### Notes Template
Create `./phase-06-capstone/notes/01-recon.md`:
```markdown
# Recon Notes — [Target Name]

## Date: 
## Tester: [Your Name]

## Open Ports
| Port | Service | Version | Notes |
|------|---------|---------|-------|
| | | | |

## Web Technology Stack
- Framework:
- Server:
- Database (if identified):
- Interesting directories found:

## Interesting Findings
-
-
```

---

## Phase B — Vulnerability Analysis & Exploitation

### Checklist
- [ ] Identify the most likely vulnerability from your recon
- [ ] Research the CVE or attack type
- [ ] Document your hypothesis before attempting
- [ ] Execute the exploit
- [ ] Capture proof of initial access (screenshot of `whoami`)

### Methodology
```
Enumerate → Research → Hypothesize → Test → Document

Never fire exploits randomly. Every action should be intentional.
```

### Notes Template
Create `./phase-06-capstone/notes/02-exploitation.md`:
```markdown
# Exploitation Notes

## Vulnerability Identified
- CVE (if applicable):
- Attack Type (SQLi / RCE / LFI / ...):
- Affected Component:

## Research
- Source: (Exploit-DB, searchsploit, GitHub)
- Exploit details:

## Steps to Reproduce
1.
2.
3.

## Proof of Access
[Screenshot / command output]
- User: 
- Hostname:
```

---

## Phase C — Privilege Escalation

### Checklist
- [ ] Run LinPEAS/WinPEAS immediately after gaining a shell
- [ ] Review output for highlighted findings (red = critical)
- [ ] Select and execute the PrivEsc path
- [ ] Capture root/SYSTEM proof
- [ ] Find any sensitive data (flags, credentials, secrets)

### Root Proof Template
```bash
# Always capture this as your final proof:
echo "==== ROOT PROOF ====" && whoami && id && hostname && date && cat /root/root.txt
```

### Notes Template
Create `./phase-06-capstone/notes/03-privesc.md`:
```markdown
# Privilege Escalation Notes

## Starting User
-

## LinPEAS Key Findings
(paste relevant sections)

## PrivEsc Vector Used
- Technique:
- Why it worked:

## Root Proof
[Screenshot]
- Command output:
```

---

## Phase D — The Professional Report

> **This is the most important deliverable of the entire course.**
> A pentester who can hack but can't communicate risk in writing is unemployable.

### Report Template
Your final report goes in `./phase-06-capstone/report/pentest-report.md`

See the detailed template: [Pentest Report Template](./report/pentest-report-template.md)

### Report Sections (Required)

#### 1. Cover Page
```
PENETRATION TEST REPORT
Target: [Name]
Test Date: [Date]
Report Date: [Date]
Classification: CONFIDENTIAL
Prepared by: [Your Name]
```

#### 2. Executive Summary (1 page, non-technical)
- Overall risk rating (Critical / High / Medium / Low)
- Number of findings by severity
- Most critical issue in plain English
- Top 3 recommendations

#### 3. Scope & Methodology
- What was in scope (IP, URLs)
- Type of test (black-box)
- Methodology used (PTES)
- Tools used

#### 4. Technical Findings

For each vulnerability, document:
```markdown
## Finding 1: [Vulnerability Name]
**Severity:** Critical | High | Medium | Low
**CVSS Score:** X.X (calculate at https://www.first.org/cvss/calculator/3.1)
**CVE (if applicable):** CVE-XXXX-XXXXX

### Description
What the vulnerability is.

### Proof of Concept
Step-by-step reproduction:
1.
2.
3.

[Screenshot of exploitation]

### Business Impact
What an attacker could do with this access.

### Remediation
Specific code/config change to fix it.
**Priority:** Immediate / Short-term / Long-term
```

#### 5. Remediation Summary Table
| # | Finding | Severity | Remediation | Priority |
|---|---------|----------|-------------|----------|
| 1 | | | | |

---

## Pentest Report Template (Full)

See [./report/pentest-report-template.md](./report/pentest-report-template.md)

---

## 🏆 Capstone Completion Criteria

You have successfully completed this course when:

- [ ] You have compromised the target machine (have root/SYSTEM access)
- [ ] You have written notes for all 3 phases (recon, exploitation, privesc)
- [ ] Your final report includes:
  - [ ] Executive Summary
  - [ ] At least 2 documented findings with CVSS scores
  - [ ] Proof-of-concept steps that are reproducible
  - [ ] Specific remediation recommendations
  - [ ] Remediation table

### 🤖 Have Gemma Review Your Report
```bash
ollama run gemma3
# Prompt:
# "You are a senior penetration tester reviewing a junior tester's first pentest report.
# Here is the executive summary and one finding:
# [paste content]
# Please review for:
# 1. Clarity — would a non-technical executive understand the risk?
# 2. Completeness — is anything missing from the finding?
# 3. Accuracy — is the CVSS justification correct?
# 4. Remediation — is my fix actionable and specific enough?
# Provide constructive feedback."
```

---

## 🎓 What Comes Next

After completing this capstone, you are ready for:

| Path | Resource |
|------|---------|
| Bug Bounty Hunting | https://www.hackerone.com / https://bugcrowd.com |
| CEH Certification | https://www.eccouncil.org/programs/certified-ethical-hacker-ceh/ |
| OSCP (Industry Gold Standard) | https://www.offsec.com/courses/pen-200/ |
| eJPT (Good first cert) | https://elearnsecurity.com/product/ejpt-certification/ |
| CTF Competition | https://ctftime.org |

---

**Mark Capstone as ✅ in [PROGRESS.md](../PROGRESS.md)**

**🎉 Congratulations — you are an ethical hacker.**
