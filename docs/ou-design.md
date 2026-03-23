# Organizational Unit (OU) Design

**Project:** Active Directory Enterprise Lab  
**Focus Area:** Directory Structure & Administration  

---

## What This Is

Organizational Units (OUs) are containers within Active Directory used to group users, computers, and other directory objects in a structured way.

---

## Why It Matters

- Improves directory organisation and manageability  
- Allows policies to be applied to specific groups of users or devices  
- Makes delegation and administration easier  
- Reflects how departments and roles are separated in real environments  

---

## Where It Fits in This Project

In this lab, OUs were used to structure the environment in a way that resembles a small enterprise setup, separating users, computers, and departments for easier management and policy application.

---

## How It Was Implemented

- Created top-level OUs for major object categories such as Users, Computers, and Groups  
- Created department-based OUs beneath them where needed  
- Moved relevant users and machines into their respective OUs  
- Used the OU structure as the basis for Group Policy targeting and administrative organisation  

---

## Example Structure

A simplified structure used in the lab:

```
Ranya.local
├── Users
│   ├── IT
│   ├── HR
│   ├── Sales
│   └── Marketing
│
├── Computers
├── Workstations
├── Servers
└── Groups
│   ├── Global
|   |    ├── GG_IT
│   |    ├── GG_HR
│   |    ├── GG_Marketing  
|   |    ├── GG_Sales
|   |
│   ├── Domain Local
│        ├── DL_IT
│        ├── DL_HR
│        ├── DL_Marketing  
|        ├── DL_Sales 

```

---

## Key Design Decisions

- Kept the OU structure simple and readable  
- Separated users from computers to make policy application cleaner  
- Used department-based OUs to reflect role separation  
- Avoided unnecessary nesting to keep administration straightforward  

---

## Validation / How I Tested It

- Verified that users and computers appeared in the correct OUs  
- Confirmed Group Policies were applied to the intended OUs  
- Checked that moving objects between OUs changed the policies applied to them  

---

## Common Pitfalls

- Overcomplicating the OU hierarchy  
- Using OUs as security boundaries instead of for organisation  
- Placing all users and machines in default containers  
- Creating deeply nested structures that are hard to manage  

---

## Key Takeaways

- Good OU design makes administration much easier  
- OU structure directly affects how GPOs are applied  
- Simple and logical design is usually better than excessive complexity  

---
