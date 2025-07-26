### Which protocol does Windows use to broadcast name request if DNS fails?
- If the DNS fails to find a name respolution request that means it want on host files and DNS oculdnt find it. This means that your computer will send out a LLMNR ping out with I believe 5355 port. The way that the sequence works is it goes from host file > DNS local cache > dns query > then LLMNR.

### What is the first source Windows checks when resolving a Hostname
- The first source windows check when resolving a host name is the host file which is located in \Windows\System32\drivers\etc\host


### How can you view the ARP cache in Windows
I can find the arp cache by using `arp -a`
### How do you release and renew a DHCP lease in Windows
- `ipconfig /release` `ipconfig /renew`
### What does it mean if a device has a 169.254.x.x ip address
- It could not get an IP address from DHCP server
### What address range is used for APIPA
- 169.254.x.x
### Why might disabling NETBIOS improve security
- NetBIOS is a legacy protocol that is not secure.
### What PowerShell command tests both ICMP and TCP connectivity
- `Test-NetIPConfiguration`
### What does ARP stand for and what does it do?
- 
### What command would you use to clear the ARP cache in Windows
- Get-DnsClientCache
### What does the subnet mask 255.255.255.0 mean
- a
### How can you manually overritde DNS resolution in Windows
- a
### What happens if your device is in a different subnet from the target device
- a
### 