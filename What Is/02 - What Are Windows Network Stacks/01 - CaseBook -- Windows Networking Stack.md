**Date:** #2025-06-30
**Purpose:** Understanding how Windows transmits and receives network packets through the TCP/IP stand and how the OSI model maps to practical network operations

### Summary
Windows implements networking using a 4 layer TCP/IP Model that maps to the theoretical 7 layer OSI model. Applicatios like Chrome use Winsock APIs to send rtequests, which are encapsulation into TCP segments and IP packets, sent out via NDIS and NIC drivers. Understanding this allows Help Desk technicians to diagnise network connectivity issuesm packet loss, firewall rules, DNS failures, and more.

### Key Takeways:
- OSI = Theory  |  TCP/IP = Implementation
- Windows uses Winsock --> TCP/IP.sys --> NDIS --> NIC
- Tools like netstat, Wireshark, and Get-NetIPConfiguration show real-time insights
- All user complaints tie back to a layer: bad DNS = Layer 7, wrong gateway = layer 3
- Mastering this lets you escalate faster, diagnose smarter, and understand traffic analysis tools like Wireshark/Splunk

### Questions
1. What happens when you visit a website step by step from app to NIC
	1. Browser initiates connection to a domain like example.com
	2. It asks the OS to resolve the domain name via the DNS Resolver stub on the host
	3. Winsock handles this communication for the browser
	4. The dns query is formnatted as a UDP packt (Layer 4) and encapsulated in an IP paclket (Layer 3)
	5. It is passed to TCP/IP.sys, then handed off to NDIS, and then to the NIC
	6. NIC sends it over the netowkr to your DNS
	7. DNS replies with an A or AAAA record, ip address of example.com
	8. Your browser now opens a TCP connection with the web server at the resolved IP address
	9. The browser sends an HTTP GET request(Layer 7) over TCP (Layer 4)
	10. Response comes back -> NIC -> NDIS -> TCP/IP stack -> Windsock -> Browser

So from my understanding, I enter in a domain name. Browser doesn't know what a domain name means so it wants to resolve it to a an IP address. the IP address is unknown so it asks the host(me) to provide the IP. My winsock will handle it from here. First the Winsock passes the Packet through TCP/IP.sys to include the proper headers and formatting. It is formatted as a UDP packet by default then encapsulated as an IP packet. The packet is handed off to the NDIS then to the NIC where it is sent to our DNS. The DNS server will try to find it in its own records or if it cant find it it will l


2. What is Winsock and why is it critical
	- Winsock is a programming interface that allows windows applications to send and recieve data over TCP/IP networks
		- Provides consistent way for applications t talik to the network lay without worrying about how packets are formed or sent.
		- Does not handle networking directly, but passes down to TCP/IP.sys and NDIS
		- Winsock operates primarily at Layer 5-7 (Session - Application)
		- Misconfiguration in Winsock can break all network functionality which why you'll often see {netsh winsock reset} to fix the issue
3. How do netstat ipconfig and rout print relate to different layers
	- **netstat:** Displays active TCP/UDP connections, listening ports, protocol stats, and routing information
		- netstat -an = show all connections and listening ports numerically
		- netstat -e = shows ethernet level stats (bytes, errors, discatrds)
	- **ipconfig** / **Get-NetIPConfiguration**: Displays your IP address, subnet mask, default gateway, and DNS servers.
		- Also shows adapter-specific configurations like DHCP status
	- **route print**: Shows the Windows routing table - how your OS determines where to send packets based on their destination
		- Includes metrics, interface index, and gateway paths
4. How do transport and applications layers interact in Windows
	- Application Layer consists of programs like browsers, email clients, teams, etc.
	- These applications hand data to the transport layer via APIs like Winsock
	- Transport Layer uses TCP or UDP to:
		- Establish connections (TCP) or fire-and-forget (UDP)
		- Handle Segmentation, sequencing, reliability (TCP)
		- Multiple sessions using port numbers
	- Once the transport layer wraps the data in TCP/UDP headers, it hands it to the IP layer (Layer 3) for delivery
	- **Key: The app doesn't "tallk TCP", the OS does that on its behalf.**



---
**Date:** #2025-07-01
**Topic:** IP Addresses and Subnet Masks

**What I Learned:** 
- How IP + Subnet Mask define local communication
- How Gateway provides routing to other networks
- How to diagnose connectivity using ipconfig, route print, and ping
- Help Desk relevance: printers, unreachable apps, duplicate IPs
- Cyber relevance: Segmentation, lateral movement, misconfigured detection zones


---

#### ARP & MAC Resolution in Windows
**Date:** #2025-07-01
**What I Learned:**
- How Windows discovers MAC addresses tied to IPs
- How ARP works at Layer 2 vs IP at Layer 3
- Real-world usage of `arp -a`, `ping` and Wireshark ARP filters
- Help Desk Use: IP confilicts, "can ping but cannot connect", weird VLAN misroutes
- Cyber Use: ARP Spoofing, MITM setup, lateral movement visability


---
#### DHCP in Windows - Leases and Troubleshooting
**Date:** #2025-07-02
**What I Learned**:
- How Windows requests an IP via DORA
- Common Failure result in 169.254.x.x (APIPA) or IP conflicts
- `ipconfig /renew`, lease times, and DHCP reservations fix most problems
- Rogue DHCP detection = critical for SOC work and pentesting.
- Wireshark + bootp filter shows DHCP handshakes in real time

---
#### Windows Firewall & Routing Table Internals
**Date:** #2025-07-02
**What I Learned**:
- Firewall determines access permission, routing table determines destination
- Inbound/outbound rules differ, outbound is allow-all by default
- Route table shows how traffic is forwarded based on subnet logic
- Both components are abused by malware and used in post-exploitation
- wf.msc, netsh, route print, and PowerShell cmdlets are essential tools