# Ransomware Playbook

Status: draft

---

## Purpose / Scope
Fast, decisive steps for suspected ransomware on an endpoint or server.  
This is an initial response guide — full IR may require rebuilds, legal engagement, and coordination with management.

---

## TL;DR
1. Isolate infected host(s) immediately.  
2. Identify scope — network shares, backups, or other hosts.  
3. Preserve evidence (EDR logs, file samples, ransom note).  
4. Rebuild from known-good backups and rotate credentials.

---

## Detection Cues
- Mass file renames, new file extensions, or ransom note files appear.  
- EDR alerts for file encryption or mass file modification behavior.  
- High CPU/disk IO during a short window across many files.  
- File shares suddenly inaccessible or renamed.  
- Spike in failed authentication attempts before encryption event.

---

## Triage (first 15–30 minutes)
- Assign case ID: `IR-YYYYMMDD-###`  
- Isolate affected hosts (EDR network containment or disconnect cable/Wi-Fi).  
- Identify ransom note file name, any contact email or instructions.  
- Capture volatile data quickly (process list, open network connections, memory dump).  
- Determine whether lateral spread is active or contained.  
- Note any known patient-zero or likely infection vector (email, RDP, removable media, etc.).

---

## Containment
- Isolate subnets if spread is visible (disable switch ports or VLAN).  
- Stop backup rotations to preserve clean restore points.  
- Block known C2 IPs/domains in firewall and DNS.  
- Disable compromised admin or service accounts immediately.  
- Notify leadership and legal/privacy team depending on scope.

---

## Eradication & Recovery
- Do **not** pay ransom without executive and legal review.  
- Restore only from verified, trusted backups.  
- Rebuild host from clean image; re-apply patches and hardening.  
- Rotate all credentials (domain admin first).  
- Validate restored systems before reconnecting to production network.  
- Review endpoint policies and segmentation to prevent recurrence.

---

## Hunting Queries (Examples)
**Splunk (mass file rename burst):**
```splunk
index=edr sourcetype=fs:events action=rename
| bin _time span=1m
| stats count by host, _time
| where count > 500
```

## MITRE ATT&CK Mapping (Common Techniques)
- T1486 — Data Encrypted for Impact
- T1021.002 — SMB/Windows Admin Shares (lateral movement)
- T1059 — Command and Scripting Interpreter (PowerShell, batch scripts)
- T1490 — Inhibit System Recovery (disable shadow copies)
- T1497 — Virtualization/Sandbox Evasion (some variants check for VMs)
