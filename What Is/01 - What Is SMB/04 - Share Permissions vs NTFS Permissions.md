
> [!NOTE] Objective
> What you'll learn
> 	- What Share Permissions are and how they work
> 	- How Share and NTFS permissions interact
> 	- What "most restrictive means in real scenarios
> 	- How to correctly configure access using both layers
> 	- Red team abuse cases and blue team detection logic
## Deep Dive: What Are Share Permissions
- Share permissions apply only when accessing a folder over the network, not locally.
- Set via: `Right Click folder > Properties > Sharing > Advanced Sharing > Permissions`

| Permission   | Meaning                                      |
| ------------ | -------------------------------------------- |
| Full Control | Can read, write, delete, change permissions. |
| Change       | Can read, write, delete (No change)          |
| Read         | Can only view files and folders              |
**Applies only at the root of the share, not individual subfolders.**
#### Effective Permissions: NTFS + Share
	Final Permission = Most restrictive of NTFS and Share

| NTFS         | Share        | Final Result      |
| ------------ | ------------ | ----------------- |
| Full Control | Read         | Read (Share wins) |
| Read         | Full Control | Read (NTFS wins)  |
| Modify       | Change       | Change            |
| Deny         | Anything     | Deny Always Wins  |
#### Real-World Scenario
- `Share\\filesrv\HR` has Share permissions: **Read for Everyone**
- NTFS gives Modify to Domain Users
**Result?** Users can only **Read**. Because Share is more restrictive than NTFS
#### Recommended Practice:
- Set Share Permissions to Full Control for everyone and manage real access via NTFS only.
- Why?
	- NTFS is more granular (file level)
	- Easier to Audit
	- Share permissions can create confusion if misaligned
### Command Line View
- See Share Permissions:
```
Get-SmbShareAccess -Name "HR"
```
- List all file share:
```
Grant-SmbShareAccess -Name "HR" -AccountName "Everyone" -AccessRight Full -Force
```
#### Blue Team Use Case

| Misconfig                              | Risk                         |
| -------------------------------------- | ---------------------------- |
| Share = Full, NTFS - Full for everyone | Ransomeware, data theft      |
| NTFS = Modify, Share = Read            | User reports "Access denied" |
| Guest access                           | Unauthenticated exposure     |
| Share hiddenshares but accessible      | Red Team stealth access      |
| "Authenticated Users" on Share         | Allows domain wide access    |
#### Red Team Abuse
- LLMNR + SMB share combo to steal hashes (Responder)
- CObalt Strike payload drop via accessible shares
- Mapping to hidden shares (\\host\C$\) via stolen credentials
- PowerView used to enumerate misconfigured shares
