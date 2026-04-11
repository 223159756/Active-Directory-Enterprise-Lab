# Troubleshooting Domain Login Failure due to AD Screenshots/DNS Misconfiguration

## Overview

This Ticket Resolution documents a domain login issue encountered in a controlled Active Directory lab, where a newly created domain user was unable to authenticate on a Windows 11 client machine.

The focus here was resolving thr issue and understanding how domain authentication depends on AD Screenshots/DNS and network configuration, and how such problems appear from an IT support perspective.

This was performed in a lab environment, so while the behaviour reflects real-world scenarios, production environments would typically include additional layers such as endpoint security, and monitoring tools.

## Lab Setup
Domain Controller (Windows Server)
Windows 11 (Domain-joined client)
VirtualBox (NAT + Host-only network configuration)
Active Directory Domain: Ranya.local
Issue Encountered

A newly created domain user (HRJeffrey) was unable to log in to the Windows 11 client.

## Error observed:

“We can’t sign you in with this credential because your domain isn’t available”

<p align="center"> <img src="../../screenshots/AD Screenshots/DNS-Misconfiguration/Login-Issue.png" width="800"/> </p>

Interestingly, previously used accounts were still able to log in successfully.

## Initial Observation

The behaviour suggested:

- Existing users were logging in using cached credentials
- New users required live authentication with the Domain Controller
- The client machine was likely unable to consistently communicate with the domain

## Investigation
1. Network Configuration Check

The client machine was configured with multiple network adapters:

NAT (10.0.2.x)
Host-only (192.168.56.xx)

<p align="center"> <img src="../../screenshots/AD Screenshots/DNS-Misconfiguration/Checking-Network-Settings.png" width="800"/> </p>

This introduced multiple routing paths, potentially causing inconsistent communication with the Domain Controller.

2. AD Screenshots/DNS Configuration Analysis

Using:

ipconfig /all

It was observed that the client had mixed Network Configuration:

Internal AD Screenshots/DNS (Domain Controller)
External AD Screenshots/DNS (8.8.8.8)
<p align="center"> <img src="../../screenshots/AD Screenshots/DNS-Misconfiguration/Checking-Network-Settings.png" width="800"/> </p>

We removed the NAT setting from the system as that could potentially cause the system to pick the NAT IP while trying to authenticate and not have proper connection with the DC, relying on old Kerberos tickets to authenticate.

Next we login from one of the cached users and manually repositioned our DNS server to the DC to ensure that connection requests are routed to it. 
<p align="center"> <img src="../../screenshots/AD Screenshots/DNS-Misconfiguration/Manual-DNS-Configuration.png" width="800"/> </p>

3. Connectivity Validation

Basic tests confirmed that the Domain Controller was reachable:

ping 192.168.56.101
nslookup Ranya.local
nltest /dsgetdc:Ranya.local
<p align="center"> <img src="../../screenshots/AD Screenshots/DNS-Misconfiguration/Confirm-DNS-Configuration.png" width="800"/> </p>

## Root Cause

The issue was caused by:

Multiple active network adapters (NAT + Host-only) creating inconsistent routing
Presence of external DNS Misconfiguration (using global 8.8.8.8 and host router 192.168.1.8 as DNS servers) interfering with Active Directory name resolution

This prevented the client from reliably locating the Domain Controller during first-time authentication.

## Resolution

The issue was resolved by simplifying the network configuration:

Disabled the NAT adapter to remove conflicting routes
Ensured both client and Domain Controller were on the same Host-only network (192.168.56.x)
Configured AD Screenshots/DNS manually to point only to the Domain Controller
<p align="center"> <img src="../../screenshots/AD Screenshots/DNS-Misconfiguration/Manual-DNS-Configuration.png" width="800"/> </p>

After applying these changes, the new user was able to log in successfully.

<p align="center"> <img src="../../screenshots/AD Screenshots/DNS-Misconfiguration/New-User-Login-Success.png" width="800"/> </p>

## Conclusion

At this stage, the following was established:

The issue was not related to the user account itself
Cached credentials allowed existing users to log in
New user authentication required live communication with the Domain Controller
AD Screenshots/DNS misconfiguration disrupted domain controller discovery

This aligns with common Active Directory login failures caused by DNS Misconfiguration inconsistencies.

## Final Note

This exercise highlights a common IT support scenario where login issues are not caused by user accounts, but by underlying network and AD Screenshots/DNS configuration.

The focus here was on identifying the root cause through structured troubleshooting rather than applying quick fixes.