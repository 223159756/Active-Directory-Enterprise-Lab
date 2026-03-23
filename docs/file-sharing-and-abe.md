# File Sharing & Access-Based Enumeration (ABE)

**Project:** Active Directory Enterprise Lab  
**Focus Area:** File Services & Access Control  

---

## What This Is

File sharing in Active Directory allows users to access shared resources over the network, while Access Based Enumeration (ABE) ensures that users can only see files and folders they have permission to access.

---

## Why It Matters

- Enables centralized file access across an organisation  
- Prevents unauthorized visibility of sensitive resources  
- Improves user experience by hiding irrelevant folders  
- Supports role based access control in shared environments  

---

## Where It Fits in This Project

In this lab, file sharing was used to simulate a centralised file server, while ABE was implemented to ensure users only see resources relevant to their roles.

---

## How It Was Implemented

- Configured a shared folder on the Domain Controller / file server  
- Enabled sharing with appropriate permissions  
- Applied NTFS permissions based on user groups  
- Enabled Access Based Enumeration on the shared folder  
- Integrated access control using AGDLP group structure  

---

## Key Configurations

- **Shared Folder Setup**
  - Created shared directory   
  - Enabled network sharing  

- **Permissions**
  - Assigned access via Domain Local Groups  
  - Controlled permissions using NTFS security settings  

- **Access-Based Enumeration**
  - Enabled ABE to hide inaccessible folders  
  - Ensured users only view authorized resources  

---

## Validation / How I Tested It

- Logged in as different users from separate groups  
- Accessed shared folder via network path  
- Verified visibility of only permitted folders  
- Confirmed restricted access to unauthorized directories  

---

## Common Pitfalls

- Misconfigured NTFS vs share permissions  
- Assigning permissions directly to users instead of groups  
- Forgetting to enable ABE  
- Over permissive access (e.g., Everyone has full control)  

---

## Key Takeaways

- Combining NTFS permissions with group-based access is critical  
- ABE significantly improves both security and usability  
- Proper permission structure prevents data exposure  

---
