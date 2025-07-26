
> [!NOTE] Objective
> - How drive mapping actually works (manual vs GPO vs script)
> - How to map and unmap drives from CLI like a pro
> - Why "access Denied" even when permissions look perfect
> - Real-world scenarios for fixing shared drive issues
> - How attackers abuse mapped drives for lateral movement
> - Logging, detection, and red flags
### Deep Dive: What is Drive Mapping
- Drive Mapping= assigning a drive letter to a shared folder over SMB
	`Z: \\filesvr\HR`
- Windows treats it like a normal folder - but every access request still goes across the network.
### Three Ways to Map Drives

| Method           | Pro                  | Con                    |
| ---------------- | -------------------- | ---------------------- |
| Manual (net use) | Fast, scriptable     | Temporary              |
| File Explorer    | Easy for Users       | Not scalable           |
| GPO Drive Maps   | Scalable across orgs | Complex, needs testing |
### Mapping Drives (CLI Style)
- Manual Map (with credentials):
	`net use Z: \\filesrv\HR /user:DOMAIN\brayden yourpassword`
- Map and persists:
	`net use Z: \\filesrv\HR /persistent:yes`
- Unmap a Drive:
	`net use Z: /delete`
- List all mapping:
	`net use`
### Troubleshooting "Access Denied"

| Cause                                 | Fix                                         |
| ------------------------------------- | ------------------------------------------- |
| Wrong user logged in                  | Re-auth withcorrect creds                   |
| SID mismatch (local vs domain)        | Confirm account type                        |
| Mapping made under different contexty | `Use /user:flag`                            |
| Old credentials cached                | `cmdkey /deleteor net use * /delete`        |
| NTFS permissions deny access          | Audit ACLs and inheiritance                 |
| Share permissions limit access        | Check both layers                           |
| Network not reachable                 | Test with `ping`, `Test-NetConnection`      |
| Hidden share used incorrectly         | Hidden shares (e.g D$) require admin rights |
### Real World Script Fix
```
net use * /delete /yes
cmdkey /delete:filesrv
net use Z: \\filesrv\HR /user:domain\brayden
```
### Red Team / Attacker Use Cases

| Attack                              | Description                             |
| ----------------------------------- | --------------------------------------- |
| Enumerate mapped drives             | Use `net use`, Powerview                |
| Harvest file contents               | Dump files from mapped Z:\shares        |
| Drop payloads                       | Write malware to trusted network shares |
| Use mapped drive for C2 persistance | Exfil via "normal" paths                |
### Blue Team / SOC Angle
- Monitor Event ID **5140** (SMB Access)
- Log `net use` behavior via EDR or Sysmon for suspicous mappings
- Block mapping to hidden shares unless authorized
- Restict drive letter assignments via GPO
### Helpful Powershell Commands
##### Show who is connected to a share:
`Get-SmbSession | Select-Object ClientComputerName, ClientUserName`
##### Audit what they're accessing
`Get-WinEvent -LogName Security | ? {$_.Id -eq 5140 }`
