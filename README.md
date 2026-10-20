# Phishing Investigation Playbook

A structured methodology for triaging and investigating phishing emails in a SOC environment.
Built from CompTIA Security+ concepts and hands-on research into real-world phishing techniques.

---

## Overview

Phishing remains one of the most common initial access vectors in security incidents.
This playbook documents a repeatable investigation process for analyzing suspicious emails,
identifying indicators of compromise (IOCs), and determining appropriate response actions.

---

## Investigation Workflow

### Step 1 — Initial Triage
- Identify the report source (user complaint, email gateway alert, SIEM alert)
- Collect the original email with full headers intact
- Do NOT click any links or open attachments on a production machine
- Assign severity: Low / Medium / High based on targeting and payload

### Step 2 — Header Analysis
Examine the full email headers for:
- **From / Reply-To mismatch** — sender display name doesn't match actual address
- **SPF / DKIM / DMARC results** — authentication failures indicate spoofing
- **Originating IP** — trace the sending server's IP against threat intel
- **X-Mailer / User-Agent** — can reveal bulk mailing tools

Key fields to check:
Received: from [IP]
Authentication-Results: spf=fail / dkim=fail / dmarc=fail
Reply-To: (different from From:)


### Step 3 — URL Analysis
- Extract all URLs from the email body
- Do NOT visit directly — use sandboxed tools:
  - [URLScan.io](https://urlscan.io) — passive scan of URL behavior
  - [VirusTotal](https://virustotal.com) — multi-engine reputation check
  - [Any.run](https://any.run) — interactive sandbox
- Look for: domain spoofing, lookalike domains, URL shorteners, redirects

### Step 4 — Attachment Analysis
- Note file type — executables, Office macros, and PDFs are high risk
- Hash the file (MD5/SHA256) and check against VirusTotal
- Submit to sandbox if hash is unknown
- Never open on a live system

### Step 5 — Indicator Extraction
Document all IOCs found:

| Type | Value | Notes |
|------|-------|-------|
| Sender IP | x.x.x.x | Failed SPF |
| Domain | evil-login[.]com | Lookalike domain |
| URL | hxxps://... | Defanged — leads to credential harvester |
| File Hash | abc123... | Malicious macro detected |

### Step 6 — Scope Assessment
- Search email gateway logs: did others receive the same email?
- Search SIEM: did anyone click the link or open the attachment?
- Identify affected users and systems

### Step 7 — Response Actions

| Severity | Action |
|----------|--------|
| Low | Document IOCs, no further action |
| Medium | Block sender/domain, notify user, preserve evidence |
| High | Block IOCs, isolate affected systems, escalate to IR team |

### Step 8 — Documentation
- Log all findings in ticketing system
- Record IOCs in threat intel platform
- Write incident summary: what was found, what was done, outcome

---

## Tools Used

| Tool | Purpose |
|------|---------|
| URLScan.io | URL reputation and behavior analysis |
| VirusTotal | File and URL multi-engine scanning |
| MXToolbox | Email header and SPF/DKIM analysis |
| Any.run | Interactive malware sandbox |
| Wireshark | Packet analysis if network traffic is available |

---

## Key Concepts

**Defanging** — Modifying IOCs so they can't be accidentally clicked:
`http://` → `hxxp://` | `evil.com` → `evil[.]com`

**SPF (Sender Policy Framework)** — DNS record listing authorized sending IPs for a domain

**DKIM (DomainKeys Identified Mail)** — Cryptographic signature verifying email integrity

**DMARC** — Policy telling receivers what to do when SPF/DKIM fail

---

*Part of the [stephen-sec](https://github.com/stephen-sec) cybersecurity portfolio.*
