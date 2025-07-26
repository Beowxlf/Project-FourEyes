#### SMB Protocol - Core Behavior and Security Relevance
**Date:** #2025-07-03
	What I Learned:
	- SMB enables file and resource sharing using TCP 445 (modern)
	- Versions matter: SMBv1 is insecure; SMBv3 has encryption
	- Authentication methods include NTLM (weak) and Kerberos (prefered)
	- It's a common vector for ransomware and Red Team lateral movement
	- Logs like 5140 and commands like `Get-SmbSession` are used for monitoring

---
