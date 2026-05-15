# 🤖 Gemma 4 AI Study Assistant

> Use your local Gemma 4 installation as a 24/7 private cybersecurity tutor,
> code reviewer, exploit explainer, and practice exam generator.
> Everything stays on your machine — no API keys, no internet required.

---

## Setup & Verification

```bash
# Verify your Gemma 4 installation
ollama list
# Should show: gemma3 or gemma3:latest (Gemma 4 in Ollama is tagged as gemma3)

# Quick test
ollama run gemma3 "Explain what a reverse shell is in 2 sentences."

# For extended multi-turn conversations, use the interactive mode
ollama run gemma3
```

> **Note:** Ollama's model library uses `gemma3` as the tag for Gemma models.
> If you have a custom local install, substitute your model name accordingly.

---

## Use Case 1: Lesson Explainer

Use Gemma to deepen your understanding of any concept in the curriculum.

### Prompt Templates

#### Explain a concept from scratch
```
You are an expert cybersecurity instructor. I am an experienced web developer (4+ years
with Next.js and TypeScript) who is learning ethical hacking.

Explain [CONCEPT] to me. Assume I understand:
- HTTP request/response cycle
- How web servers and databases work
- REST APIs and JSON
- JavaScript/TypeScript deeply

Structure your explanation as:
1. What it is (1-2 sentences)
2. Why it's dangerous from an attacker's perspective
3. A concrete example with code showing the vulnerable pattern
4. How I would identify it in a real pentest
```

#### Connect to developer knowledge
```
I know how to build Next.js applications. Help me understand [VULNERABILITY]
by explaining:
1. What I might have built in the past that is vulnerable to this
2. What the attack looks like from the attacker's perspective
3. How the framework (Next.js/React) either protects or exposes you to this
```

#### Explain a tool's output
```
I ran [TOOL NAME] and got this output:
[PASTE OUTPUT]

Please explain:
1. What each section means
2. What the most important/interesting findings are
3. What I should investigate next based on this output
```

---

## Use Case 2: Test Project Generator

Ask Gemma to generate intentionally vulnerable code snippets or mini-projects for you to practice attacking.

### Prompt Templates

#### Generate a vulnerable Next.js API route
```
Generate a Next.js 14 App Router API route (route.ts) that is intentionally vulnerable
to [VULNERABILITY TYPE: SQL injection / command injection / SSRF / IDOR].

Requirements:
- Use realistic-looking code that a junior developer might write
- Include comments explaining what the route is supposed to do
- Do NOT include the fix — I want to find and fix it myself
- Add a comment at the bottom: // HINT: Look for [vulnerability category]

This is for my local ethical hacking practice lab only.
```

#### Generate a vulnerable Express/Node endpoint
```
Generate a Node.js/Express.js endpoint that is intentionally vulnerable to [VULNERABILITY].
Make it look like real production code a junior dev might write.
Include a realistic API purpose (e.g., user profile fetch, file upload, search).
```

#### Generate a CTF-style challenge
```
Create a mini CTF challenge for me to practice [SKILL: privilege escalation / SQLi / XSS].

Include:
1. The setup/scenario (what I'm told as the tester)
2. The vulnerable code or configuration (as files I would find)
3. The flag format: FLAG{some_meaningful_string}
4. A hint system — 3 hints of increasing detail that I can ask for one at a time

Do NOT give me the solution. I'll ask for hints if I get stuck.
```

#### Generate a vulnerable Docker environment
```
Create a docker-compose.yml and associated files for a deliberately vulnerable
[TYPE: Node.js API / PHP web app / Python Flask app] that demonstrates [VULNERABILITY].

It should:
- Be runnable with docker-compose up
- Have a realistic purpose (e.g., blog, e-commerce, user management)
- Be vulnerable to [VULNERABILITY] in a non-obvious way

This is for my personal ethical hacking lab.
```

---

## Use Case 3: Approach Reviewer

After you attempt a challenge or lab, have Gemma review your methodology.

### Prompt Templates

#### Review your pentest methodology
```
I just completed a penetration test on a practice machine. Here's what I did:

[PASTE YOUR NOTES]

Please review my approach and tell me:
1. What I did well
2. What I missed or could have found with a different approach
3. What a senior pentester would have done differently
4. Any techniques I should add to my methodology for next time
```

