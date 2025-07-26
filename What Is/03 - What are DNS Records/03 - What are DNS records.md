1. What are CNAMEs?

- A type of record that maps a domain name to another domain name.
- Use cases:
	+ Point subdomains to services
	+ Load balancing with CDNs
	+ Alias for branded URLs
- Considerations:
	+ Only be used for sub-domains, not root domains
	+ Domain with a CNAME cannot have additional DNS records
	+ Introduces an extra lookup so it is slower than an A record

2. What are A records?

- Address Records are the most fundamental DNS record. It maps a domain name directly to an IPv4 address - no middle man, no aliasing, just raw IP targeting.
- For anything above a small business, companies will typically use A records to route the sub domains to their IPs.
- Why use A records?
	+ Performance: Direct mapping means fewer lookups which means its faster.
	+ Simplicity: No aliases or redirection logic to worry about.
	+ Control: You decide exactly which server handles the domain.
	+ Necessary for apex domains: You cant use CNAMEs for root domains so A records are required.
	+ Firewall and DNS tuning: DNS filtering, geofencing, and rate limting often depends on clear IP mapping
- Limitations
	+ Tied to static IPs: If IP of your server changes, you must update the A record.
	+ No abstraction: unlike CNAMEs, A records dont allow you to alias to another name - so no backend flexability
	+ A != AAAA: If infrastructure uses IPv6, you need AAAA records for it not A.

3. What are Pointer records

- PTR record maps a IP to a DNS server (rDNS or reverse DNS lookup)
- Email Server Verification
	+ Email Servers use rDNS to verify the sender of the email. It will then confirm the rDNS matches the A record. This process is called FCrDNS (Forward Confirmeed reverse DNS)		
		= Email is built on a no trust protocol, anyone can claim to be anything.
		= PTR/rDNS: Verifies IP -> Domain mapping
		= SPF: Authorizes which IPs can send for a domain
		= DKIM: Signs the message with a cryptographic key tied to the domain
		= DMARC: Tells receiver what to do if SPF and DKIM fail
- What if no PTR?
	+ If server tries to send an email, then SPAM apps will reject the app and phishing filters tighten up where gmail might just say its unauthenticated.
		

4. What are MX records?

- Tells mail servers where to deliver email for a specific domain
- Key Points
	+ Domain Level Scope:
		= Tied to your root domain
	+ Points to a hostname:
		= Never an IP address, must be an A or CNAME resolved domain
	+ Uses priorities
		= Allows failover and load balancing
	+ Critical for SPF, DKIM, DMARC
		= MX must match or align with allowed sender policies
	+ Not used to send mail
		= Only tells other mail servers where to deliver inbound mail
- What happens behind the scenes:

	{Someone sends email to brayden@example.com}
	{Their mail server looks up the MX record for example.com}
	{It sees: example.com > MX > mail.example.com}
	{It resolves mail1.example.com to an IP using A record}
	{It opens an SMTP session to that IP}
	{Sends email}

- What if its misconfigured?
	+ MX record missing
		= Email cannot be delivered to the domain
	+ Points to non existent hostname
		= Permanent delivery failure (bounces)
	+ Hostname doesn't resolve to IP
		= Temporary failure or rejection
	+ Misaligned SPF/DKIM
		= Emails go to spam or are rejected
	+ Too many domains sharing MX
		= Increases attack surface (phishing, spoofing, takeovers)
- Security Use Case
	- Often used in:
		+ Email spoofing protection
			= Verifies senders IP = MX Domain or SPF
		+ Phishing detection
			= Catching unusual or rogue IP matches the MX domain or SPF
		+ Recon
			= MX records often expose what vendor a company uses
			{dig MX example.com} --> Bash
			{nslookup -type=mx example.com} --> powershell


5. What are TXT records?

- Definition: A record that allows domain owners to attach free-form text to their domain. Originally designed for annotation, now heavily used for:
	+ Email authentication
	+ Service verification
	+ Ownership proof
	+ security policy enforcement
- Real-World TXT record examples
	+ SPF: Prevents unauthorized IPs from sending email on your behalf
	+ DKIM: Cyptographically signs outgoing email messages
	+ DMARC: Tells receiving mail servers what to do if SPF/DKIM fail
	+ Domain Verification: Proves domain ownership to services like google workspace, AWS, Office 365, Facebook.
