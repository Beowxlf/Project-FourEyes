
> [!NOTE] Objectives
> Understand what handles, tokens, and privileges are, how they affect access and execution in Windows, and how attackers use them for privilege escalation, impersonation, and persistence
## What Is a Token?
- A token is your identity and what youre allowed to do. Its like your police badge.
- When a user logs in, Windows creates a security token for them taht includesL
	- Their SID (Who they are)
	- Their gorup memberships (What they are apart of)
	- Their privileges (what theyre allowed to do)
- Processes inherit the token of the user who runs them
	- If youre a local admin, your procecesses can do admin things, if youre not you cant
## What Are Privileges?
- Privileges are superpowers embedded in tokens
- Examples:
	- SeDebugPrivilege = lets a process look into others (very dangerous)
	- SeImpersonationPrivilege = lets you become someone else
	- SeTcbPrivilege = full god-mode (trusted Computing Base)
## Deep Dive
#### Handles (Access to a Thing)
- Created via OpenProcess(), OpenFile(), etc.
- Represent access to:
	- Files 
	- Processes
	- Registry keys
	- Threads
- Used in process injections (eg. open a handle to another processes mnemory)
#### Tokens
- Created at logon or impersonation
- Include
	- User SID
	- Group SIDs
	- Privileges
	- Default DACL
- Two Types
	- Primary Token (main user/process)
	- Impersonation Token
#### Privileges (from whoami /priv)

| Privileges (from whoami /priv) | Purpose                                 | Abused by                       |
| ------------------------------ | --------------------------------------- | ------------------------------- |
| SeDebugPrivilege               | Inspect or inject into any process      | Mimikatz, remote access trojans |
| SeImpersonatePrivilege         | Impersonate another user                | Potato attacks, token theft     |
| SeAssignPrimaryTokenPrivilege  | Assign tokens to new processes          | Malware spawning child procs    |
| SeBackupPrivilege              | Read any file regardless of permissions | Data exfiltration tools         |

