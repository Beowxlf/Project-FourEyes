#### Event ID 4625
- Failed logon
- First signal of brute force, password spraying, or misconfigured services
#### Event ID 4624
- Successful Logon
- Confirms access
- Correlate with 4672 for admin logins
#### Logon Types: The Key to Behavior Analysis

| Logon Type | Meaning                      | Common Source                | Suspicious?                  |
| ---------- |:---------------------------- | ---------------------------- | ---------------------------- |
| 2          | Interactive (local keyboard) | Console Logon                | Normal                       |
| 3          | Network                      | File shares, SMB             | Monitor                      |
| 4          | Batch                        | Scheduled tasks              | Normal                       |
| 5          | Service                      | Logon by service Accounts    | Can be abused                |
| 7          | Unlock                       | Desktop unlock               | Normal                       |
| 8          | NetworkClearText             | Password sent in clear text  | Very Risky                   |
| 9          | New Credetials               | Runas usage                  | Could be privilege           |
| 10         | RemoteInteractive<br>        | RDP logins<br>               | Critical to monitor<br>      |
| 11         | CachedInteractive            | Cached domain creds (laptop) | Normal (usually offline use) |
> - Most abused = Type 3 (SMB lateral) and Type 10 (RDP)
> - Used in brute force= Type 3 or 10, depending on the port
> - Used in scheduled tasks or malware = Type 4 or 5
### Detection Logic: Brute Force + Lateral Movement
- Scenario: RDP Brute Force > Privilege Escalation
	- Burst of 4625 (logon type 10) from same IP
	- Then followed by 4624 (Type 10)
	- Then 4672 -- Privilege user?
	- Possibly 7045 or 4688 -- attacker drops tools




