#Arp #MAC
#### Mastering ARP Gives You Power to:
- Diagnose "weird" issues where ping works by **IP** but not **hostname**
- Understand how your system actually locates other machine on the LAN
- Detect ARP spoofing and lateral movement
- Fix duplicate IP and "device unreachable" issues like a T2

#### Learning Goals:
- What ARP does ad how it works with Windows
- How IP traffic is translated to MAC addresses
- What `arp -a` shows and how to send it
- How attackers poison ARP to redirect or sniff traffic
- Tools for diagnosis and detection

## ARP

#### What is ARP?
- ARP = Address Resolution Protocol
- It resolves IP addresses to MAC addresses
- **Why is it necessary**
	- IP is used at Layer 3
	- MAC is used at Layer 2
	- Network cards can only send to MAC addresses
#### When is ARP used?
- Anytime you send a packet to a device on your subnet --> Your system needs to know the MAC address tied to the destination IP
- If it doesnt already know it, it sends an ARP Request broadcast to the subnet
#### ARP Flow
1. You try to ping 192.168.1.100
2. Your OS checks the ARP cache
3. If not there, it sends: `ARP Request: Who has 192.168.1.100`
4. The machine replies: `ARP Reply: 192.168.1.100 is at aa-bb-cc-dd-ee-ff`
5. Now your system can build the Ethernet Frame and send the ping
#### Tools: How to Work With ARP in Windows

| command          | use                                   |
| ---------------- | ------------------------------------- |
| `arp -a`         | Shows current ARP cache (IP <--> MAC) |
| `arp -d *`       | Cleares ARP cache                     |
| `ping {IP}`      | Triggers ARP if no entry exists       |
| Wireshark Filter | Arp to see broadcasts                 |

### Common ARP Problems

| Problem                                   | Explanation                                            |
| ----------------------------------------- | ------------------------------------------------------ |
| Can ping IP but not connect to app        | MAC is reachable but app/service is down               |
| Device unreachable even though its online | ARP cache is stale or wrong                            |
| Duplicate IP warning                      | Two MACs claiming the same IP                          |
| Random packet drops                       | ARP table full or poisoned                             |
| MITM attack / sniffing                    | Attacker sends fake ARP replies to impersonate gateway |
#### Real-World Ticket Examples Solved by ARP Knowledge

| Issue                                     | Fix                                                 |
| ----------------------------------------- | --------------------------------------------------- |
| "I can ping the printer but I cant print" | ARP correct, but app/port issue                     |
| "IP conflict"                             | Run `arp -` and look for duplicate MACs             |
| Cannot access server after switch reboot  | Clear ARP cahce with `arp -d *` or wait for timeout |
| Strange traffic being sniffed             | Detect ARP spoofing in Wireshark                    |
| Vm has wrong binding                      | Clear static ARP, reboot adapter                    |
#### Cybersecurity Implications
- **Attackers use ARP spoofing** to:
	- Impersonate Gateways
	- intercept traffic for sniffing or MITM
	- Disrupt DNS or file server access
- **Detection Stratagies:**
	- Watch for miltiple MACs bound to same IP
	- Look for gratuitous ARP packets at high frequency
	- Use Wireshark filters: `arp.duplicate-address-frame` `arp.opcode ==2 and eth.src != know_MAC`

