## Deep Dive
#### Event ID 5140 -- Shared File Access
- Logged When:
	- A file or folder is accessed over the network
	- Usually triggered by SMB (Logon Type 3)
- Key Fields:
	- Share Name (\\server\C$, \server\HRDocs)
	- Client Address
	- Account Name
- Why it matters:
	- Indicates lateral movement
- Used by:
	- Internal recon
	- Manual data theft
	- Tool staging (eg. Cobalt strike payloads)
#### Common Attack Indicators

| Pattern                                | meaning                             |
| -------------------------------------- | ----------------------------------- |
| User accesses `\\C$`                   | Admin share access - Escalate alert |
| Share accessed from new host           | Lateral movement attempt            |
| Multiple shares accessed in short time | Recon or ransomware staging         |
#### Event ID 4662 -- Sensitive Object Access
- Logged when:
	- A user tries to read, write or modify AD objects like:
		- Users
		- Groups
		- Organizational Units
		- Domain Controllers
- This is how attackers:
	- Discover users and privileges
	- Run DCSync attacks (steal password hashes)
	- Create backdoor accounts
## Detection Example: DCSync
- DCSync is when a compromised account tricks a domain controller into sending password hashes
- Indicators include:
	- 4662 trigger with
		- Access mask: 0x100 (replication)
		- Object type: User
	- Often preceded by 4673 (privileged service usage)
#### Detection Summary

| Event | Suspicious pattern                    | Response                      |
| ----- | ------------------------------------- | ----------------------------- |
| 5140  | Access to `\\C$`, `\\Admin$`          | Lateral admin movement        |
| 5140  | Share access from unusual IP          | Flag + correlate with 4624    |
| 4662  | Object access with replication rights | Possible DCSync               |
| 4662  | Triggered by non DC account           | Escalation or privilege abuse |
