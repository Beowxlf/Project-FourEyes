### Objectives:
- Learn how to visualize connections and open ports
- How to trace packet flow and connection status
- How to validate DNS, routing, and firewall behavior live
- How to inspect packets with Wireshark
- How these tools empower Help Desk and cyber defense workflows
## Core Tools
#### 1. Netstat
`netstat -an`

| Flag | Use                                         |
| ---- | ------------------------------------------- |
| -a   | Show all connections + listening ports      |
| -n   | Show numeric IPs, not Hostnames             |
| -o   | Shows owning process ID                     |
| -e   | Show Ethernet stats (packets, bytes, error) |
**Use Case:**
- See which ports your machine is listening on
- Confirm if a remote connection is established
- Diagnose app behavior (eg is Teams using port 443)
#### 2. ipconfig / Get-NetIPConfiguration

| Purpose              | Command                              |
| -------------------- | ------------------------------------ |
| View adapter details | ipconfig /all                        |
| Renew DHCP lease     | ipconfig /release && ipconfig /renew |
| View via PowerShell  | Get-NetIPConfiguration               |
#### 3. tracert + pathping

| Command         | Function                                             |
| --------------- | ---------------------------------------------------- |
| tracert {host}  | Shows hop-by-hop path to remote IP                   |
| pathping {host} | Combines tracert and ping - adds packet loss metrics |
**Diagnosing routing issues, ISP problems, firewall blocks mid-path**
#### 4. Test-NetConnection (PowerShell)
`Test-NetConnection -ComputerName example.com -port 443`
- Replaces telnet for port testing
- Checks ICMP ping, TCP port reachability, routing path
`Test-NetConnection -ComputerName 10.10.10.1 -Port 445`
**Tells you:**
- Pingable?
- Port open?
- RTT?
- Used Interface?
#### 5. Resolve-DnsName, nslookup, dig
- Resolve-DnsName example.com
- nslookup example.com
- dig --> Shows full DNS record set
- **Validates**
	- DNS record resolution
	- TTL and authoritative source
	- Which DNS server is being queried
#### 6. Wireshark
- Capture all packets on an interface
- Filter with:
	- `ip.addr == 192.168.1.100`
	- `dns`
	- `icmp`
	- `arp`
- **Use for**
	- Verifying DHCP handshakes
	- Watching DNS queries in real time
	- Analyzing TCP 3 way handshake
	- Detecting ARP spoofing or NetBIOS noise
## Help Desk Applications

| Problem                      | Tools                                         |
| ---------------------------- | --------------------------------------------- |
| Cannot reach website         | ping, Test-NetConnection, tracert             |
| App won't connect            | netstat, TCPView, firewall logs               |
| Drive mapping fails          | Resolve-DnsName, ping, Get-NetIPConfiguration |
| DNS resolves but no response | Test-NetConnection -Port 445                  |
| Suspected malware beacon     | netstat -an, TCPView, Wireshark               |
## Cyber Applications

| Technique             | Tool                                                |
| --------------------- | --------------------------------------------------- |
| Port scan detections  | netstat -an, SIEM                                   |
| C2 detection          | TCPView, Wireshark, EDR                             |
| LLMNR abuse           | Wireshark dns, entropy analysis                     |
| Unauthorized outbound | Test-NetConnection, firewall logs, routing analysis |
