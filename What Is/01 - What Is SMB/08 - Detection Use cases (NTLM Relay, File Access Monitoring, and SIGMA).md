
> [!NOTE] Objective
> - How attackers exploit misconfigured SMB + NTFS
> - What NTLM relay looks like on wire + logs
> - What Event IDs matter for **file access detection**
> - How to write or tune **SIGMA rules** to detect suspicious behavior
> - How to use this knowledge in a SIEM or EDR context
## Deep Dive: SMB Threats to Watch
#### NTLM Relay Attacks
- Attacker acptures NTLM hash (via LLMNR/Responder)
- Relays it to target SMB server before it expires
- Authenticates as the user **without cracking the password**
Exploits port 445 and misconfigured SMB shares or services
#### Over-permissive Shares
- "Everyone" or "Authenticated Users" have **Write/Modify**
- Red team drops Cobalt Strike payload, runs from mapped share
- Or exfiltrates docs via Z:\ mapped drive
#### Null Sessions & Hidden Shares
- Hidden shares like `C$`, `ADMIN$`, `IPC$` exposed
- Null Session = access without auth (old networks)
- Allows enumeration of users, groups, shares, silently
## Key Windows Event IDs for Detection

| Event ID | Meaning                             | Relevance                   |
| -------- | ----------------------------------- | --------------------------- |
| 5140     | A network share was accessed        | Core SMB usage              |
| 5145     | Detailed file share access          | Use for object-access       |
| 4624     | Successful logon                    | Track lateral auth attempts |
| 4648     | Logon with explicit creds           | Used in NTLM relay          |
| 4656     | Handle requested to objecft         | Showsaccess attempt         |
| 4663     | Object accessed (read/write/delete) | File activity logging       |
| 4660     | Object deleted                      | File deletion               |
### Use Case 1: Suspicious Access to Hidden Shares
**Logic:** Access to \\hostname\C$ or ADMIN$
**Detection:** Alert when non-admin users triggers 5140 logs for those shares
### Use Case 2: NTLM Relay Behavior
**Logic:** Rapid 4624 +4648 from the same source
**Detection:** Correlate logon success with explicit credential usage from unexpected hosts
### Use Case 3: Mass File Deletion or Exfil
**Logic:** Repeated 4660 (delete) or 4663 (read) across share
**Detection:** SIGMA rule for high-volume access in short time window

## SIGMA Rule Sample (Access to Hidden Shares)
```
title: NTLM Relay Behavior
logsources:
	product: windows
	service: security
Detections:
	selection1:
		EventID: 4624
		LogonType: 3
	selection2:
		EventID: 4648
	timeframe: 1s
	condition: selection1 followedby selection2
level: high
```
### Red Team Tools that Abuse SMB/NTFS

| Tool                     | Behavior                          |
| ------------------------ | --------------------------------- |
| Responder                | Captures hashes via LLMNR/NetBIOS |
| Impacket (ntlmrelayx.py) | Relays NTLM hashes                |
| CrackMapExec             | Enumerates shares, drops payloads |
| Cobalt Strike            | Runs from SMB-based stagers       |
| SharepShares/PowerView   | Finds writable shares and ACLs    |
