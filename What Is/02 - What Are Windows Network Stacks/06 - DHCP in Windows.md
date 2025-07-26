### Objectives:
- Learn how Windows gets its IP addresses automatically
- What DORA process is and how it fails
- How to fix lease, conflict, and reservation problems
- How DHCP interacts with gateways, DNS, and subnetting
- How to verify, reset, and debug DHCP issues on live systems

### Deep Dive: How DHCP Works (DORA Process)

#### DORA Process

| Step                | Action                                           | Protocol                 |
| ------------------- | ------------------------------------------------ | ------------------------ |
| **D** = Discover    | Client broadcasts: "Any DHCP servers out there?" | UDP 67 > 255.255.255.255 |
| **O** = Offer       | DHCP server replies: "Use IP 192.168.1.55"       | UDP 68                   |
| **R** = Request     | Client says: "Yes, I'll take that IP"            | UDP 67                   |
| **A** = Acknowledge | Server confirms "Lease granted"                  | UDP 68                   |
This 4 Step process happens every time a client requests an IP

#### Where DHCP Runs in Windows
- DHCP Client Service: Manages lease requests and renewals
- DHCP Server Role (On Windows Server): Offers and manages scopes

#### Viewing DHCP Info on Your Machine
- **CLI:** `ipconfig /all`
	- Look for: 
		- DHCP Enabled = Yes
		- IPv4 Address
		- Lease Obtained / Lease Expires
		- Default Gateway
		- DHCP Server
- **PowerShell:** `Get-NetIPConfiguration`

### Help Desk Implications
#### Common Help Desk Problems Solved by DHCP Knowledge

| Problem                            | Root Cause                          | Fix                                          |
| ---------------------------------- | ----------------------------------- | -------------------------------------------- |
| "Cannot connect to Wi-Fi/internet" | No IP or 169.254.x.x APIPA address  | ipconfig /renew                              |
| IP conflict                        | Two devices using the same IP       | Check DHCP lease pool, force renew           |
| Keeps losing IP after reboot       | Lease is expiring or adapter resets | Set reservation or extend lease time         |
| Can ping IP but not name           | DHCP did not register in DNS        | Use dynamic DNS updates or register manually |
| Guest device cannot get internet   | VLAN DHCP scope not reachable       | Check DHCP relay or trunk config             |
#### APIPA (169.254.x.x)
When no DHCP server responds, Windows self assigns an APIPA address
If you see a 169 address:
- The system did not get a response in its Discover packet
- Check cable, VLAN, switch port, or firewall blocking UDP 67/68
#### Reservation vs Dynamic Leases

| Term          | Meaning                                       |
| ------------- | --------------------------------------------- |
| Dynamic Lease | Automatically handed out from pool            |
| Reservation   | DHCP always gives a specific MAC the same IP  |
| Static        | Manually set on the client, bypasses the DHCP |
Use reservations when a device needs a **predicatable IP** 
- This could be printers, servers, ect
### Cyber Implications
- Rouge DHCP = MitM
- DHCP starvation = DoS
- DHCP snooping on switches = Prevents unauthorized DHCP replies
Blue Teams detect rogue DHCP via:
- Multiple DHCP servers in PCAP
- Unexpected DHCP offers from non-authorized MACs
#### Tools to Use

| Tool                  | Purpose                                                 |
| --------------------- | ------------------------------------------------------- |
| `ipconfig /release`   | Drop current lease                                      |
| `ipconfig /renew`     | Requests new lease                                      |
| `Get-IPConfiguration` | Detailed info in PowerShell                             |
| Wireshark filter      | bootp to see DHCP traffic                               |
| Event Viewer          | Logs DHCP client events (Microsoft-Windows-DHCP-Client) |
