# Troubleshooting Group Policy Not Applying: Desktop Wallpaper Policy

## Overview

This ticket documents a Group Policy issue where a desktop wallpaper policy was not applying to a domain user's workstation, despite the GPO being active within the domain.

The investigation focused on understanding how GPO links and OU placement interact, and why a policy that appears correctly configured can still be silently skipped for certain users.

This was performed in a lab environment. Production environments would typically include additional monitoring and alerting tools.

## Lab Setup

Domain Controller (Windows Server 2022)
Windows 11 (Domain-joined client, COMPUTER1)
VirtualBox (NAT + Host-only network configuration)
Active Directory Domain: Ranya.local

## Issue Encountered

HaroldJones reported that the desktop wallpaper policy was not applying to his workstation. The desktop was showing a plain black background rather than the company wallpaper that had been configured through Group Policy.

<p align="center"> <img src="../../screenshots/AD Screenshots/GPO Not Applied (Wallpaper)/02-workstation-wallpaper-not-applied.png" width="800"/> </p>

The GPO had been created and the wallpaper file was stored on a shared network path accessible from the workstation. No error was displayed on the client side.

## Initial Observation

The behaviour pointed to one of three possible causes:

- The GPO was not linked to the OU where the user account was located
- The user was excluded by the security filtering on the GPO
- The wallpaper path referenced in the GPO was unreachable from the client at logon time

## Investigation

### 1. GPO Configuration Review

The Desktop Wallpaper GPO was opened in Group Policy Management Console on the Domain Controller. The Scope tab showed the GPO was linked to the `#Sales` OU within `Ranya.local/Australia/Users`. Security filtering was set to Authenticated Users and the Sales security group.

<p align="center"> <img src="../../screenshots/AD Screenshots/GPO Not Applied (Wallpaper)/01-gpmc-desktop-wallpaper-scope.png" width="800"/> </p>

### 2. gpresult Output on Workstation

Running `gpresult /r` on COMPUTER1 as HaroldJones confirmed that the Desktop Wallpaper GPO was not present in the Applied Group Policy Objects list. The output also showed the user's current location in the directory:

```
CN=Harold Jones,OU=\#Marketing,OU=Users,OU=Australia,DC=Ranya,DC=local
```

<p align="center"> <img src="../../screenshots/AD Screenshots/GPO Not Applied (Wallpaper)/03-gpresult-gpo-not-applied.png" width="800"/> </p>

HaroldJones was in the `#Marketing` OU. Because Group Policy applies based on where the user object sits in the directory tree, and the GPO was only linked to `#Sales`, it had no scope over `#Marketing` and was never delivered to HaroldJones at logon.

### 3. Confirming the Mismatch

Reviewing ADUC alongside GPMC confirmed the issue. The GPO was linked to `#Sales`, and HaroldJones' account was sitting inside `#Marketing`.

<p align="center"> <img src="../../screenshots/AD Screenshots/GPO Not Applied (Wallpaper)/04-gpmc-aduc-ou-mismatch.png" width="800"/> </p>

This is a common configuration mistake when a GPO is originally created for one team and later needs to cover additional OUs without the link being updated.

## Root Cause

The Desktop Wallpaper GPO was linked only to the `#Sales` OU. HaroldJones' user account was located in the `#Marketing` OU. Group Policy does not apply across OU boundaries unless the GPO is explicitly linked to the relevant OU or to a parent OU that covers both. Because no link existed for `#Marketing`, the policy was silently skipped during logon with no error presented to the user.

## Resolution

The GPO was linked to the `#Marketing` OU in Group Policy Management Console:

```
Right-click #Marketing OU → Link an Existing GPO → Desktop Wallpaper
```

<p align="center"> <img src="../../screenshots/AD Screenshots/GPO Not Applied (Wallpaper)/05-gpmc-gpo-linked-to-marketing.png" width="800"/> </p>

`gpupdate /force` was run on COMPUTER1, followed by a full logoff and logon. The desktop wallpaper updated on the next login.

<p align="center"> <img src="../../screenshots/AD Screenshots/GPO Not Applied (Wallpaper)/06-workstation-wallpaper-applied.png" width="800"/> </p>

## Conclusion

- The Desktop Wallpaper GPO was correctly configured but linked only to `#Sales`
- HaroldJones' account was in `#Marketing`, which was outside the GPO's scope
- `gpresult /r` identified the missing policy and showed the user's OU location, which was enough to pinpoint the cause
- Linking the GPO to `#Marketing` resolved the issue

Group Policy applies based on where objects sit in the directory tree. A GPO can be active and correctly configured but still miss users entirely if they are in an OU not covered by any of its links.

## Final Note

`gpresult /r` is the most reliable first step for GPO issues. It shows which policies were applied, which were filtered out, and where the user object sits in the directory. In most cases that is enough to find the problem without needing to dig through GPMC manually.

User-based Group Policy settings, including desktop wallpaper, require a full logoff and logon to take effect. Running `gpupdate /force` refreshes the policy in the background but does not re-apply user configuration settings mid-session.
