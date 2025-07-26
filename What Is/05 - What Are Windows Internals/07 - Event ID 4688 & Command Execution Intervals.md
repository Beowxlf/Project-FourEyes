>"If you see what was executed, you know what they were trying to do"
---

> [!NOTE] Objective
> - Understand how command execution is logged in Windows, espicially through EventID 4688
> - Understand how attackers hide commands using encoding, PPID spoofing, and obfuscation.
> - You'll learn how to trace execution from logon to payload.

## Explain Like I Am 12
`Think of a process like someone writing on a chalkboard. Event ID 4688 is security camera that records when someone picks up the chalk and what they tried to write - but not whether they erased it afterward.`
- Every time a command or program runs - calc.exe, powershell.exe, mimikatz.exe, curl, etc. - the system logs a 4688 event, which contains
	- Who ran it
	- What ran
	- What command-line arguments were passed
	- Which process started it (PPID)
## Deep Dive: Event ID 4688 -- Process Creation Log
#### **Fields to know in 4688**:

| Field                         | Meaning                                                        |
| ----------------------------- | -------------------------------------------------------------- |
| New Process Name              | Full path of binary executed (e.g C:\Windows\System32\cmd.exe) |
| Creator Process Name          | What launched it (parent)                                      |
| Process ID/ Parent Process ID | Track tree                                                     |
| Command Line                  | Entire String passed                                           |
| Subject User Name             | Who launched it                                                |
| Token Elevatuion Type         | Admin? UAC bypass?                                             |
| Security ID                   | Unique SID of user                                             |
| Logon ID                      | Session tracking                                               |
| Integrity Level               | Low/Medium/High/System                                         |
## Attacker Abuses to Watch for

| Tactic                          | What You'll See                                               |
| ------------------------------- | ------------------------------------------------------------- |
| Encoded PowerShell              | Long, Base64 strings in command line                          |
| Living off the Land (LOLBins)   | Legit tools used for bad actions (e.g regsvr32, mshta)        |
| Parent Spoofing (PPID Spoofing) | powershell.exe launched from explorer.exe looks innocent      |
| Missing logs                    | Logging Disabled, EDR bypassed, or auditing turned off        |
| Hidden Scripts                  | Script run from memory, encoded, or passed as -EncodedCommand |
## Enable Logging For 4688
`YOu must enable this manually unless youre in a domain with advanced GPOs:`
#### Audit Policy:
`auditpol /set /subcategory:"Process Creation" / success: enable`
- This enables 5688 logs in security log
- BONUS: Add Command Line Logging
```
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludedCmdLine_Enabled /t REG_DWORD /d 1 /f
```

