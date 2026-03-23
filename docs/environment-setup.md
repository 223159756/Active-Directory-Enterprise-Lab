# Environment Setup

**Project:** Active Directory Enterprise Lab  
**Focus Area:** Infrastructure & Virtualisation  

---

## What This Is

This section outlines the setup of the virtual environment used to simulate an enterprise Active Directory network.

---

## Why It Matters

- Provides a controlled environment for testing configurations  
- Gives a Template for anyone reading to recreate the enviroment
- Simulates a real world enterprise infrastructure  
- Allows safe experimentation without impacting production systems  

---

## Lab Architecture

The lab environment consists of:

- **Domain Controller:** Windows Server (AD DS, DNS)
- **Client Machine:** Windows 11 (Domain User)
- **Hypervisor:**  VirtualBox 
- **Network:** Internal / NAT network   
- **More to be Added** 

---

## How It Was Implemented

- Created virtual machines for server and client systems  
- Installed Windows Server 2022 and configured it as a Domain Controller  
- Installed Windows 11 Enterprise and joined it to the domain  
- Configured networking to allow communication between machines  

---

## Key Configurations

- Domain Name: `Ranya.local`  
- Static IP assigned to Domain Controller  
- DNS configured on Domain Controller  
- Client machine pointed to DC for DNS resolution  

---

## Validation / How I Tested It

- Verified domain controller promotion  
- Successfully joined client machine to domain  
- Logged in using domain credentials  
- Confirmed DNS resolution within the network  

---

## Common Pitfalls

- Incorrect DNS configuration preventing domain join
- Incorrect Windows VM Selection (Need Pro or Enterprise edition)
- Incorrect Windows Server  
- Not assigning a static IP to the Domain Controller  
- Network mode misconfiguration 

---

## Key Takeaways

- Proper networking is critical for Active Directory functionality  
- DNS plays a central role in domain operations  
- A clean lab setup makes troubleshooting much easier  

---

## Related Concepts

- Active Directory Domain Services (AD DS)  
- DNS in Active Directory  
- Virtualisation Networking  
