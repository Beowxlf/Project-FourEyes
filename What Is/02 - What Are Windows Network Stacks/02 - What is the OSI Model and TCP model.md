### OSI Model - The 7 Layer Reference Model
- Layer 7
	- **Name:** Application
	- **Function**: Human-facing apps
	- **Example:** Chrome, Outlook, Teams
- Layer 6
	- **Name:** Presentation
	- **Function:** Format and encrypt data
	- **Example:** SSL, TLS, Encoding
- Layer 5
	- **Name:** Session
	- **Function:** Maintains sessions
	- **Example:** NetBIOS, SMB sessions
- Layer 4
	- **Name:** Transport
	- **Function:** Reliable delivery
	- **Example:** TCP, UDP
- Layer 3
	- **Name:** Network
	- **Function:** Addressing + routing
	- **Example:** IP, ICMP
- Layer 2
	- **Name:** Data Link
	- **Function:** Framing on LAN
	- **Example:** Ethernet, ARP
- Layer 1
	- **Name:** Physical
	- **Function:** Bits on wire
	- **Example:** NIC, cables, WIFI

### TCP/IP Model (What Windows Actually Uses)
- Application
	- **OSI Layers:** 5,6,7
	- **Windows Examples:** HTTP, SMB, DNS
- Transport
	- **OSI Layer:** 4
	- **Windows Examples:** TCP, UDP
- Internet
	- **OSI Layer:** 3
	- **Windows Examples:** IP, ICMP
- Link
	- **OSI Layers:** 1,2
	- **Windows Examples:** Ethernet, WiFi

### Windows Networking Stack Internals
- Windows uses TCP/IP stack, implented as follows:
	- Key Components
		- Winsock (WS2_32.dll)
			- Windows API used by applications to send/recieve network data
		- TCP/IP.sys
			- Kerbnal-mode driver that implements TCP/IP stack
		- NDIS.sys
			- Communicates with physical NIC drivers
		- NIC
			- Hardware sending out packets
	- Flow of network packet from chrome to internet
		1. Chrome makes request  --> Winsock API
		2. Winsock passes to TCP/IP.sys --> adds TCP/IP headers
		3. Passed to NDIS.sys --> NIC
		4. Sent over physical medium (Ethernet or Wi-Fi)
		5. Response comes in and is unwrapped in reverse


### Tools to Visualize and Monitor Windows Stack

| Tool                       | Use                                           |
| -------------------------- | --------------------------------------------- |
| Netstat -en                | Show  current TCP connections and ports       |
| Wireshark                  | Packet capture at all stack layers            |
| TCPView                    | GUI version of netstat (live)                 |
| netsh winsock show catalog | Show Winsock LSPs (Layered service providers) |
| Get-NetIPConfiguration     | View full IP config and intercae settings     |
| ipconfig /all              | Legacy command for quick interface check      |
| route print                | View routing table in Windows                 |
| tracert {IP}               | Trace packet route over network               |
### Use Cases from a Help Desk Perspective

| User Complaint                       | Windows Stack Layer Involved | Fix                              |
| ------------------------------------ | ---------------------------- | -------------------------------- |
| "Outlook can't connect"              | Layer 7 (App), Layer 4 (TCP) | Check DNS, check TCP ports       |
| "Internet works but Teams is broken" | Layer 5-7                    | App-specific or firewall rule    |
| "Can't ping printer"                 | Layer 3 (IP)                 | Check subnet, ARP cache, routing |
| "Wi-Fi keeps dropping"               | Layer 1/2                    | NIC drivers, signal strength     |
| "Websites don't load"                | Lauer 3(IP), Layer 4(TCP)    | Check netstat, ping, tracert     |
