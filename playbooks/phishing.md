# Phishing Playbook

status: draft

**Purpose / scope:**  
Simple steps to triage and respond to suspected phishing that may involve credential theft or malicious attachments/links. Designed for my practice lab and small-team environments.

---

## TL;DR
1. Preserve original email (.eml/.msg).  
2. Extract IOCs: sender, sender IP, URLs, attachments, hashes.  
3. Check sign-in logs for credential use.  
4. Reset password + revoke active sessions if creds may be compromised.  
5. Block sender/domain and remove from mailboxes.  
6. Follow up with user and run a short hunt for related activity.

---

## Detection cues
- User reports clicking a suspicious link or entering creds.  
- Email gateway flags message as phishing.  
- Spike of failed logins then successful login from new geo.  
- Attachment with macros or obfuscated content.

---

## Triage (first 15–30 minutes)
- Assign case ID: `IR-YYYYMMDD-###`  
- Save a copy of the original email (Download .eml).  
- Pull sign-in logs for the user (SSO/Okta/Azure AD): times, IPs, device info.  
- Extract IOCs:
  - URLs/domains
  - Sender email (full headers)
  - Attachment hash (SHA256)
- Basic severity decision:
  - **High**: creds used / lateral movement evidence
  - **Medium**: clicked but no creds / suspicious attachment
  - **Low**: looks like spam only

---

## Containment
- If credentials are likely compromised:
  - Reset password & force logout from all devices (SSO session revoke)  
  - Confirm MFA is enabled; if not, escalate
- Block domain / URL at email gateway and DNS if possible  
- Purge copies from mailboxes (search & remove by message-id)  
- Quarantine any suspicious attachments in a safe sandbox for analysis

---

## Eradication & Recovery
- If endpoint shows compromise, follow endpoint playbook (isolate, collect forensics, reimage if needed).  
- User training message: brief explanation + steps (change passwords, watch for follow-ups).  
- Monitor the user’s account for 24–72 hours (look for additional suspicious logins).

---

## Hunting queries (examples)
**Splunk (Okta anomalous success)**
```splunk
index=okta sourcetype=okta:auth outcome.result=SUCCESS
| stats count by user, client.geographicalContext.country, src_ip
| where country!="US" AND count>0
```
---

## MITRE ATT&CK mapping (common techniques)
- T1566 — Phishing (email/links)
- T1078 — Valid Accounts (credential reuse/compromise)
- T1059 — Command and Scripting (if payload runs scripts)
- T1110 — Brute Force (if credential stuffing follows leak)
