# Phase 2: Web Application Penetration Testing

> **Your Superpower.** You've built Next.js apps — now you'll learn to break them.

---

## 🎓 Phase 2 Core Resources — Watch / Study Before Starting

### 🆓 Free Full Courses
| Course | Platform | URL | Notes |
|--------|----------|-----|-------|
| **Web Security Academy** | PortSwigger (official) | https://portswigger.net/web-security | 280+ free interactive labs — your main training ground |
| **OWASP Top 10 2021** | OWASP (free) | https://owasp.org/Top10/ | Official reference for all vulnerability categories |
| **Web Application Pen Testing for Beginners** | freeCodeCamp / YouTube | https://www.youtube.com/watch?v=gTpkGgJ9G9g | 2.5 hr course covering Nmap, Burp, Nikto |
| **Hacker101** | HackerOne (free) | https://www.hacker101.com/ | Free video course, maps to bug bounty skills |

### 📺 Key YouTube Channels for This Phase
| Channel | Best For | URL |
|---------|----------|-----|
| **Rana Khalil** | Best PortSwigger lab walkthroughs, deep technique explanations | https://www.youtube.com/@RanaKhalil101 |
| **HackerSploit** | OWASP Top 10 practicals, Burp Suite workflow | https://www.youtube.com/@HackerSploit |
| **John Hammond** | XSS, JWT, API hacking in CTF and real contexts | https://www.youtube.com/@_JohnHammond |
| **TCM Security** | Web pentesting methodology, BOLA, IDOR | https://www.youtube.com/@TCMSecurityAcademy |

---

## Primary Practice Platform

| Platform | URL | Cost |
|----------|-----|------|
| **PortSwigger Web Security Academy** | https://portswigger.net/web-security | **100% Free** |
| DVWA (Damn Vulnerable Web App) | https://github.com/digininja/DVWA | Free |
| OWASP WebGoat | https://owasp.org/www-project-webgoat/ | Free |

> Create a free account at PortSwigger to track your lab completion.

---

## Module 2.1 — Injection Attacks (SQLi, NoSQLi, Command Injection)

