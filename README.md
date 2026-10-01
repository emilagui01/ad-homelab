# Windows Server 2022 Active Directory Home Lab

*Home Lab 1 of 3 · **Complete** · [view all labs](https://emilagui01.github.io/#labs)*

A virtualized Windows domain built in Hyper-V to practice real help desk and sysadmin work: domain controller setup, DNS, DHCP, organizational units, user accounts, Group Policy, account troubleshooting, and joining a Windows 11 client to the domain.

**Live writeup:** https://emilagui01.github.io/ad-homelab/
**Portfolio:** https://emilagui01.github.io/#labs

## Environment

| Component | Details |
|---|---|
| Hypervisor | Hyper-V on Windows 11 Pro (32 GB RAM) |
| Domain controller | DC01, Windows Server 2022 Evaluation, 192.168.50.10 (static) |
| Client | CLIENT01, Windows 11 Enterprise Evaluation, DHCP |
| Domain | emillab.local |
| Network | Internal switch `LabSwitch` + NAT, 192.168.50.0/24 |

## What's built

- [x] Hyper-V internal switch with NAT for an isolated lab subnet
- [x] Domain controller with AD DS and integrated DNS
- [x] DNS forwarder for internet name resolution
- [x] DHCP server authorized in AD with a scope for clients
- [x] Lab Users and Lab Computers OUs with three domain users
- [x] Windows 11 client (virtual TPM) joined to the domain and signed in as a domain user
- [x] Lab Staff security group and a file share with share + NTFS permissions
- [x] Group Policy: S: drive map, Control Panel restriction, Remote Desktop rights for Lab Staff
- [x] Account troubleshooting: four help desk tickets resolved end to end

## Home lab series

| # | Lab | Status |
|---|---|---|
| 1 | Windows Server 2022 Active Directory (this repo) | Complete |
| 2 | [Help Desk Ticketing System](https://emilagui01.github.io/helpdesk-lab/) | Complete |
| 3 | [PowerShell Automation](https://emilagui01.github.io/powershell-automation-lab/) | Complete |

## Help desk tickets

| Ticket | User | Issue | Diagnosis | Fix |
|---|---|---|---|---|
| INC-0001 | jdoe | Account locked out | `Get-ADUser` LockedOut = True; event 4740 named CLIENT01 as the source | `Unlock-ADAccount` |
| INC-0002 | pparker | Forgotten password | No lockout or disabled flag | Temp password + `-ChangePasswordAtLogon` |
| INC-0003 | jsmith | Account disabled | `Search-ADAccount -AccountDisabled` | `Enable-ADAccount` |
| INC-0004 | pparker | Policies stopped applying | `gpresult /r` showed CN=Users instead of the Lab Users OU; S: drive persisted as a GP Preference | Moved user back to Lab Users |

## Troubleshooting highlights

| Problem | Root cause | Fix |
|---|---|---|
| "Account already exists" after a failed `New-ADUser` | Account object is created before the password is set, so a password complexity failure leaves a disabled account behind | `Set-ADAccountPassword -Reset` and `Enable-ADAccount` |
| `Get-ADComputer CLIENT01` not found after domain join | Hyper-V VM name differs from the Windows computer name (auto-named DESKTOP-P26AUJ7) | Found with `Get-ADComputer -Filter *`, renamed with `Rename-Computer -DomainCredential` |
| Domain user blocked from signing in to the VM | Hyper-V Enhanced Session uses Remote Desktop, which standard users lack by default | Basic session as a workaround, then a GPO adding Lab Staff to Remote Desktop Users |
| RDP GPO applied but group stayed empty | Group name was typed instead of picking the built-in group from the dropdown | Reselected `Remote Desktop Users (built-in)` and resolved Lab Staff with the browse button |
| DC clock on the wrong time zone | Server defaulted to Pacific time; Kerberos depends on synced clocks | `Set-TimeZone -Id "Central Standard Time"` |

The full build steps, commands, and screenshots are on the [writeup page](https://emilagui01.github.io/ad-homelab/).

## Repo structure

```
index.html            Lab writeup (GitHub Pages)
README.md             This file
adds-forest.png       Promoting DC01 to a domain controller
get-addomain.png      Domain verification
aduc-users.png        Users in the Lab Users OU
aduc-computers.png    CLIENT01 in the Lab Computers OU
whoami.png            Domain user signed in on CLIENT01
gpmc-gpos.png         GPOs linked in Group Policy Management
gpresult-jdoe.png     User GPOs applied to jdoe
drive-restriction.png S: drive and Control Panel restriction
rdp-group.png         Lab Staff in Remote Desktop Users
t1-*.png … t4-*.png   Help desk ticket screenshots (INC-0001 to INC-0004)
```

---
Emil Aguirre Herrera · CompTIA Network+ · Microsoft Azure Fundamentals (AZ-900)
