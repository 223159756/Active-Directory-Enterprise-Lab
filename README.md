# Active-Directory-Enterprise-Lab
This is a Homelab where I experiment with Active Directory on a Windows server VM as my DC and multiple Windows 11 Enterprise VM's as my Clients. All the work is a representation of what User management would appear in a small/medium enterprise and how the organizational structure would appear. 

The focus was building something that actually behaves like a real syste, with clear segmentation through departments, controlled access to resources, enforced policies, and validation of how everything works in practice.

What’s in the environment
A domain built on Windows Server 2022 with Windows 11 Enterprise clients
Organisational Units structured by geography and departments
Department-based users (HR, IT, Sales etc.) with proper grouping
Registered Computers, Servers and Service Accounts for static presentation
Role-based access control using the AGDLP model
Central file sharing with NTFS permissions and Access-Based Enumeration (ABE)
Mapped drives deployed through Group Policy
Multiple GPOs for security, restrictions, and standardisation
Audit logging enabled and tested through Event Viewer

How access is handled:

Access isn’t given directly to users. Everything flows through groups.

Users are placed into department-based global groups, those groups are linked to folder access groups, and those access groups are what actually get permissions on resources.

This keeps permissions clean, scalable, and easy to manage as the environment grows.

File sharing approach:

All shared resources sit under a central network share and are segmented by department.

Access is controlled using NTFS permissions, while ABE ensures users only see the folders they’re allowed to access. Drives are mapped automatically at login so users don’t need to manually navigate network paths.

What I tested:

I made sure the setup actually works in keeping users seperate.

Users can access only their department folders
Cross-department access is denied
Mapped drives appear correctly on login
Account lockout triggers after failed attempts
Policies apply as expected
Security logs capture authentication and access activity

Screenshots of these are included in the repo.

Why this matters:

This lab is essentially the foundation of enterprise identity and access management.

It ties together:

how users are organised
how access is granted
how policies are enforced
how behaviour is monitored

What’s next?

This is just the base layer. Next steps include:

expanding into DNS/DHCP and network-level design
adding centralised logging / SIEM
simulating attack scenarios and detection
implementing stronger endpoint and identity security controls
exploring hybrid identity with cloud integration (Especially with my AWS Projects which I hope to intersect into something awesome:) 

Notes

Detailed breakdowns of each component (OU design, AGDLP, GPOs, file sharing, validation) are in the /docs folder.

Thank you for your time!