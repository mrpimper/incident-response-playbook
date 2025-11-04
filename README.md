# Incident Response Playbook (Personal Learning Project)

This repo is part of my effort to move from academic cybersecurity concepts into more hands-on, practical work.  
It’s a simple, evolving **incident response playbook** built around common scenarios like phishing, web server compromise, and ransomware - using the tools and processes I’m currently learning.

I wanted something I could reference when practicing in labs or simulating small incidents, so this is basically my personal notebook made public.

---

## Why I Made This
While finishing my M.S. in Cybersecurity, I realized I could explain IR frameworks (NIST, MITRE, etc.) but hadn’t done enough *walking through* how to handle an incident end to end.  
This project helps me connect theory → application:
- Build small labs, simulate alerts
- Document what I’d do and why
- Keep improving as I learn new tools (Splunk, ELK, PowerShell, etc.)

---

## Core IR Flow (what I try to follow)
```
Detect → Triage → Contain → Eradicate → Recover → Review
```
It’s easy to remember, and helps keep order when things feel chaotic.

---

## Common Scenarios (simplified)

### 1. Phishing

**What I’d look for:**
- Suspicious sender, spoofed domain, weird links
- Okta or O365 logins from unusual geos  
- Users saying “I clicked this email”

**Steps I’d take:**
1. Save the original email (as `.eml`).
2. Pull IOCs (domains, URLs, IPs).
3. Check if credentials were entered.
4. Reset passwords + revoke sessions if needed.
5. Block the sender/domain and purge copies from inboxes.
6. Send user follow-up / awareness message.

**Splunk example query**
```
index=okta sourcetype=okta:auth outcome.result=SUCCESS
| stats count by user, client.geographicalContext.country
| where country!="US" AND count>3
```

---

### 2. Web Server Compromise
**Indicators:**
- Sudden spike in 404/500 errors  
- Unknown PHP files in `/uploads/` or `/wp-content/`  
- Suspicious outbound traffic

**Quick actions:**
- Snapshot the server or VM before touching it.
- Check logs for first-seen POST requests with file uploads.
- Pull from load balancer and block bad IPs.
- Patch or reinstall clean.
- Re-deploy from a safe backup.

**Elastic query example**
```
url.path : "*upload*" and http.request.method : "POST"
```

---

### 3. Ransomware

Signs:
- File extensions changing fast
- CPU spikes
- New “README” or “RECOVER” text files

**My response plan:**
1. Isolate host immediately (EDR, network cable, whatever’s fastest).
2. Identify the variant and note any visible ransom details.
3. Check if backups are safe and untouched.
4. Wipe/rebuild the system — don’t try to “clean” it.
5. Restore from backups and reset all creds.
6. Log everything: timestamps, hostnames, actions, tools used.

**PowerShell snippet**
```
Get-ChildItem C:\ -Recurse -Include "*README*.txt" -ErrorAction SilentlyContinue
```

---

## Triage Checklist
- [ ] Confirm what triggered the alert (source, type)
- [ ] Identify affected assets or users
- [ ] Save evidence (logs, screenshots, hashes)
- [ ] Assign severity (Low/Med/High)
- [ ] Notify the right people (even if that’s just a teammate)
- [ ] Document all actions + time taken

---

## Lessons Learned So Far
- Logging > guessing. Without logs, you’re blind.  
- Even small steps (like resetting a password) can stop spread.  
- Write down everything — when you’re stressed, you forget details.  
- Playbooks are living docs. Every incident teaches you something new.  

---

## Tools I’m Using to Practice
- **Splunk Free / ELK Stack:** basic log search & dashboards  
- **TryHackMe + HackTheBox:** to simulate attacks  
- **PowerShell & Bash:** for quick investigation scripts  
- **Wireshark:** to analyze network traffic  
- **VirtualBox/VMWare:** local lab testing  

---

## Repo Layout
```
incident-response-playbook/
│
├── playbooks/
│   ├── phishing.md
│   ├── web-compromise.md
│   └── ransomware.md
│
├── detection/
│   ├── splunk_queries.spl
│   └── elastic_queries.kql
│
├── templates/
│   ├── triage_checklist.txt
│   ├── comms_update.txt
│   └── report_outline.md
│
└── README.md
```

---

## Next Steps (as I get better)
- Add Sigma rules and detection maps  
- Include a small Splunk dashboard export  
- Create a basic “After Action” report template  
- Map each scenario to MITRE ATT&CK techniques  
- Eventually simulate one full incident and publish the timeline

---
