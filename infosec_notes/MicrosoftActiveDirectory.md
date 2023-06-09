# Microsoft Active Directory (Port...)

- Windows Domain: simply put, a group of users and computers under the administration of a given business
- main idea: centralize administration of common components of Windows Computer network in a single repository called **Active Directory**
- advantages:
  - Centralized Identity Management: all users across network can be configured from AD with minimum effort
  - Centralized Security Policy Management; policies can be applied upon users/computers acress network as needed
- server that runs AD services is known as Domain Controller

![DomainTryHackMe](img/2023-06-08-16-53-32.png)

- core of any Windows Domain is **Active Directory Domain Service**
  - acts as catalogue that holds information of all objects that exist on the network
    - users, groups, machines, printers, shares etc.
- **Security Groups**

![ADSecurityGroups](img/2023-06-08-18-53-14.png)

- **Organizational Units**

  - OUs are container objects that allow to classify users and machines
  - mainly used to define sets of users with similar policing requirements
  - a user can only be a part of a single OU at a time

- **Difference OU <--> Security Groups**

  - OUs: handy for applying policies to users and computers --> one user can only be a part of one OU
  - Security Groups: used to grant permissions over ressources e.g. shared folder or network printers --> one user can be apart of many groups (needed to grant access to multiple ressources)

- **Group Policy Objects (GPOs)**
  - configuration and security settings that can be applied to OUs
  - can contain policies aimed either at users or computers
  - configuration in **Group Policy Management** tool
  - any GPO will apply to the linked OU and any sub-OUs under it
  - GPOs are distributed to network via network share called `SYSVOL` which is stored in the Domain Controller
  - all users in domain should have access to this share over network to sync their GPOs periodically
  - this share points to `C:\Windows\SYSVOL\sysvol` directory on each of the Domain Controllers in the network
