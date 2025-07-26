 ## Writing Detectable Logic from Logs

> [!NOTE] 12 Year Old
> Imagine every time someone breaks a rule at school, a camera records it, but only if you tell the camera what to look for. SIGMA is that rulebook.
> You write things like:
> - "If someone opens a file from a teachers desk after hours, alert me"
> - "If someone logs in too many times in a row and fails, alert me"
> **SIGMA turns your thoughts into SIEM alerts, no matter what SIEM**
## Deep Dive
#### What is SIGMA?
- It is a detection rule format (like Snort network traffic, but for logs)
- Written in YAML
- Converts into Elastic, Splunk, Sentinel, Wazuh
SIGMA = SIEM-agnostic detection logic
#### Anatomy of a SIGM Rule
```
title: Suspicious PowerShell Execution
logsource:
	product: windows
	service: security
detection:
	selection:
		EventID: 4688
		ParentImage: `*winword.exe`
		Image: `*powershell.exe` CommandLine|contains `-enc`
	condition: selection
level: high
```
- Breakdown
	- `title:` what the rule detects
	- `logsource:` Where the data comes from (Windows Security logs)
	- `detection:` What event fields to match
	- `condition:` when to trigger (if match)
	- `level:` How severe the alert is
#### Rule Writing Examples
- Brute Force (Event ID 4625)
```
title: Encoded PowerShell Execution
logsource:
	product: windows
	service: security
detection:
	selection:
		EventID: 4625
		LogonType 10
	timeframe: 5m
	condition: selection | count >= 5
```
- Encoded PowerShell
```
title: Encoded PowerShell Execution
logsource:
	product: windows
	service: security
detection:
	selection:
		EventID: 4688
		Image: `*powershell.exe` CommandLine|contains: `-enc`
	condition: selection
level: high
```
- Service Creation (Persistence)
```
title: Suspicious Service Installed
logsource:
	product: windows
	service: system
detection:
	selection:
		EventID: 7045
		ServiceFileName|contains:
		  - `\AppData\`
		  - `\Temp\`
	condition: selection
level: high
```
#### How Its Used in Real Life
- Blue teams write these to detect attackers
- Red teams test them to bypass detections
- SOCs use them as base templates and customize
#### Why You Care as a Junior Analyst

| Skill               | Value                                                                              |
| ------------------- | ---------------------------------------------------------------------------------- |
| Understanding SIGMA | You can explain **why a rule truiggers**                                           |
| Writing SIGMA       | You build your own detections                                                      |
| Customizing SIGMA   | YOu translate real world behavior (like Caldera or Hydra attacks) into real alerts |
