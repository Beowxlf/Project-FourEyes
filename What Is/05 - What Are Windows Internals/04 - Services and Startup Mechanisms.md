If the system starts it, it's trusted. That's why attackers love it.

> [!NOTE] Objective
> Understand how Windows services and startup mechanisms work, where legitimate and malicious persistence hides, and how to detect suspicious autostarts using tools like `services.msc`, Autoruns, and Event Viewer
## Explain Like I Am 12
- Think of Windows as a hotel. Services are employees that automatically start when the hotel opens
- Some are important (front desk, kitchen), others less go (gift)
- Attackers sneaks in malware disguised as staff, and once they're on the startup, they launch every time the hotel opens
## Deep Dive: How Persistence Works via Services
#### Windows Services
- Services are long running processes managed by the Service Control Manager (SCM)
- Run in background without user login
- Can be configured to start:
	- Automatically
	- Manually
	- Delayed
	- Disabled
#### Startup Mechanisms

| Method                    | Description                       |
| ------------------------- | --------------------------------- |
| Startup Folder            | Runs on user login                |
| Run/RunOnce Registry Keys | Executes on login                 |
| Services (SCM)            | Background system level processes |
| Scheduled Tasks           | Time or Event triggered           |
| WMI Persistence           | Script triggered by system event  |
#### Key Persistence Abuse Tactics

| Method                              | Description                                     |
| ----------------------------------- | ----------------------------------------------- |
| Service Creation                    | Malware installs a fake service (sc create)     |
| Service Hijack                      | Malware modifies a path of a legitimate service |
| Autorun Registry Keys               | Points to payloads at boot                      |
| Image File Execution Options (IFEO) | Debuggers used to hijack legit app launch       |
| Scheduled Tasks                     | Malware executes silently every reboot          |
#### Tools

| Tool               | Use                                                                                    |
| ------------------ | -------------------------------------------------------------------------------------- |
| services.msc       | View and manage system services                                                        |
| `sc query`/`sc qc` | CLI version of above                                                                   |
| Autoruns           | See everything that auto-starts (GUI)                                                  |
| taskschd.msc       | View Scheduled Tasks                                                                   |
| Event Viewer       | Look for Event IDs: 4697 (Service Install), 7045 (service created), 4702(task updated) |
#### Lab - Hunt For persitence
- Step 1: Examine Running Services
	- `sc query type = service state= all`
	- Look for:
		- Unusual names (Windows update helper --> not real)
		- `sc qc <servicename>` to see service path
	- Example: `sc qc wuauserv`
- Step 2: Open services.msc
	- Sort by Startup Type (Focus on automatic)
	- Inspect description and path to executable
	- Check if it runs as SYSTEM or localService
- Step 3: Run Autoruns (Sysinternals)
	- Download and run Autoruns as Admin
	- Check tabs
		- Logon
		- Scheduled Tasks
		- Services
		- Drivers
		- Image Hijacts
	- Look for:
		- Unsigned entries
		- Missing descriptions
		- Path in Temp folders, %APPDATA%, or suspicious subfolders
	- Export the full list (File > Save) if desired
- Step 4: Event Log Search
	- Open Event Viewer > Security or system logs
	- Filter:
		- Event ID4697 > Service was installed
		- Event ID 7045 > A service was created
		- Event ID 4702 > Scheduled Task updated
	- Correlate timestamps with installs or system changes