> [!NOTE] Objective
>- Understand how Windows Defender logs threats
>- How AV software interacts with malware, and what types of detections are logged, ignored, or bypassed.
>- Learn where Defender keeps its logs
>- How to query detections
>- What evasion patterns to watch for

> [!NOTE] Explain Like I Am 12
> - Defender is like a guard dog, sometimes it barks at shadows (false positives), other times it sleeps through a break in
> - As a detection engieer or blue teamer, your job is not just to trust it - it is to:
> 	- Read its logs
> 	- Detect when it is turned off
> 	- Spot when it misses something
## Deep Dive: How Defender Works
#### Defender Components

| Component            | Description                         |
| -------------------- | ----------------------------------- |
| Real-Time Protection | Constant file/process scanning      |
| Cloud Protection     | Sends samples for deeper inspection |
| Tamper Protection    | Prevents registry/setting edit      |
| Behavior Monitoring  | Catches suspicious chains           |
| Threat Service       | Runs engine and reporting system    |
#### Log Sources You Must Know

| Log Type              | Location                                                                                              |
| --------------------- | ----------------------------------------------------------------------------------------------------- |
| Windows Defender Log  | Event Viewer > Applications and Services Logs > Microsoift > Windows > Windows Defender > Operational |
| Security Center Log   | Event Viewer > Security                                                                               |
| Command Line Queriers | Powershell + MpCmdRun.exe                                                                             |
| Wazuh/EDR SIEM        | Aggregates alerts                                                                                     |
## Attacker Behaviors to Detect

| Tactic                                     | Log Indicator                                                  |
| ------------------------------------------ | -------------------------------------------------------------- |
| Malware dropped but not executed           | File found in Defender scan Log                                |
| Script based attack (ex .j/.vbs/.ps1)      | Defender may only catch payload, not stager                    |
| Disabling Defender via registry/PowerShell | EventID 5001+ (or suspicious audit log)                        |
| Tamper protection Off                      | Alerts in Defender UI or registry mismatch                     |
| Living off the Land                        | Tools like certutil, mshta often missed without behavior rules |
## Tools

| Tool         | Purpose                                   |
| ------------ | ----------------------------------------- |
| MpCmdRun.exe | CLI interface for Defender                |
| Get-MpThreat | View history of malware                   |
| Event Viewer | Track Defender state manually             |
| PowerShell   | View policies, signatures, detection sett |

