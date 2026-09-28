# Windows Server 2022 Active Directory Home Lab

A virtualized Windows domain built in Hyper-V to practice real help desk and sysadmin work: domain controller setup, DNS, DHCP, organizational units, user accounts, and joining a Windows 11 client to the domain.

**Live writeup:** https://emilagui01.github.io/ad-homelab/
**Portfolio:** https://emilagui01.github.io/

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
- [ ] Group Policy: mapped drive, desktop restrictions, Remote Desktop rights by group
- [ ] Account troubleshooting scenarios: lockouts, password resets, disabled accounts
- [ ] PowerShell bulk user provisioning from CSV

## Troubleshooting highlights

| Problem | Root cause | Fix |
|---|---|---|
| "Account already exists" after a failed `New-ADUser` | Account object is created before the password is set, so a password complexity failure leaves a disabled account behind | `Set-ADAccountPassword -Reset` and `Enable-ADAccount` |
| `Get-ADComputer CLIENT01` not found after domain join | Hyper-V VM name differs from the Windows computer name (auto-named DESKTOP-P26AUJ7) | Found with `Get-ADComputer -Filter *`, renamed with `Rename-Computer -DomainCredential` |
| Domain user blocked from signing in to the VM | Hyper-V Enhanced Session uses Remote Desktop, which standard users lack by default | Basic session for now; Group Policy fix planned |
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
```

---
Emil Aguirre Herrera · CompTIA Network+ · Microsoft Azure Fundamentals (AZ-900)
