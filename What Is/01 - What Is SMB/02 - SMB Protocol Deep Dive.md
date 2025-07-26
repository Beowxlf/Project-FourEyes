## Objective:
- **Learn**:
	- What SMB is and why is the backbone of file sharing on Windows
	- How authentication works (NTLM, Kerberos, etc.)
	- Which ports SMB uses (and how attackers exploit them)
	- Differences between SMB versions (1/2/3) and their security implications
	- Why SMB is abused in 90% of internal breaches

## SMB
#### What is SMB?
- SMB = Server Block Message
- Application-layer network file sharing protocol used in Windows and many enterprise networks.
#### Ports Used

| SMB Version | Ports             | Protocol |
| ----------- | ----------------- | -------- |
| SMBv1       | 137,138 (NetBIOS) | UDP      |
| SMBv1       | 139               | TCP      |
| SMBv2/3     | 445               | TCP      |
**Port 445 is now standard. Block it at the firewall unless you want ransomware knocking**
#### SMB Versions and Security

| Version | Notes                                   | Security Status     |
| ------- | --------------------------------------- | ------------------- |
| SMBv1   | Legacy (Windows XP Era)                 | DISABLE IMMEDIATLEY |
| SMBv2   | Major Performance Boost                 | Better, but dated   |
| SMBv3   | Adds encryption, integrity, compression | Modern and Secure   |
- Use PowerShell to check: `Get-SmbServerConfiguration`
- Disable SMBv1: `Set-SmbServerConfiguration -EnableSMBProtocol $false`
#### Authentication with SMB

| Type          | Used By                   | Weakness                           |
| ------------- | ------------------------- | ---------------------------------- |
| NTLM          | Workgroups, older domains | Susceptible to relay/hash theft    |
| Kerberos      | Active Directory          | Stronger but not perfect           |
| Null Sessions | Legacy/guest Systems      | Full access without authentication |
**Most lateral movement starts with NTLM relay or pass-the-hash over SMB**
#### Real-World Examples

| Use Case           | Description                     |
| ------------------ | ------------------------------- |
| File Server Access | \fileshare\public               |
| Print Server       | \print-srv\laserjet1            |
| Log Collection     | SIEM pulls logs via SMB         |
| Malware Movement   | Ransomeware via SMB shares      |
| Red Team Tactic    | Mount Admin share (C$) remotely |
#### Blue Team Use Cases

| Threat           | Detection                                         |
| ---------------- | ------------------------------------------------- |
| Lateral Movement | Audit Access to `ADMIN$`, `C$`, `IPC$`            |
| NTLM Relay       | Alert on SMB requests from unknown internal hosts |
| Guest Logins     | Log and block null sessions                       |
| Ransomeware      | Monitor Large File modifications over SMB         |
#### Common Commands
- Access a share: `net use Z:\\server\share /user:domain\username`
- List SMB sessions: `Get-SmbSession`
- Log SMB usage: `wevtutil qe Security "/q:*[System[(EventID=5140)]]" /f:text`