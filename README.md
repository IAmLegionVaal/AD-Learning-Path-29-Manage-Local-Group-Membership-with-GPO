# AD Learning Path 29 — Manage Local Group Membership with GPO

## Objective
Control the approved local Administrators membership on lab workstations through Group Policy while preserving a tested recovery path.

## Prerequisites
- Workstations OU and test client
- Dedicated domain support group
- Group Policy Management
- Existing local membership recorded

## Setup
1. Create `GG-Workstation-Local-Admins-Lab` and add only approved lab operators.
2. Create a workstation GPO.
3. Under Computer Configuration → Preferences → Control Panel Settings → Local Users and Groups, create an **Update** action for the built-in Administrators group.
4. Add the domain support group without removing required built-in entries.
5. Link the GPO only to the Workstations OU.
6. Refresh policy and inspect the resulting local membership.

```powershell
New-ADGroup -Name 'GG-Workstation-Local-Admins-Lab' `
    -GroupScope Global -GroupCategory Security `
    -Path 'OU=Groups,DC=corp,DC=lab' `
    -Description 'Approved lab workstation support operators'
```

## Validation
On `CL01`:
```powershell
gpupdate.exe /force
Get-LocalGroupMember -Group Administrators
gpresult.exe /scope computer /r
Get-WinEvent -LogName 'Microsoft-Windows-GroupPolicy/Operational' -MaxEvents 30
```

## Evidence
Store the GPO item, link/filtering, local group before/after, `gpresult`, Group Policy events, recovery-path test, troubleshooting, and final pass/fail status under `evidence/`.

## Troubleshooting
- Required members disappear: review whether a replacement-style configuration was used and restore the recorded baseline.
- Domain group not resolved: verify DNS, domain connectivity, and the exact group name.
- Inconsistent application: inspect OU placement, policy precedence, and filters.

## Security notes
Local administrator rights are high impact. Use a dedicated support group, Windows LAPS, separate admin identities, and regular membership reviews.

## Cleanup
Disable or remove the preference item and explicitly restore the approved local membership baseline.

## References
- Microsoft Learn: Group Policy Preferences Local Users and Groups
- Microsoft Learn: Restricted Groups

## Next activity
`AD-Learning-Path-30-Map-Network-Drives-with-Group-Policy`
