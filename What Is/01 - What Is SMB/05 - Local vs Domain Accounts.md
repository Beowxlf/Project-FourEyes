
> [!NOTE] Objective
> - Difference between local and domain accounts
> - How authentication happens when cpmmectomg tp a sjare
> - What happens when connecting to a share
> - What happens when you access a resource across machines
> - How SID-to-name resolution and token generation works
> - Why your permissions can be perfect but still get denied
> - Common misconfigurations in SMB enviorments, especially at SMB size orgs
### Local vs Domain Account Basics

| Account Type   | Description                | Scope        |
| -------------- | -------------------------- | ------------ |
| Local Account  | Exists on a single machine | Local-only   |
| Domain Account | Managed by AD              | Network-wide |
Even if two accounts have the same name(Brayden), they are not equal unless they shard a SID
### Authentication Flow (accessing \Share1\Share)
1. Client (Brayden-PC) connects to \Server1\Share
2. Server1 asks: "Who is this user?"
3. If Brayden is in the same domain > Kerberos
4. If not > Try NTLM or fallback (fail)
5. Server1 checks Brayden's **take** > Are they in a group with NTFS/Share rights
### Gotcha: Local user != Remote Local User
- You cannot use `Brayden` from `Workstation-A` to access `Server-B` unless: 
	- Youre using a domain account
	- Or that exact username + password exists on server-B
	Even then the SIDs will not match
### Effective Access Scenarios
##### Domain Access
- Brayden logs in with main domain creds
- Server sees domain SID > checks group membership
- Access granted based on ACLs
##### Local Account Mismatch
- Brayden logs into local PC
- Tries to access \Server1\HR
- Server sees SID doesnt match its local Brayden
- Access denied even if same username + password
### SID Breakdown

| SID Example                                   | Meaning              |
| --------------------------------------------- | -------------------- |
| S-1-5-21-3623811015-3361044348-303000820-1000 | Local or Domain user |
| S-1-1-0                                       | Everyone             |
| S-1-5-32-544                                  | Built in admin       |
| S-1-5-21                                      | Domain Users         |
**Windows doesntcare about the nam-it maps permissions based on SID match**
### PowerShell to Explore This
- Get current user SID `whoami /user`
- See access token groups `whoami /groups`
- Show user sessions on remote SMB server `Get-SmbSession`
### Common Real-World Issues

| Symptom                                          | Root Cause                             |
| ------------------------------------------------ | -------------------------------------- |
| Same user cannot access share on another machine | Local account mismatch                 |
| NTFS permissions look fine, still denied         | Token SID mismatch                     |
| User cannot access share after AD migration      | Group membership not synced            |
| Works on PC1, fails on PC2                       | PC1 uses domai, PC2 uses local login   |
| Shared folders randomly fail                     | Untrusted domain or stale SID mappings |
### Blue Team Considerations
- Prefer domain accounts only for all share access
- Audit group membership (net group {groupname} /domain)
- Avoid lcoal user shares in business settings (e.g \\DESKTOP-1234\stuff)
- Disable Guest + anonymous shares
- Monitor Event ID 5140 (SMB access) and 4624 (Logon)