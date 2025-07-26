>"A Program that runs quietly, every reboot, without detection... that's how you stay in the system"


> [!NOTE] Objective
> - Master the Scheduled Task System and Registry Based autostarts in Windows.
> - Learn how attackers use these for persistence without privilege escalation, and how to detect them with tools and log analysis.
## Explain Like I Am 12
- Windows has a chort chart (Task Schedular) and note-to-self sticky notes (registry keys)
- Scheduled Tasks:
	- Run this app every time I wake up
	- Check this script every morning at 3AM
- Registry Autostarts:
	- Remember to run this every time I log in
- Attackers love these. Why? Because they are legit, invisible to many AV tools, and dont need admin rights in some cases
## Deep Dive
#### Scheduled Tasks
- Manage via Task Scheduler (taskschd.msc) or schtasks.exe
- Tasks can trigger on:
	- Boot
	- Login
	- Idle
	- Time Interval
- Each task has:
	- Name
	- Trigger
	- Action (what it runs)
	- User Context
- Attacker abuse

| Vector                | Use Case                                        |
| --------------------- | ----------------------------------------------- |
| Time Based            | Run Malware every 5 inutes                      |
| On-Logon              | Trigger keylogger or downloader silently        |
| Hijack existing Task  | Replace command path of a legit task            |
| Vreate via PowerShell | Use `Register-ScheduledTask` to stay persistent |
#### Registry Autostarts Keys

| Key                                                             | Purpose                         |
| --------------------------------------------------------------- | ------------------------------- |
| HKCU\Software\Microsoft\Windows\CurrentVersion\Run              | Autostart on current user login |
| HKLM\Software\Microsoft\Windows\CurrentVersion\Run              | Autostart fro all users         |
| HKCU\Software\Microsoft\WindowsNT\CurrentVersion\Winlogon\Shell | Replace user shell (dangerous)  |
| HKLM\SYSTEM\CurrentControlSet\Services                          | Autoload services (covered bf)  |
| HKCU\Enviroment\UserInitMprLogonScript                          | Logon scripts                   |
#### High Risk Techniques

| Technique                           | Description                                         |
| ----------------------------------- | --------------------------------------------------- |
| Logon Persistence (HKCU\Run)        | Low-priv malware can auto-execute on login          |
| Task Scheduler Abuse                | Silent scheduled PowerShell tasks                   |
| Registry Shell Hijack               | Replace Explorer.exe with malware                   |
| Image File Execution Options (IFEO) | Debugging hijack (eg. launching calc opens malware) |
#### Tools

| Tool                        | Use                                                |
| --------------------------- | -------------------------------------------------- |
| taskchd.msc                 | GUI to view all scheduled tasks                    |
| schtasks /query /fo LIST /v | CLI enumeration                                    |
| reg query                   | CLI to inpsect registy autostart keys              |
| Autoruns                    | GUI overview of all startup methods                |
| Event Viewer                | Event ID 4702 (Task modified), 4698 (task created) |
## Lab -- Hunt and Simualte Persistence
- Task scheduler GUI
	- Open task scheduler
	- Browse
		- `Task Scheduler > Library > Microsoft > Windows`
	- Looking for
		- Weird names
		- Triggers like "at logon" or "every 5 minutes"
		- Actions with suspicoious paths (C:\Users\...\AppData\local\...)
		- User context (SYSTEM, unexpected users)
	- Record Suspicious tasks
- schtasks CLI
	- `schtasks /query /fo LIST /v`
	- Looking for:
		- TaskName
		- Task To Run
		- Start In
		- Run As User
	- Example to view task details:
		`schtasks /query /TN "UpdateCheck" /V`


