
> [!NOTE] Objective
> - Memorize and internalize the most important Windows Security, Sysmon, and Application Event IDs used for threat huntuing, triage, detection engineering, and incident respone
> - This cheat sheet becomes your quick look arsenal when reviewing logs or building correlatiojn rules

## Critical Windows Security Log Event IDs (Default Log: Security)

| Event ID | Meaning                             | Detection Use                    |
| -------- | ----------------------------------- | -------------------------------- |
| 4624     | Successful logon                    | Baseline + account abuse         |
| 4625     | Failed logon                        | Brute force attempts             |
| 4672     | Special privileges assigned         | High-value logon (Admin, System) |
| 4634     | Logoff                              | Correlate with 4624              |
| 4648     | Logon with explicit credentials     | runas, lateral movement          |
| 4769     | Kerberos TGT request                | Golden Ticket detection          |
| 4771     | Kerberos pre-auth failed            | Brute force or expired account   |
| 4662     | Object access (if auditing enabled) | File or AD object interaction    |
| 4697     | Service install                     | Persistence (eg. sc.exe create)  |
| 7045     | Service created (System log)        | Alternate to 4697                |
| 4702     | Scheduled task modified             | Persistebnce path                |
| 4688     | Process Creation                    | Core Execution detection         |
| 4689     | Process termination                 | Correlate lifecycle              |
| 1102     | Security log Cleared                | Anti forensics attempt           |
## Critical Sysmon Event IDs

| Event ID | Meaning                   | Detection Use                               |
| -------- | ------------------------- | ------------------------------------------- |
| 1        | Process created           | Alternative to 4688 (with better detail)    |
| 3        | Network connection        | C2 detection, lateral movement              |
| 7        | Image loaded (DLL)        | Detect DLL injection, sideloading           |
| 10       | ProcessAccess (injection) | Catch CreateRemoteThread, OpenProcess abuse |
| 11       | File created              | Detect dropped payloads                     |
| 13       | Registry set value        | Persistence or config change                |
| 19-21    | WMI events                | Fileless persistence                        |
| 22-24    | DNS queries               | C2 beaconing, malware staging               |
Sysmon has better logging fidelity than native logs. Use it for detailed detection logic.
## Applicaiton and Services Logs 
Event Viewer > Microsoft > Windows > {compopnent}

| Channel                        | Event IDs                             | Detection Use               |
| ------------------------------ | ------------------------------------- | --------------------------- |
| Windows Defender > Operational | 1116 (Detected), 1117 (Removed), 1006 | AV alerts                   |
| WMI-Activity > Operational     | 5860, 5861                            | Filesless execution via WMI |
| Powershell > Operational       | 4103 (pipeline), 4104 (script block)  | Malicious PowerShell        |
| Task Scheduler > Operational   | 106 (created), 141 (updated)          | Scheduled task persistence  |
| AppLocker                      | 8001, 8002                            | Blocked execution           |
