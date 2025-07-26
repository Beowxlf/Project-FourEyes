|          | What It Does                  | Detection Example                                        |
| -------- | ----------------------------- | -------------------------------------------------------- |
| 1        | Process Creation              | Attacker runs `cmd.exe` or `mimikatz.exe`                |
| 3        | Network Connection            | `powershell.exe` connects to `10.10.10.10`(C2 Beaconing) |
| 11       | File Create                   | Malware drops .bat or .exe in AppData                    |
| 12/13/14 | Registry Create/Delete/Modify | Persistence via Run keys                                 |
| 7        | Image Load                    | DLL side-loading or malicious library use                |
| 10       | process Access                | Mimicry of LSASS dump behavior (used by mimikatz)        |
#### Install and Configure Sysmon
**Step 1: Download**
Grab from Sysinternals:
`https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon`
**Step 2: Install with config**
Example config (SwiftOnSecurity's is excellent):
`sysmon64.exe -accepteula -i sysmonconfig.xml`
	- TIP: Use SwiftOnSecurity sysmon config -- Great standard
**Step 3: View Logs**
Logs show up in Event Viewer under:
`Applications and Services Logs > Microsoft > Windows > Sysmon > Operational`

#### Detection Example: Cobalt Strike Beacon
- Event ID 1 
	- Suspicious `powershell.exe` with encoded command
- Event ID 3
	- Powershell initiates outbound TCP to port 4444
- Event ID 11
	- Drops a .dll or .exe in temp directory
- Event ID 10
	- Touches LSASS for token stealing
Correlation of these events = beacon+ execution + privilege escaltion
#### How Sysmon Changes Detection Game

| Without Sysmon                       | With Sysmon                                                               |
| ------------------------------------ | ------------------------------------------------------------------------- |
| Just logs login success/failure      | Logs what process was run and with what arguements                        |
| No registry tracking                 | Full registry creation/modifications visability                           |
| No file creation logging             | Can detect dropped payloads immediately                                   |
| Blind to parent-child process chains | Can detect `winword.exe -> powershel.exe` abuse (common phishing payload) |

