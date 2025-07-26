
### What is an IP address?
- Am IP address is a 32 bit number that identifies a device on a network.
- Ex: 192.168.1.100
- It has 2 parts
	- Network Portion = Defines the subnet
	- Host Portion = Identifies the individual Service

### What is a Subnet Mask?
- It determines which part of the IP is the network and which part is the host.

| IP Address    | Subnet Mask   | Result                            |
| ------------- | ------------- | --------------------------------- |
| 192.168.1.100 | 255.255.255.0 | /24 -> 256 IPs total (254 usable) |
| 10.0.5.22     | 255.255.0.0   | /16 -> 65536 IPs in total         |
- A subnet mask chops the IP space into isolated segments

### What is a CIDR Block
- CIDR = Classless Inter-Domain Routing
	- Shorthand for subnet mask

| CIDR | Subnetmask      | Hosts                                     |
| ---- | --------------- | ----------------------------------------- |
| /24  | 255.255.255.0   | 254                                       |
| /16  | 255.255.0.0     | 65,534                                    |
| /30  | 255.255.255.252 | 2 Hosts (Used for router to router links) |

### What is a Default Gateway
- Default Gateway is the IP of the router your computer sends traffic to when it needs to reach something outside its subnet
	- Set in DHCP or manually
	- Shown in ipconfig

### Common Help Desk Problems Solved by IP Knowledge

| Issue                           | Diagnosis                                   | Fix                                |
| ------------------------------- | ------------------------------------------- | ---------------------------------- |
| Cannot access printer           | IP is out of subnet                         | Change IP or route                 |
| "Internet not working"          | Missing or wrong gateway                    | Reset IP/Gateway                   |
| IP Conflict                     | Duplicates IPs in the same subnet           | Set static IP or fix DHCP          |
| Can ping by IP but not hostname | DNS or name resolution issue                | Check hosts, DNS                   |
| VLAN mismatch                   | IP and subnet dont match switch port config | Fix switch port or VLAN device tag |

#### Subnetting as Security
- Networks often segment:
	- Finance: 10.10.1.0/24
	- Engineering: 10.10.2.0/24
- Devices cant talk across without a router and firewall rule
#### Cyber perspective
- Threat actors love flat networks --> Easier lateral movement
- Segmented networks = better defense
- Bad routes or incorrect subnetting = missing detections or broken containment

