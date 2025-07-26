E## Deep Dive: Kerberos, Tickets, and Attacks
#### Kerberos Ticket Types

| Ticket                       | Event | Purpose                                              |
| ---------------------------- | ----- | ---------------------------------------------------- |
| TGT (Ticket Granting Ticket) | 4768  | Proves Users Identity to the system                  |
| TGS (Service Ticket)         | 4769  | Grants access to a specific service(like CIFS, HTTP) |
## Event ID 4768 - TGT Request
Logged when:
- A user or service requests initial login to the domain
- Typically means someone just logged in or used runas
	**Key Fields:**
	- Account Name
	- Client Address (IP) = where request origonated
	- Result Code = 0x0 = success; others = failure
## Event ID 4769 - TGS Request
Logged when:
- A user tries to access a specific service using their TGT
You'll see this during:
- SMB file share access
- RDP connections
- Web Services (HTTP/hostname)
- Lateral movement
**Key Fields:**
- Service Name (cifs, host, wsmgmt)
- Ticket Encryption Type (0x17 = RC4, 0x12 = AES)
### Detection Use Case
- Pass the Ticket
	- Attacker steals a TGT from memory and reuses it.
	- You see a 4768 or 4769 from a new workstation/IP
	- Account was idle or should never log in from there
	- Encryption type = RC4 on a domain with AES enabled = Red FLAG
- Service Account Ennumeration
	- Adverseries request tons of TGS (4769) looking for vulnerable services.
	- Burst of 4769s with different services in short time = scan
- Targeting High-Priv Accounts
	- Watch out for 
		- krbtgt account activity
		- Admin account 4769s with CIFS