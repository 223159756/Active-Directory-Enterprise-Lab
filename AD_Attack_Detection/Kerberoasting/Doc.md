# Kerberoasting Detection in Active Directory using Splunk

## Overview

This project simulates a Kerberoasting attack in a controlled Active Directory lab and walks through how the activity can be identified using Splunk.

The goal here was to execute the kerberoasting technique, but more so to understand how it appears from a SOC perspective, What logs are generated, how to investigate them, and what basic remediation would look like.

This was performed in a lab environment, so while the concepts reflect real-world scenarios, actual enterprise environments would involve more complexity, scale, and noise.

---

## Lab Setup

- Domain Controller (Windows Server)
- Windows 11 (Domain-joined)
- Kali Linux (attacker machine)
- Splunk (running on host)
- Sysmon enabled for additional visibility

---

## Attack Simulation

From the Kali machine, Impacket was used with compromised domain user credentials (`HRHindocha`) to request Kerberos service tickets for a service account (`SVQ_SQL`) that had an SPN configured.

This mimics a typical Kerberoasting scenario where an attacker, already in possession of valid domain credentials, targets service accounts to extract ticket hashes for offline cracking.

![Impacket Execution](images/impacket_command.png)

---

## Initial Detection (Event ID 4769)

The first step in detection was identifying Kerberos service ticket activity in Splunk.

Query used:


index=* EventCode=4769
| sort - _time


This highlights all Kerberos TGS requests.

![4769 Query](images/query1.png)  
![4769 Results](images/query1_result.png)

From here, the account `HRHindocha` stood out as part of the activity.

---

## Investigation – Source Analysis

Next, activity from that specific user was isolated to understand where the requests were coming from.

Query used:


index=* Account_Name="HRHindocha"
| stats count by Client_Address
| sort count


![User Activity Query](images/query2.png)  
![User Activity Results](images/query2_result.png)

One IP address showed a very limited number of events, which made it stand out compared to normal background activity.

---

## Validation – Encryption Type

To confirm whether this aligned with Kerberoasting behaviour, the encryption type of service tickets was analysed.

Query used:


index=* EventCode=4769
| stats count by Ticket_Encryption_Type


![Encryption Query](images/query3.png)  
![Encryption Results](images/query3_result.png)

Most tickets used AES (`0x12`), which is expected in modern environments.

However, there was a single instance of:

0x17 → RC4


This is significant because RC4 is commonly associated with Kerberoasting activity due to its weaker encryption.

The source IP tied to this RC4 request matched the earlier suspicious IP, confirming the behaviour.

---

## Conclusion of Detection

At this point, the following was established:

- A domain user (`HRHindocha`) requested service tickets
- A specific IP generated an RC4-encrypted TGS request
- This behaviour aligned with Kerberoasting patterns

This allowed us to reasonably conclude that Kerberoasting activity had been emulated.

---

## Remediation Actions

Immediate containment was performed by disabling the compromised account:

```powershell
Disable-ADAccount -Identity HRHindocha

In a real-world environment, this would be followed by:

Password reset and credential rotation
Endpoint isolation of the source system
Investigation into lateral movement
Hardening of service accounts (strong passwords, enforcing AES-only encryption for all service accounts)
Escalation to incident response teams

Key Takeaways
Event ID 4769 is central to detecting Kerberos service ticket activity
Kerberoasting detection relies on patterns, not just single events
RC4 (0x17) can be a strong indicator, but not always present
Investigating who, where from, and how often is critical
Even simple log analysis can reveal meaningful signals in a controlled setup

Final Note

This project was completed in a lab to understand detection workflows. Real environments would involve significantly more noise, stricter controls, and more advanced detection mechanisms.

The focus here was on building a foundational understanding of how Kerberos abuse appears from a SOC perspective.