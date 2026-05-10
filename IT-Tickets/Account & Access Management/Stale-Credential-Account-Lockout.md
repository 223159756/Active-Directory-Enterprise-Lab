# Troubleshooting Repeated Account Lockout due to Stale Saved Credential

## Overview

This Ticket Resolution documents a repeated account lockout issue encountered in a controlled Active Directory lab, where a domain user was being locked out continuously — even after the account was unlocked — without any apparent login attempts by the user.

The focus here was identifying the root cause through structured investigation and understanding how saved credentials in Windows Credential Manager interact with domain authentication, and how such problems appear from an IT support perspective.

This was performed in a lab environment, so while the behaviour reflects real-world scenarios, production environments would typically include additional layers such as endpoint security and monitoring tools.

## Lab Setup

Domain Controller (Windows Server 2022)
Windows 11 (Domain-joined client — COMPUTER1)
VirtualBox (NAT + Host-only network configuration)
Active Directory Domain: Ranya.local

## Issue Encountered

A domain user (HaroldJones) reported being unable to stay logged in — their account kept locking out shortly after being unlocked by the helpdesk.

The user confirmed they had not mistyped their password.

<p align="center"> <img src="../../screenshots/AD Screenshots/Account Management/01-aduc-haroldJones-user-account.png" width="800"/> </p>

Interestingly, no active login sessions were observed for the user at the time of the lockouts.

## Initial Observation

The behaviour suggested:

- The account was being locked by repeated failed authentication attempts
- The attempts were occurring silently in the background, not from a user-initiated login
- Something on the network was continuously retrying with incorrect credentials

## Investigation

### 1. Shared Resource Context

A shared folder (CorpShare) had previously been set up on the Domain Controller and made accessible to domain users.

<p align="center"> <img src="../../screenshots/AD Screenshots/Account Management/02-corpshare-share-permissions.png" width="800"/> </p>

HaroldJones had previously mapped this share as a persistent network drive using their credentials.

<p align="center"> <img src="../../screenshots/AD Screenshots/Account Management/03-drive-mapped-old-credential.png" width="800"/> </p>

### 2. Password Change Event

A password reset had been performed on HaroldJones' account on the Domain Controller shortly before the lockouts began.

Using:

```
Set-ADAccountPassword -Identity "HaroldJones" -NewPassword (...) -Reset
```

<p align="center"> <img src="../../screenshots/AD Screenshots/Account Management/04-dc-password-reset-haroldJones.png" width="800"/> </p>

This meant the credentials saved on COMPUTER1 were now out of date.

### 3. Credential Manager Analysis

Logging into COMPUTER1 and opening Windows Credential Manager revealed a saved entry for the domain using HaroldJones' old password.

```
Control Panel → User Accounts → Credential Manager → Windows Credentials
```

<p align="center"> <img src="../../screenshots/AD Screenshots/Account Management/05-credential-manager-stale-entry.png" width="800"/> </p>

This stale entry was silently retrying against the Domain Controller each time a connection to the share was attempted, burning through the account lockout threshold.

### 4. Event Log Analysis

Filtering the Security log on the Domain Controller for Event ID 4771 confirmed repeated Kerberos pre-authentication failures originating from COMPUTER1.

<p align="center"> <img src="../../screenshots/AD Screenshots/Account Management/06-event-viewer-4771-kerberos-failure.png" width="800"/> </p>

The failure code 0x18 confirmed the cause was an incorrect password, not an expired account or policy restriction.

### 5. ADUC — Bad Password Count

Checking HaroldJones' properties in Active Directory Users and Computers (with Advanced Features enabled) confirmed the account had accumulated a high bad password count, consistent with repeated automated retries.

<p align="center"> <img src="../../screenshots/AD Screenshots/Account Management/07-aduc-bad-password-count-locked.png" width="800"/> </p>

## Root Cause

The issue was caused by:

A persistent network drive mapped on COMPUTER1 using HaroldJones' previous password
A password reset performed on the domain account without removing or updating the saved credential on the workstation
Windows Credential Manager silently retrying the stale password against the Domain Controller on each connection attempt, triggering the account lockout policy

This prevented HaroldJones from staying logged in despite no deliberate login attempts being made.

## Resolution

The issue was resolved by removing the stale credential and unlocking the account:

Removed the outdated entry from Windows Credential Manager on COMPUTER1
Unlocked the account on the Domain Controller using:

```
Unlock-ADAccount -Identity "HaroldJones"
```

<p align="center"> <img src="../../screenshots/AD Screenshots/Account Management/08-aduc-account-unlocked-powershell.png" width="800"/> </p>

HaroldJones was then able to log in successfully using the new password, and no further lockouts occurred.

## Conclusion

At this stage, the following was established:

- The issue was not caused by the user mistyping their password
- A stale saved credential in Windows Credential Manager was silently retrying against the Domain Controller
- Event ID 4771 on the DC confirmed the source machine and failure reason
- The badPwdCount attribute in ADUC confirmed the account was being hit repeatedly

This aligns with a common Active Directory support scenario where account lockouts are caused not by the user, but by saved credentials that were never updated after a password change.

## Final Note

This exercise highlights an important IT support pattern — repeated lockouts that resume shortly after unlocking are almost always caused by a background process or saved credential, not the user themselves.

The focus here was on identifying the actual source of the authentication attempts through Event Viewer and Credential Manager rather than simply unlocking the account and closing the ticket.
