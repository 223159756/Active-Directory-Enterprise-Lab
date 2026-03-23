# Group Policy Configuration

**Project:** Active Directory Enterprise Lab  
**Focus Area:** Security & Policy Enforcement  

---

## What are GPO's?

Group Policy Objects (GPOs) are used in Active Directory to centrally manage and enforce configurations across users and computers within a domain. 
It is an easy way to manage user/computer/server permissions, access, configuration ensuring correct network annd security segmentation and ensuring uniformity. 

---

## Why It Matters

- Enforces security policies across all machines  
- Standardises configurations in enterprise environments  
- Reduces manual configuration on individual systems  
- Enables centralized control over user and system behaviour  

---

## Where It Fits in This Project

In this lab, GPOs were used to simulate enterprise catered security enforcement by applying consistent policies across domain joined machines and users. 

---

## How It Was Implemented

- Accessed Group Policy Management Console on the Domain Controller  
- Created and linked GPOs to appropriate Organizational Units   
- Configured policies under Computer Configuration and User Configuration  
- Applied policies to client machines  

---

## Key Configurations

Implemented the following policies:

- **Password Policy**
  - Enforced minimum password length  
  - Enabled complexity requirements  

- **Account Lockout Policy**
  - Defined lockout threshold  
  - Set lockout duration  

- **System Restrictions**
  - Restricted access to control panel / settings  
  - Enforced desktop configurations like common wallpaper

- **Audit Account Management Policy**
  - Defined Account Auditing during Logon/Logoff  
  - Enabled Audit on Credential Verification
  - Enabled Audit on any change in policy

- **Removable Storage Policy**
  - Denied any Removable storage device on any computers

- **Fine Grained Passwords Policy**
  - Enabled Password Policies for Administrators

- **Drive Mapping Policy**
  - Enabled Common Drive Mappings for all Users

---

## Validation / How I Tested It

- Logged in with domain user accounts  
- Attempted actions restricted by policies  
- Verified password policy enforcement during account creation  
- Confirmed policies were applied using `gpresult /r`  

---

## Common Pitfalls

- GPO not linked to the correct OU  
- Delay in policy application (requires `gpupdate /force`)  
- Conflicts between multiple GPOs  
- Editing Default Domain Policy unnecessarily  

---

## Key Takeaways

- GPOs provide powerful centralized control over systems  
- Proper OU structure is essential for effective policy application  
- Testing policies in a lab environment is critical before production use  

---

