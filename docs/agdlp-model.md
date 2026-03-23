# AGDLP Model (Access Control Strategy)

**Project:** Active Directory Enterprise Lab  
**Focus Area:** Identity & Access Management  

---

## What This Is

AGDLP is a Microsoft suggested access control model used in Active Directory to manage permissions in a structured and scalable way.

It stands for Accounts Globals Groups, Domain Local Groups , Permissions 

and it is the permision flow recommended my Microsoft for Enterprise AD enviroments. 


---

## Why It Matters

- Simplifies permission management in large environments  
- Reduces direct permission assignments to users  
- Makes access control scalable and easier to audit  
- Helps enforce role-based access control (RBAC)  

---

## Where It Fits in This Project

In this lab, the AGDLP model was used to simulate a real enterprise structure where users are assigned roles through groups rather than being given direct access to resources.

This ensured a clean separation between **user identities**, **roles**, and **resource permissions**.

---

## How It Was Implemented

- Created user accounts representing different departments (e.g., HR, IT, Sales)
- Created **Global Groups** to represent roles (e.g., HR_Users, IT_Admins)
- Created **Domain Local Groups** for resource access (e.g., DL_IT_Folder_Access)
- Assigned permissions to Domain Local Groups
- Added Global Groups into Domain Local Groups
- Added user accounts into Global Groups

---

## Structure Used

User -> Global Group -> Domain Local Group -> Resource

Example implementation:

Aditya -> IT_Admins -> ServerAccess -> File Server

---

## Key Configurations

- Users were not assigned permissions directly  
- All access was routed through group memberships  
- Permissions were applied only at the Domain Local Group level  

---

## Validation / How I Tested It

- Logged into a client machine using different user accounts  
- Verified access to shared resources based on group membership  
- Confirmed that removing a user from a group revoked access immediately  

---

## Common Pitfalls

- Assigning permissions directly to users instead of groups  
- Mixing up Global vs Domain Local group usage  
- Overcomplicating group nesting  

---

## Key Takeaways

- AGDLP enforces clean and scalable access control  
- Group-based access is far easier to manage than user-based permissions  
- Proper structure becomes critical as environments scale  

---

## Related Concepts

- Role-Based Access Control (RBAC)  
- Active Directory Group Types  
- NTFS Permissions  


