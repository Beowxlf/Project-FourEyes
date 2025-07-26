## Objective:
- Learn how to configure and debug Windows Defender Firewall
- Learn how to tell the difference between inbound vs outbound rules
- Learn how Windows routes traffic using its routing table
- What happens when multiple gateways exist
- How malware abuses firewall rules and routes

## Part 1: Windows Defender Firewall
#### Controls:
- What apps are allowed to recieve traffic
- What apps are allowed to send traffic out
- What ports are open
- Based on profile (Domain, Private, Public)
#### Inbound vs Outbound Rules

| Direction | Meaning                                         | Example                            |
| --------- | ----------------------------------------------- | ---------------------------------- |
| Inbound   | Block or allow traffic coming into your machine | Remote Desktop, incoming ping      |
| Outbound  | Block or allow your machine sending data out    | Malware beaconing, browser traffic |
**Inbound rules are active by default. Outbound rules allow-all by default unless customized**
#### View and Manage Rules
- GUI:
	wf.msc > Windows Defender Firewall with Advanced Security
- PowerShell:
	`Get-NetFirewallRule`
	`New-NetFirewallRule`
	`Remove-NetFirewalRule`
- CLI:
	`netsh advfirewall firewall show rule name=all`
#### Common Fixes

| Issue                         | Fix                                 |
| ----------------------------- | ----------------------------------- |
| App not recieving connections | Inbound rule not present or blocked |
| Cannot ping machine           | ICMP inbound rule disabled          |
| Malware persistance           | Hidden outbound rule allowing C2    |
| Printer unreachable           | Port 9100 blocked inbound           |
| RDP blocked                   | Port 3389 not allowed inbound       |
### Blue Team Relevance
- Monitor for creation of custom firewall rules by malware
- Alert on new outbound rules to strange IPs
- Use PowerShell to log changes to firewall configs

## Part 2: Routing Table in Windows

#### What is the Routing Table?
- Routing table defines **where to send traffic based on destination IP**
- Each entry says: "If traffic is going to X, send it to gateway Y with interface Z"
#### View It
- We can view it on bash with: `route print`
- Or on PowerShell it is: `Get-NetRoute`
#### Core Fields

| Field               | Meaning                          |
| ------------------- | -------------------------------- |
| Network Destination | Target subnet or IP              |
| Netmask             | Subnet Size                      |
| Gateway             | Next hop device (Usually Router) |
| Interface           | Which NIC to use                 |
| Metric              | Priority                         |
#### Default Route Example
`0.0.0.0  |  0.0.0.0  |  192.168.1.1  |  192.168.1.100  |  25`
	- Any unknown traffic --> Send it to **192.168.1.1** using **192.168.1.100** interface
#### Modify Routes
- Add: 
	`route add 10.10.10.0 mask 255.255.255.0 192.168.1.1`
- Delete:
	`route delete 10.10.10.0`
- Add persistence: 
	`route -p add ...`

## Help Desk Case Solved by Firewall + Routing Knowledge

| Problem                              | Real Fix                          |
| ------------------------------------ | --------------------------------- |
| App won't connect                    | Inbound firewall rule missing     |
| Can ping IP but no app access        | Port blocked, not IP              |
| Internet broken after VPN connects   | Bad default route                 |
| Local device unreachable post-reboot | Static route not set persistently |
| Device unreachable via name          | Firewall blocking NetBIOS/LLMNR   |
## Cybersecurity Angle

| Risk                | Abuse                                                       |
| ------------------- | ----------------------------------------------------------- |
| Malware persistence | Add stealth outbound firewall rules                         |
| C2 traffic routing  | Modify routing table to route traffic through attacker host |
| Data exfil          | Use hidden outbound rule + static route to bypass VPNs      |
#### Detection
- Monitor for new `New-NetFirewallRule` activity
- Audit `route print` during incident response
- SIEM alert on strange outbound connections (egress control)