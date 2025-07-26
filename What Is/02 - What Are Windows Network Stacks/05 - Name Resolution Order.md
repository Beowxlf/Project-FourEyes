#### Objectives
- Learn :
	- How Windows resolves names like `fileserver.local` or `example.com`
	- What order Windows checks (hosts file, DNS, NetBIOS, ect)
	- How LLMNR and NetBIOS work
	- How attackers abuse name resolution
	- How to fix broken resolution step by step
#### Easy Explanation:
- When you type a name like example.come or `fileserver` --> PC goes down a checklist like this:
	1. Can I find them in my host file?
	2. Can I find them in my DNS cache
	3. Can I find them in my DNS Server?
	4. Can I find them in a LAN broadcast (LLMNR or NetBIOS)
	- It stops as soon as it finds a match.
	- If it finds nothing --> "Could not resolve host" or "Windows cannot access \fileserver"

### Deep Dive: The Windows Name Resolution Sequence

#### Standard Order
- **Host File**
	- Manual overrides located at: 
		`C:\Windows\System32\drivers\etc\hosts`
- **DNS Cache**
	- Locally stored answers
	- View with `ipconfig /displaydns`
	- Clear with `ipconfig /flushdns`
- **DNS Server Query**
	- Uses settings from ipconfig /all
	- If multiple DNS servers are listed, they're used in order
	- Uses UDP port 53
- **LLMNR** (Link-Local Multicast Name Resolution)
	- Fallback method for non-DNS names
	- Uses multicast address 224.0.0.2552 on UDP 5355
- **NetBIOS over TCP/IP** (NBT)
	- Legacy method
	- UDP 137, TCP 139
- **WINS** (rare)
#### Host File Example:
```
127.0.0.1        localhost
10.10.10.5       fileshare01
```

### Diagnostic Tools

| Tool                     | Purpose                    |
| ------------------------ | -------------------------- |
| `ping fileserver`        | See if name resolves to IP |
| `nslookup fileserver`    | Test DNS resolution only   |
| `ipconfig /displaydns`   | See DNS cashe              |
| `Get-DnsClientCache`     | Powershell version         |
| `nbtstat -a {hostname}`  | NetBIOS resolution         |
| `Get-HostEntry`          | Triggers resolution        |
| `dig`, `resolve-dnsname` | Advanced querying          |
### Why This Matters

#### Help Desk

| Problem                           | Root Cause                             |
| --------------------------------- | -------------------------------------- |
| Cannot access `\\fileserver`      | Name doesnt resolve via DNS or LLMNR   |
| Web app only loads by IP          | Hostname not in DNS, host files needed |
| User can ping IP but not hostname | DNS issue or name not registered       |
| Printer offline by name           | NetBIOS failed, DNS outdated           |
| Mapped drive fails                | Offline DNS or bad WINS fallback       |
#### Cyber
- Attackers abuse fallback methods:

| Technique           | Vector                          |
| ------------------- | ------------------------------- |
| LLMNR/NBT Poisoning | Responder tool, MitM attacks    |
| Fake WPAD entries   | Proxy autoconfig interception   |
| Malicious DNS       | Redirecting users to fake sites |
**Detection = Knowing what name resolution path the system used**
##### SOC Use Cases:
- Detect LLMNR packets and alert on suspicious respones
- Investigate spoofed DNS queries in SIEM
- Look for unusual `.local`, `.lan`, or `wpad` queries