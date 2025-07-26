
> [!NOTE] Objective
> - What Event Logs matter and **why attackers trigger them**
> - How to analyze logs to ** brute force, malware, persistence, and privilege escalation
> - How to think like a SOC analyst and detection engineer, not just a log reader
## Deep Dive: Event Logs for Security Monitoring
#### Core Security Logs

| Log                             | Description                                                                            |
| ------------------------------- | -------------------------------------------------------------------------------------- |
| Security                        | Most critical for defenders - tracks login attempts, file access, object creation, etc |
| System                          | Driver failures, shutdowns, restarts - can show attacker cleanup or a tool execution   |
| Applicaiton                     | Software activity - useful when walware or persistence is embedded                     |
| Sysmon (Microsoft Sysinternals) | Advanced logging -> shows process creation, network connections, etc.                  |
#### Common Event IDs (from the Security Log)

| Event ID | What It Means                             | Why it matters                           |
| -------- | ----------------------------------------- | ---------------------------------------- |
| 4624     | Successful Login                          | Look for unusual logon types or accounts |
| 4625     | Failed login                              | Brute force indicators                   |
| 4672     | Special Priveleges assigned (admin logon) | Escalation of privilege                  |
| 4688     | Process Creation                          | Track execution of suspicious tools      |
| 7045     | New service created (from system log)     | Often malware or persistence tools       |
| 4769     | Kerberos ticket requested                 | Can show lateral movement                |
| 5140     | File share accessed                       | Lateral movement via SMB or file drops   |
#### Detection Logic
Lets say an attacker is brute forcing RDP:
- You will see a burst of 4625 (failed logins) to same user
- Eventually you will see a 4624 if successful
- Then maybe a 4672 if it was a privileged account
As a detection engineer, your job is to:
- Correlate those logs by time, source IP, and account
- Trigger an alert when thresholds are met (eg. 10 failed logins from one IP in 5 minutes)
- 