### Resources
| Resource | URL |
|----------|-----|
| PortSwigger: SQL Injection | https://portswigger.net/web-security/sql-injection |
| PortSwigger: NoSQL Injection | https://portswigger.net/web-security/nosql-injection |
| PortSwigger: Command Injection | https://portswigger.net/web-security/os-command-injection |
| PayloadsAllTheThings (SQLi) | https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [SQL Injection for Beginners](https://www.youtube.com/watch?v=1nJgupaUPEQ) | Rana Khalil | Deep, methodical explanation of every SQLi type |
| [SQL Injection — PortSwigger Lab 1 Walkthrough](https://www.youtube.com/watch?v=X1X1UdaC_90) | Rana Khalil | See the lab workflow before attempting it yourself |
| [NoSQL Injection Tutorial](https://www.youtube.com/watch?v=whuIf33v2Ug) | HackerSploit | MongoDB NoSQL injection — directly relevant to your stack |
| [Command Injection Explained](https://www.youtube.com/watch?v=27Agc6Xg2HE) | John Hammond | Real-world OS command injection from a CTF context |

### Key Concept: SQL Injection
```javascript
// BAD — Never do this in Next.js
const query = `SELECT * FROM users WHERE username = '${req.body.username}'`

// Attack payload: ' OR '1'='1
// Result: SELECT * FROM users WHERE username = '' OR '1'='1'
// → Returns ALL users
```

### Key Concept: NoSQL Injection (Relevant for MongoDB/Next.js)
```javascript
// Vulnerable Mongoose query
User.findOne({ username: req.body.username, password: req.body.password })

// Attack JSON payload:
// { "username": "admin", "password": { "$gt": "" } }
// → $gt (greater than) matches ANY non-empty password
```

### Key Concept: Command Injection
```javascript
// NEVER pass user input to exec() in Next.js API routes
import { exec } from 'child_process'
exec(`ping ${req.query.host}`, callback)
// Attack: ?host=127.0.0.1; cat /etc/passwd
```

### 🔬 Labs (PortSwigger — Do These In Order)
1. [ ] [SQLi – retrieve hidden data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)
2. [ ] [SQLi – login bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)
3. [ ] [SQLi – UNION attack, determine number of columns](https://portswigger.net/web-security/sql-injection/lab-determine-number-of-columns)
4. [ ] [NoSQLi – bypass authentication](https://portswigger.net/web-security/nosql-injection/lab-nosql-injection-bypass-authentication)
5. [ ] [OS command injection – simple case](https://portswigger.net/web-security/os-command-injection/lab-simple)

---

## Module 2.2 — Client-Side Attacks: XSS, DOM XSS, CSRF

### Resources
| Resource | URL |
|----------|-----|
| PortSwigger: XSS | https://portswigger.net/web-security/cross-site-scripting |
| PortSwigger: CSRF | https://portswigger.net/web-security/csrf |
| XSS Cheat Sheet | https://portswigger.net/web-security/cross-site-scripting/cheat-sheet |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [XSS (Cross-Site Scripting) Explained](https://www.youtube.com/watch?v=EoaDgUgS6QA) | Rana Khalil | Complete XSS breakdown — reflected, stored, DOM |
| [DOM XSS Explained](https://www.youtube.com/watch?v=26KPWZ_HmE4) | HackerSploit | DOM-based XSS with live examples |
| [CSRF Attack Explained](https://www.youtube.com/watch?v=eWEgUcHPle0) | HackerSploit | How CSRF tokens work and when they fail |

### Key Concept: How Attackers Bypass React's XSS Protection
```jsx
// React IS safe:
<div>{userInput}</div>           // ✅ Auto-escaped

// React is NOT safe in these patterns:
<div dangerouslySetInnerHTML={{ __html: userInput }} />  // ❌ Direct injection
<a href={userInput}>Click</a>   // ❌ javascript: URI bypass
```

### DOM-Based XSS
```javascript
// URL hash read without sanitization
document.getElementById('output').innerHTML = location.hash.slice(1)
// Attack URL: https://victim.com/page#<img src=x onerror=alert(1)>
```

### 🔬 Labs (PortSwigger)
1. [ ] [Reflected XSS – nothing encoded](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)
2. [ ] [Stored XSS – nothing encoded](https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)
3. [ ] [DOM XSS – document.write sink](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink)
4. [ ] [Reflected XSS – JS string context](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-javascript-string-single-quote-backslash-escaped)
5. [ ] [CSRF – no defenses](https://portswigger.net/web-security/csrf/lab-no-defenses)

---

## Module 2.3 — Server-Side Attacks: SSRF & Path Traversal

### Resources
| Resource | URL |
|----------|-----|
| PortSwigger: SSRF | https://portswigger.net/web-security/ssrf |
| PortSwigger: Path Traversal | https://portswigger.net/web-security/file-path-traversal |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [SSRF Explained](https://www.youtube.com/watch?v=ih5R_c16bKc) | Rana Khalil | Full SSRF concept, bypass techniques, AWS meta-data attacks |
| [File Path Traversal Explained](https://www.youtube.com/watch?v=OECe8FWHmUc) | Rana Khalil | Directory traversal — clear walk of every bypass variant |

### Key Concept: SSRF in Next.js API Routes
```javascript
// Vulnerable Next.js API route — URL from user input
export async function GET(req) {
  const url = req.nextUrl.searchParams.get('url')
  const response = await fetch(url)  // User controls this!
  return Response.json(await response.json())
}
// Attack: /api/fetch?url=http://169.254.169.254/latest/meta-data/iam/
// → Reaches AWS metadata service → steals IAM credentials
```

### 🔬 Labs (PortSwigger)
1. [ ] [Basic SSRF against local server](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost)
2. [ ] [SSRF with blacklist-based input filter](https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter)
3. [ ] [Path traversal – simple case](https://portswigger.net/web-security/file-path-traversal/lab-simple-case)

---

## Module 2.4 — Auth & Authorization: JWTs, IDOR, OAuth

### Resources
| Resource | URL |
|----------|-----|
| PortSwigger: JWT Attacks | https://portswigger.net/web-security/jwt |
| PortSwigger: IDOR | https://portswigger.net/web-security/access-control/idor |
| JWT.io Debugger | https://jwt.io |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [JWT Security Explained](https://www.youtube.com/watch?v=7Q17ubqLfaM) | Rana Khalil | The alg:none, RS256→HS256, and kid injection attacks in one video |
| [IDOR Explained for Beginners](https://www.youtube.com/watch?v=MKS-SCtVGiA) | TCM Security | IDOR/BOLA — most common real-world bug bounty vulnerability |
| [OAuth 2.0 Attack Explained](https://www.youtube.com/watch?v=ZDuX-obcTdU) | Rana Khalil | OAuth misconfigurations and token theft |

### Key Concept: JWT Algorithm Confusion
```
Attack 1: alg:none — Some servers skip signature verification
Attack 2: RS256→HS256 — Sign with the public key as HMAC secret
Attack 3: kid injection — "kid": "../../dev/null" to forge any token
```

### 🔬 Labs (PortSwigger)
1. [ ] [JWT bypass – unverified signature](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature)
2. [ ] [JWT bypass – flawed signature verification](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-flawed-signature-verification)
3. [ ] [JWT bypass – algorithm confusion](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-algorithm-confusion)
4. [ ] [IDOR – insecure direct object references](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)

---

## Module 2.5 — API Hacking: REST & GraphQL

### Resources
| Resource | URL |
|----------|-----|
| PortSwigger: API Testing | https://portswigger.net/web-security/api-testing |
| OWASP API Security Top 10 | https://owasp.org/www-project-api-security/ |
| InQL Burp Extension | https://portswigger.net/bappstore/296e9a0730384be4855c0a2bfa9a74fc |

### 📺 Watch These
| Video | Channel | Why Watch |
|-------|---------|----------|
| [API Penetration Testing - Full Course](https://www.youtube.com/watch?v=CkVvB5aQLFg) | TCM Security | Comprehensive REST and GraphQL API hacking, uses real targets |
| [GraphQL Hacking — Introspection and More](https://www.youtube.com/watch?v=NPDp7GHmMa4) | HackerSploit | Practical GraphQL exploitation and introspection abuse |

### Key Concept: GraphQL Introspection
```graphql
# Dumps the entire schema — all queries, mutations, and fields
{ __schema { types { name fields { name } } } }
```

### 🔬 Labs (PortSwigger)
1. [ ] [Exploiting API endpoint using documentation](https://portswigger.net/web-security/api-testing/lab-exploiting-api-endpoint-using-documentation)
2. [ ] [Accessing private GraphQL posts](https://portswigger.net/web-security/graphql/lab-graphql-reading-private-posts)

---

## 🏆 Verifiable Step: Phase 2 Exit Exam

**Complete ALL before moving to Phase 3:**

### A — Minimum Labs
- [ ] At least 8 labs from the lists above (1 SQLi, 1 XSS, 1 SSRF, 1 JWT minimum)

### B — Developer Post-Mortem (3 vulnerabilities)
Create notes in `./lab-notes/` for 3 exploited vulnerabilities. Answer:
1. What was the vulnerable code pattern?
2. How does the attack work?
3. How would you fix it as a developer?

### C — DVWA Local Practice
```bash
# Run DVWA via Docker
docker run -d -p 8080:80 vulnerables/web-dvwa
# Access: http://localhost:8080 | Creds: admin / password
# Practice: SQL Injection, XSS (Reflected), Command Injection
```

### 🤖 Gemma Review Prompt
```bash
ollama run gemma3
# Prompt:
# "Review my post-mortem note for [vulnerability name]. Check:
# 1. Technical accuracy, 2. Anything I missed, 3. Is my fix correct?"
```

**Mark all Phase 2 tasks ✅ in [PROGRESS.md](../PROGRESS.md)**

---

➡️ **Next Phase:** [Phase 3 — Network Reconnaissance](../phase-03-network-recon/README.md)