- Advanced Uses
	+ DNS Based policy declaration
		= Some orgs declare crypto keys, security policies
	+ MTA-STS policy
		= Secure email transport rules published via DNS
	+ Proof of possession
		= Bug bounty domains sometimes use txt to verify domain ownership
	+ Red team Abuse
		= Exfiltrating data over DNS (covert channels using TXT records)
- Security and SOC Relevance
	+ Email spoofing defense
		= SPF/DKIM/DMARC TXT records detwermine whether your emails are accepted or rejected
	+ Recon Intelligence
		= TXT Records can reveal who is hosting, managing, or validating a domain
	+ Threat Detection
		= Malicious TXT Records could signal command and control over DNS, data exfiltration, or typesqutting


6. What are NS records?

- Name Server records tell the world which DNS servers are authoritive for a domain or subdomain
- Delegate authority
	+ They dont hold IPs or content themselves, but they tell clients where to ask next.
- What NS Records Actually Do
	+ Delegation: NS records assign which servers control DNS for a domain
	+ Zone Transfer: Tell resolvers where to query authoritative data
	+ DNS resolution chain: Every DNS lookup follows the NS path until it reaches the zone that can answer
	+ Subdomain Delegation: You can have different NS records for a subdomain (api.example.com uses different DBNS than example.com)
-Why NS Records Matter for Security
	+ Domain Hijacking: If attackers change your NS records, they can control all DNS answers for your domain
	+ 3rd Pary Visibility: NS records often show who hosts DNS > Good for Recon
	+ Monitoring Takeovers: Sudden NS changes in logs = potential domain compromise or unauthorized delegation
	+ Multi-tenant SaaS security: Apps may use separate DNS zones per client (client1.app.com with separate NS records
- Advanced Use: Subdomain Delegation
	+ Can assign different DNS providers to subdomains via NS records
	+ Very common in:
		= Enterprise delegating apps/dev enviorments
		= SaaS companies isolating customer enviorments
		= CDNs offloading static content to external zones


7. What are SOA records?

- Definition: Mandatory DNS record that declares the primary DNS server for the domain, and defines how other DNS servers should handle zone transfers, caching, and synchronization
- SOA record Fields (and what they do)
	example.com IN SOA ns1.exampledns.com admin@example.com (
		205062701 ; serial
		7200	  ; refresh
		3600	  ; retry
		1209600	  ; expire
		86400	  ; minimum TTL
	)
	+ ns1.exampledns.com
		= Primary/master name server --> authoritive source
	+ admin@example.com
		= Contact email
	+ serial
		= Version of the zone file
		= Increases every time zone is updated 
	+ refresh
		= How often secondaries check for changes (in seconds)
	+ retry
		= How long secondaries wait before retrying after a failed refresh
	+ expire
		= If the primary cant be reached, how long secondaries keep serving the zone before dropping it
	+ minimum TTL
		= The default Time to Live for negative responses (eg for NXDOMAIN)
- Why SOA Records Matter
	+ Zone Integrity: Without a SOA = zone file isn't valid
	+ DNS replication: Secondary DNS servers use SOA to know when to pull new data
	+ Caching: TTLS and NXDOMAIN handling depend on the  SOA minimum TTL
	+ Forensics: Serial Numbers help analysts verify changes or rollback versions
	+ Recon: SOA often reveals the primary DNS server and admin email -> useful for OSINT
- Red team / Blue Team implications
	+ Red Team: SOA may expose internal hostnames, real admin emails, or legacy name servers
	+ Blue Team: Watch for unauthorized SOA changes -> Signals zone tampering or takeover
	+ DNS Security: SOA serial should increment cleanly -> Random jumps mean DNS poisoning or broken automation
	+ Disaster Recovery: Expire and retry values control how resilient your DNS  zone is during outages
	
8. What are SRV records.

- Definition: Service Record maps a service and protocol to the host and port that provide that service
	+ Answers the questions: "Where can I find the service I need, and on what port, with what priority and weight?"
- Real World Applications
	+ Microsoft AD: Clients use SRV to find domain controllers
	+ Kerberos Authentication: Used for Identity Services
	+ SIP/VoIP: {_sip._udp/tls} to find SIP servers for calls
	+ Teams/Skype: Use SRV to auto configuration server connections
	+ XMPP: For IM/chat platforms
- Security Implications
	+ Recon signal: SRV records often reveal internal services, like AD domain controllers or SIP

9. What are CAA records

