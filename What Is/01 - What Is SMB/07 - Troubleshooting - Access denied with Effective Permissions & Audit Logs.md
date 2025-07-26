
> [!NOTE] Objective
> - Why permissions look fine but access still fails
> - How to calculate **Effective Permissions** across NTFS, groups, inheiritance, and shares
> - How to enable **auditing logs** to catch permission failures
> - Real tools abnd commands to diagnose access issues like a Tier 2/ Blue Teamer
> - How this maps to insider threat detection and privilege abuse hunting
### Deep Dive: Effective Permissions
- Windows evaluates all layers of access control

| Layer                 | Examples                       |
| --------------------- | ------------------------------ |
| Share Permissions     | Read, Change, Full Control     |
| NTFS Permissions      | DACLs with Allow/Deny ACEs     |
| User Group Membership | Domain Users, HR, Accounting   |
| Inheritance Rules     | From parent folders            |
| Token SID Matching    | SID of user must match ACE SID |
### Rules of Access
1. Explicit Deny overrides everything
2. Most restrictive combination wins
3. Inheritance applies unless manually broken
4. Membership in multiple groups compounds permissions
5. Final Decision is based on Effective Access calculation
### How to Check Effective Permissions (GUI)
1. Right-Click folder > Properties > Security > Advanced
2. Select "Effective Access" tab
3. Select user or group
4. Review what permissions they actually have
### Powershell: Check Permissions
- View ACL:
	`Get-Acl "C:\SensitiveFolder" | Format-List`
- See user SID:
	`whoami /user`
### Troubleshooting Access Denied - Checklist

| Step                     | Action                                  |
| ------------------------ | --------------------------------------- |
| Check NTFS permissions   | Right-click > Security tab              |
| Check Share permissions  | Advanced tab                            |
| Run `whoami /groups`     | Confirm group memebership               |
| Use Effective Access tab | Simulate real result                    |
| Enable Auditing          | Watch 46633, 4656 logs                  |
| Use `Get-SmbSession`     | Confirm SID matches user Identity       |
| Clear cached creds       | `cmdkey /delete` or `net use * /delete` |
### Red team implications
- Attackers look for "Everyone: Full Control" folders
- Lateral movement often fails due to hidden "Deny" ACEs
- Exploits use audit failures to blend in with noisy events
- Red teams often map access manually before dropping payloads
### Blue Team Use Cases
- Audit sensitive shares for unauthorized **write/delete attempts**
- Flag Deny ACEs that override group logic
- Watch for attackers exploiting misconfigured inheritance
- Track user activity over time via 4663 + 5140 correlation