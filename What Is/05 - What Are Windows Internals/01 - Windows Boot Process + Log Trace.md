
> [!NOTE] Objective
> - Understand Windows Boot Process
> - Identify where it can be tampered with
> - Learn how to trace boot-related logs in event viewer and command line
> - Being able to describe each stage of boot
> - Detect persistence mechanisms tied to boot (bootkits, autostarts, GPOs)
> - Use bcdedit, msconfig, and logs to trace and control boot behavior.
### Explain Like I Am 12
- Power is pressed > Castle doors swing open (Power-on Self-Test = POST)
- BIOS/UEFI > The guard captain checks all rooms (CPU, RAM, boot disk)
- Bootloader runs > He finds a scroll (Boot Configuration Data = BCD) that says what to do
- Windows Kernal Loads > Knights (drivers) and servants (services) line up to work
- Session Manager starts > Royal court begins operations (user logon begins)
- Winlogon & Explorer > You're inside the castle with a full team awake and working
### Deep Dive: Boot Process Breakdown

| Stage                         | Details                   | Attacker Abuse                                         |
| ----------------------------- | ------------------------- | ------------------------------------------------------ |
| BIOS/UEFI                     | Initializes Hardware      | UEFI rootkits / firmware implants                      |
| MBR or EFI Bootloader         | loads bootmgr             | MBR bootkits                                           |
| BCD (Boot Configuration Data) | Points to Windows Loader  | Tampering with bcdedit (eg disabling integrity checks) |
| Windows Loader                | Loads kernal and drivers  | Signed driver abuse                                    |
| NTOSKRNL (kernal)             | Initializes core services | Kernal Rootkits                                        |
| Session Manager (smss.exe)    | Starts CSRSS, Winint      | Malware can replace key binaries                       |
| Winlogon + Explorer           | User logon and shell      | Persistence via run keys, shell hijack                 |
## bcdedit - View and Edit Boot Loader Entries
- `bcdedit /enum`
	- Shows Boot entries. Look for:
		- path > should point to \Windows\system32\winload.exe
		- testsigning or nointegritychecks
			- If ON, something phishy
		- Disable integrity checks (for malware, or attackers):
			`bcdedit /set nointegritychecks on`
- msconfig - GUI Tool for Boot Options
	- Boot tab > See active OS boot path
	- Startup tab > View legacy startup programs
	- Use this to spot boot-time programs or enable Safe Boot for malware cleanup.
## Event Logs
Where to watch for Boot behavior
- Open Event Viewer (eventvwr.msc) > Windows Logs > System
Filter on these Event IDs:

| ID   | Source         | Description                             |
| ---- | -------------- | --------------------------------------- |
| 12   | Kernel-General | System is booting up                    |
| 13   | Kernel-General | Time change (can indicate tampering)    |
| 6005 | EventLog       | Event log service started (boot marker) |
| 6006 | EventLog       | Event log stopped (shutdown marker)     |
| 41   | Kernel-Power   | Improper shutdown or forced reboot      |
Use these to track uptime, crashes, or abnormal reboots
- Also check:
	- Microsoft > Windows > Diagnostics-Performace > Operational
	- Event ID 100 = boost performance
	- Event 200-299 = shutdown times
