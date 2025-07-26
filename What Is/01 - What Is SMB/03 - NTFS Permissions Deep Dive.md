## Objectives
- The difference between NTFS and Share Permissions
- How ACL and Access Control Entries (ACE) work
- The priority of Deny vs Allow
- What inheritance really means
- How to calculate effective permissions
- Blue team implications: data leakage, privilege escalation, and insider threats
### Deep Dive: NTFS Permission System
#### What is NTFS?
- New Technology File System
- It controls how files are read, modified, executed and deleted
- Every file and folder has a Doscetionary Access Conrol List
**Each DACL contains multipl Access Control Entries like**:
```
Brayden : Full Control
Jane: Read and Execute
Guest: Deny Write
```
#### NTFS vs. Share Permissions

| Feature     | NTFS Permissions      | Share Permisions      |
| :---------- | :-------------------- | :-------------------- |
| Stored on   | Local disk(NTFS)      | Network share (SMB)   |
| Applies to  | Local + remote access | Remote access only    |
| Granularity | File/folder level     | Folder level only     |
| Priority    | Most restrictive wins | Most restrictive wins |
Effective Access = NTFS and Share Permissions
#### Permission Levels

| Level                | Includes                                  |
| -------------------- | ----------------------------------------- |
| Full Control         | Everything (including permissions change) |
| Modify               | Read, write, delete                       |
| Read & Execute       | Open and run files                        |
| List Folder Contents | View Folders                              |
| Read                 | Open, copy                                |
| Write                | Create, modify                            |
#### Deny vs Allow
**Explicit Deny always overrides Allow, unless Allow is higher in hierarchy**
#### Inheritance
- NTFS permissions trickle down from parent folders
- Chold folders/files inherit parent ACEs by default
- You can break inheritance to mutually set custom rules
	### To break: 
	```Right-click folder > Properties > Security > Advanced > Disable Inheritance```
#### Effective Access
- To determine what a user actually can do, combine:
	- All groups they belong to
	- NTFS ACEs (allow + deny)
	- Share permissions
- Use tool:
	`Right-click folder > Properties > Securities > Advanced > Effective Access`
- Or in PowerShell:
	`Get-Acl "C:\Folder" | Format-List`
#### Blue Team / Help Desk Use Cases

| Problem                            | Likely Cause                        |
| ---------------------------------- | ----------------------------------- |
| User can see folder but not file   | NTFS file-level permissions         |
| Share is visible but access denied | NTFS denies or no read              |
| Deleted data nobody adnits to      | Modify access too broad             |
| Lateral movement on share          | Everyone has write to tools/scripts |
| File server compromise             | Admin shares + Full Control = abuse |
#### Red Flags
- "Everyone" group with Full Control
- Authenticated Users with Write/Modify
- Legacy shares that never removed inheritance
- Deny used inconsistently
- Inconsistent ACLs on sensitive folders (HR, payroll, backups)