#### Review your exploitation attempt
```
I'm trying to exploit [VULNERABILITY] on a practice machine.
Here's my current approach and what's happening:

Target: [description]
My approach: [what you tried]
Result: [what happened — success or error messages]

What am I missing? Guide me with a HINT only — not the full answer.
I want to figure it out myself with just enough direction.
```

#### Review your code fix
```
I found a [VULNERABILITY TYPE] in this code:
[PASTE VULNERABLE CODE]

I fixed it like this:
[PASTE YOUR FIX]

As a security expert:
1. Is my fix correct and complete?
2. Are there any edge cases my fix doesn't handle?
3. What is the industry-standard way to handle this?
4. Are there any related vulnerabilities in the surrounding code I should check?
```

---

## Use Case 4: Practice Exam & Quiz Mode

Use Gemma to test your knowledge before moving between phases.

### Prompt Templates

#### Phase knowledge quiz
```
Quiz me on [PHASE TOPIC: SQL injection / network enumeration / privilege escalation].

Rules:
- Ask me 5 questions, one at a time
- Wait for my answer before asking the next
- After each answer, tell me if I'm right and explain what I missed
- At the end, give me a score and tell me what to review

Start with question 1.
```

#### Scenario-based challenge
```
Give me a realistic penetration testing scenario and ask me what I would do next.
Wait for my response, then tell me if my approach is correct or what I missed.

Make the scenario appropriate for someone who has completed:
[PHASES COMPLETED: e.g., Phases 1-3]
```

#### Pre-assessment before moving phases
```
I'm about to move from Phase [X] to Phase [X+1] of my ethical hacking course.

Give me a 5-question verbal assessment to make sure I'm ready.
Topics should cover the key skills from Phase [X]:
[PASTE THE PHASE SKILLS]

Ask one question at a time and evaluate my answers.
```

---

## Use Case 5: Report Writing Assistant

Gemma can help you improve your professional writing in pentest reports.

### Prompt Templates

#### Improve an executive summary
```
I've written this executive summary for a penetration test report:
[PASTE SUMMARY]

Please rewrite it to be:
1. Clear to a non-technical executive (CEO/Board level)
2. Risk-focused (business impact, not technical jargon)
3. Concise (under 300 words)
4. Action-oriented in the recommendations
```

#### Write a finding description
```
I found this vulnerability during a penetration test:
- Type: [vulnerability type]
- Where: [affected component]
- How I exploited it: [brief description]

Write a professional Finding entry for my pentest report including:
- Title
- Description (2-3 sentences, technical but clear)
- Business Impact (what an attacker can actually do)
- Remediation (specific and actionable steps)

Use language appropriate for a formal security report.
```

---

## Workflow Integration Tips

### During a Lab Session
```bash
# Keep a terminal pane open for Gemma (use tmux)
tmux new-session -d -s gemma 'ollama run gemma3'
tmux new-session -d -s hacking 'bash'

# Switch between panes:
# Ctrl+B then D to detach, tmux attach -t gemma to get back
```

### Create an Alias for Quick Access
```bash
# Add to ~/.zshrc or ~/.bashrc
alias ask='ollama run gemma3'

# Usage:
ask "What does this nmap output mean: [output]"
```

### Piping Command Output to Gemma
```bash
# Get Gemma to analyze your nmap scan directly
nmap -sV 192.168.56.102 | ollama run gemma3 "Analyze this nmap output as a penetration tester. What are the most interesting findings and what should I investigate first?"
```

---

## Ethical Usage Guidelines

> [!IMPORTANT]
> Gemma 4 is your **study assistant**, not an attack tool. Keep these rules in mind:
> 1. Only generate vulnerable code for your **own local lab**
> 2. Never ask Gemma to generate malware, exploits for real systems, or social engineering content targeting real people
> 3. Gemma can hallucinate — always verify commands and CVE details against official sources (NVD, Exploit-DB)
> 4. Use Gemma to **understand** techniques, not as a black box to blindly copy-paste from

---

## Quick Reference: Best Prompts by Phase

| Phase | Best Gemma Use |
|-------|---------------|
| Phase 1 | Explain tool outputs, quiz on PTES methodology |
| Phase 2 | Generate vulnerable Next.js code, explain PortSwigger labs |
| Phase 3 | Analyze Nmap output, explain service vulnerabilities |
| Phase 4 | Explain CVEs, guide reverse shell troubleshooting |
| Phase 5 | Interpret LinPEAS output, explain PrivEsc techniques |
| Phase 6 | Review report writing, improve finding descriptions |
